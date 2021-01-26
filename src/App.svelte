<script>
    import { onMount } from "svelte";
    import Chat from "./Chat.svelte";
    import Calendar from "./Calendar.svelte";

    let chat, chatButton, chatBox, newMsgIcon
    let chatOpen = false
    let calendarButton, calendar;
    let playButton, playIcon, tagline, logMessage;
    let events = [];

    onMount(() => {
        chatButton.onclick = () => {
            viewChatBox(true);
            chat.scrlBtm()
        };
        calendarButton.onclick = () => {
            viewCalendar(true);
        };

        playButton.onclick = () => {
            play();
        };

        updateCurrentEvent();
        updateCalendar();
        getOnlineUsers();
    });

    const viewChatBox = (show) => {
        if (show) {
            chatBox.style.display = "block";
            chatOpen = true
        }
        else {
            chatBox.style.display = "none";
            chatOpen = false
            newMsgIcon.style.display = "none"
        }
    };


    const viewCalendar = (show) => {
        if (show) calendar.style.display = "block";
        else calendar.style.display = "none";
    };

    //Firebase Config
    let firebaseConfig = {
        apiKey: "AIzaSyABNQF9ZiZTzmzYYZp9y0U1zmHmxThOseE",
        authDomain: "rajpathrecalls-15944.firebaseapp.com",
        databaseURL: "https://rajpathrecalls-15944.firebaseio.com",
        projectId: "rajpathrecalls-15944",
        storageBucket: "rajpathrecalls-15944.appspot.com",
        messagingSenderId: "912134912883",
        appId: "1:912134912883:web:d98ff319fabadb46a3a306",
        measurementId: "G-0LS86C0FPG",
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    firebase.analytics();

    const updateCurrentEvent = () => {
        firebase
            .database()
            .ref("CurEvent")
            .on("value", (val) => {
                if (val.val() != "unset") tagline.innerHTML = val.val();
            });
    };

    const updateCalendar = () => {
        firebase
            .database()
            .ref("Schedule/")
            .on("value", (sanapshot) => {
                let schedule = [];
                sanapshot.forEach((childsnap) => {
                    schedule.push(childsnap.val());
                });
                schedule.sort((a, b) => {
                    return a.time < b.time ? -1 : 1;
                });
                let notOverPrograms = [];
                for (let s of schedule) {
                    let programOverTime = s.time + parseInt(s.dur) * 60 * 1000;
                    if (Date.now() < programOverTime) notOverPrograms.push(s);
                }
                events = notOverPrograms;
            });
    };

    const getOnlineUsers = () => {
        firebase
            .database()
            .ref("ActiveUsers/")
            .on("value", (sanapshot) => {
                let onlineUsers = 0;
                sanapshot.forEach((childsnap) => {
                    onlineUsers++;
                });
                console.log("Users Online : " + onlineUsers);
            });
    };
    let ref = firebase.database().ref("ActiveUsers/").push({
        platform: "web-github.io",
    });
    ref.onDisconnect().remove();

    //player - copy pasted from pre module

    let player = null;
    let oldplayer = null;
    let lagging = 0;
    let playerpausedat;
    let links;

    //Firebase call to links

    firebase
        .database()
        .ref("Links/")
        .on("value", (sanapshot) => {
            links = sanapshot.val();
            if (boolplay) sync(true);
        });

    function initplayer() {
        let srclink = links.mediaLink;
        let newplayer = new Audio();
        newplayer.src = srclink;
        return newplayer;
    }

    function sync(force = false) {
        if (!comments.canbesynced && !force) return;
        lagging = 0;
        oldplayer = player;
        player = null;
        play();
    }

    let loadinganewplayeralready = false;

    function play() {
        let newplayer = null;
        if (player == null && !loadinganewplayeralready) {
            newplayer = initplayer();
            boolplay = false;
            loadinganewplayeralready = true;
        }
        if (boolplay) {
            player.pause();
            playerpausedat = new Date();
            boolplay = false;
            playIcon.src = "./icons/play.svg";
            console.log("Pausing leads to lagging");
            logMessage.innerText = "Pausing causes lagging";
        } else {
            if (lagging == 0) {
                console.log("Establishing Connection...");
                logMessage.innerText = "Establising connection";
            } else {
                logMessage.innerText = "Network error causes lagging";
                console.log("Network error leads to lagging...");
            }
            if (newplayer != null) {
                if (newplayer.play() !== undefined) {
                    newplayer
                        .play()
                        .then(() => {
                            if (oldplayer != null) {
                                //need to change this piece of code
                                oldplayer.pause();
                                oldplayer.src = "";
                            }
                            player = newplayer;
                            boolplay = true;
                            playIcon.src = "./icons/pause.svg";
                            console.log("Connected Successfully...");
                            logMessage.innerText = "Connected successfully";
                            loadinganewplayeralready = false;
                            return;
                        })
                        .catch((error) => {
                            if (oldplayer != null) {
                                //need to change this piece of code
                                oldplayer.pause();
                            }
                            playIcon.src = "./icons/play.svg";
                            console.log("Network Issues...");
                            logMessage.innerText = "Network issues";
                            loadinganewplayeralready = false;
                            return;
                        });
                }
                return;
            }
            if (player.play() !== undefined) {
                player
                    .play()
                    .then(() => {
                        let temp = new Date();
                        lagging += (temp - playerpausedat) / 1000;
                        boolplay = true;
                        playIcon.src = "./icons/pause.svg";
                        if (lagging > 0.4) {
                            console.log(
                                "Laggin by " +
                                    Math.floor(lagging * 100) / 100 +
                                    "s with livestream"
                            );
                            logMessage.innerText =
                                "Laggin by " +
                                Math.floor(lagging * 100) / 100 +
                                "s with livestream";
                        }
                    })
                    .catch((error) => {
                        playIcon.src = "./icons/play.svg";
                        console.log("Network isuue detected");
                        logMessage.innerText = "Network issue detected";
                        return;
                    });
            }
        }
    }

    let pretime;
    let buffering = 0;
    let called = false;
    let boolplay = false;

    // Checking for connection loss
    setInterval(() => {
        if (!boolplay || player == null) {
            return;
        }
        if (player.currentTime == pretime) {
            buffering++;
            if (buffering > 2 && !called) {
                play();
                play();
                called = true;
            }
        } else {
            buffering = 0;
            called = false;
        }
        pretime = player.currentTime;
    }, 100);

    // Media session
    if ("mediaSession" in navigator) {
        navigator.mediaSession.metadata = new MediaMetadata({
            title: "Rajpath Recalls",
            artist: "Music Cloud",
            album: "1000+ songs",
            artwork: [
                {
                    src: "./icons/microphone.png",
                    sizes: "500x500",
                    type: "image/png",
                },
            ],
        });

        navigator.mediaSession.setActionHandler("play", function () {
            play();
        });
        navigator.mediaSession.setActionHandler("pause", function () {
            play();
        });
    }

    //Chat
    let messages = [];
    firebase
        .database()
        .ref("Chat/")
        .on("value", (sanapshot) => {
            messages = []
            sanapshot.forEach((childsnap) => {
                messages.push(childsnap.val());
            });
            messages = messages
            if(!chatOpen)
                newMsgIcon.style.display = "block"
        });
</script>

<div class="container">
    <main>
        <section id="player">
            <div class="player-contents-container">
                <h1><span id="rajpath">Rajpath</span>Recalls</h1>
                <div class="play-button-container">
                    <div class="play-ring">
                        <div class="play-ring">
                            <div class="play-ring">
                                <div class="play-ring">
                                    <div
                                        bind:this={playButton}
                                        class="play-button"
                                    >
                                        <img
                                            bind:this={playIcon}
                                            alt="Play"
                                            src="icons/play.svg"
                                            class="play-icon"
                                        />
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <p bind:this={tagline} id="tagline">
                    <span id="musiccloud">MusicCloud</span> 1000+ Songs
                </p>
                <div class="controls-container">
                    <div
                        bind:this={calendarButton}
                        class="control-icon-wrapper"
                    >
                        <img
                            class="control-icon"
                            src="icons/calendar.svg"
                            alt="Calendar"
                        />
                    </div>
                    <div bind:this={chatButton} class="control-icon-wrapper">
                        <img
                            class="control-icon"
                            src="icons/message-circle.svg"
                            alt="Chat"
                        />
                        <div bind:this={newMsgIcon} id="new-message-icon" />
                    </div>
                </div>
                <div class="log-message-container">
                    <p bind:this={logMessage} />
                </div>
            </div>
        </section>

        <!-- Chat modal -->
        <div bind:this={chatBox} class="modal-container hide">
            <Chat bind:this={chat} {messages} {viewChatBox} />
        </div>

        <!-- Calendar modal -->
        <div bind:this={calendar} class="modal-container hide">
            <Calendar {viewCalendar} {events} />
        </div>

        <section id="section">
            <div class="section-content-container">
                <h2>About Us</h2>
                <p>
                    The Rajpath RECalls Web App is for radio broadcasting events
                    happening in and related to NIT Calicut, which is conducted
                    for and by the students. This is a broadcasting open-source
                    web app, with multiple features including:
                </p>
                <ul>
                    <li>Real-time update of events and its schedule</li>
                    <li>Live comment section</li>
                    <li>Play-in background set-up</li>
                    <li>Accessible from the notification bar</li>
                    <li>Ad-free</li>
                    <li>Responsive to all devices</li>
                </ul>
            </div>
        </section>

        <section id="section">
            <div class="section-content-container">
                <h2>Events</h2>
                <p>
                    A radio without its listeners is like food without salt and
                    spices. We would love to be able to connect with all of our
                    listeners and help improve your experience. Here you will
                    find a google form where you can make song requests, song
                    dedications for Serenade, share stories for our Chai Un-Cut
                    or give topics to be discussed in Unfiltered.
                </p>
            </div>
        </section>

        <section id="section">
            <div class="section-content-container">
                <h2>Recall & Request</h2>
                <p>
                    A radio without its listeners is like food without salt and
                    spices. We would love to be able to connect with all of our
                    listeners and help improve your experience. Here you will
                    find a google form where you can make song requests, song
                    dedications for Serenade, share stories for our Chai Un-Cut
                    or give topics to be discussed in Unfiltered.
                </p>
            </div>
        </section>

        <section id="section">
            <div class="section-content-container">
                <h2>Contact Us</h2>
                <p>
                    Any new venture can grow only with the help of constant
                    feedback. Feel free to contact us
                </p>
                <p>
                    <a href="mailto:rajapathrecalls@gmail.com"
                        >rajapathrecalls@gmail.com</a
                    >
                </p>
                <div class="social-icons-container">
                    <a
                        href="https://www.facebook.com/rajpath.recalls"
                        target="__blank">
                        <div class="social-icon-wrapper">
                            <img
                                class="social-icon"
                                src="icons/facebook.svg"
                                alt="Facebook"
                            />
                        </div>
                    </a>
                    <a
                        href="https://in.linkedin.com/in/rajpath-recalls-radio-976757200"
                        target="__blank">
                        <div class="social-icon-wrapper">
                            <img
                                class="social-icon"
                                src="icons/linkedin.svg"
                                alt="LinkedIn"
                            />
                        </div>
                    </a>
                    <a
                        href="https://github.com/rajpathrecalls"
                        target="__blank">
                        <div class="social-icon-wrapper">
                            <img
                                class="social-icon"
                                src="icons/github.svg"
                                alt="GitHub"
                            />
                        </div>
                    </a>
                    <a
                        href="https://www.instagram.com/rajpath.recalls_nitc/"
                        target="__blank">
                        <div class="social-icon-wrapper">
                            <img
                                class="social-icon"
                                src="icons/instagram.svg"
                                alt="Instagram"
                            />
                        </div>
                    </a>
                </div>
            </div>
        </section>
    </main>
</div>

<style>
    .container {
        margin: 0 auto;
        width: 100%;
        max-width: 600px;
        min-width: 300px;
    }

    #player {
        height: 100vh;
    }

    .player-contents-container {
        display: flex;
        height: 100%;
        width: 100%;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }

    h1 {
        letter-spacing: 0.5px;
        color: rgba(255, 255, 255, 0.9);
        font-weight: 300;
        background: rgb(255, 255, 255);
        background: linear-gradient(
            90deg,
            rgb(255, 255, 255) 0%,
            rgb(184, 184, 184) 100%
        );
        background-clip: text;
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        margin-bottom: 28px;
    }

    #rajpath {
        font-weight: 600;
        background: rgb(255, 58, 58);
        background: linear-gradient(
            90deg,
            rgb(250, 50, 50) 0%,
            rgb(209, 16, 122) 100%
        );
        background-clip: text;
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
    }

    #tagline {
        font-weight: 300;
        margin-top: 30px;
        font-size: 14px;
        color: rgba(255, 255, 255, 0.7);
    }

    #musiccloud {
        margin-right: 3px;
        font-weight: 600;
        letter-spacing: 1px;
    }

    .play-button-container {
        display: flex;
    }

    .play-ring {
        display: flex;
        background: rgb(255, 58, 58);
        background: linear-gradient(
            90deg,
            rgba(255, 58, 58, 0.2) 0%,
            rgba(200, 33, 126, 0.2) 100%
        );
        padding: 15px;
        border-radius: 50%;
        box-shadow: 2px 3px 20px rgba(95, 6, 51, 0.26);
    }

    .play-icon {
        user-select: none;
        margin-left: 1px;
        width: 30px;
        height: 30px;
        opacity: 0.7;
        display: block;
    }

    .play-button {
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        width: 70px;
        height: 70px;
        border-radius: 50%;
        background: rgb(255, 58, 58);
        background: linear-gradient(
            90deg,
            rgba(255, 58, 58, 1) 0%,
            rgba(200, 33, 126, 1) 100%
        );
        box-shadow: 2px 3px 20px rgba(95, 6, 51, 0.26);
        transition: 0.3s ease all;
    }

    .play-button:hover {
        box-shadow: 3px 5px 20px rgba(88, 6, 47, 0.6);
    }

    .play-button:active {
        box-shadow: 2px 3px 20px rgba(95, 6, 51, 0.26);
    }

    #new-message-icon {
        position: absolute;
        right: 15px;
        top: 15px;
        width: 12px;
        height: 12px;
        background-color: rgba(250, 41, 76, 0.9);
        border-radius: 50%;
    }

    .controls-container {
        margin-top: 25px;
        width: 250px;
        display: flex;
        align-items: center;
        justify-content: space-evenly;
    }

    .control-icon-wrapper,
    .social-icon-wrapper {
        position: relative;
        background-color: rgba(255, 255, 255, 0.1);
        display: flex;
        align-items: center;
        justify-content: center;
        width: 60px;
        height: 60px;
        border-radius: 50%;
        cursor: pointer;
        transition: 0.1s ease all;
    }

    .control-icon-wrapper:hover,
    .social-icon-wrapper:hover {
        background-color: rgba(255, 255, 255, 0.05);
    }

    .control-icon-wrapper:active,
    .social-icon-wrapper:active {
        background-color: rgba(255, 255, 255, 0.1);
    }

    .social-icon-wrapper {
        box-sizing: border-box;
        margin: 0 5px;
        width: 45px;
        height: 45px;
    }

    .control-icon,
    .social-icon {
        user-select: none;
        opacity: 0.8;
        width: 30px;
        height: 30px;
    }

    .social-icon {
        width: 20px;
        height: 20px;
    }

    .social-icons-container {
        margin-top: 50px;
        display: flex;
        width: 90%;
        justify-content: space-between;
    }

    #section {
        margin: 100px 0;
        text-align: center;
        color: rgba(255, 255, 255, 0.5);
    }

    #section h2 {
        font-size: 20px;
        font-weight: 600;
        margin: 0;
    }

    .section-content-container {
        width: 70%;
        margin: 0 auto;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    #section p {
        line-height: 20px;
        font-weight: 100;
        letter-spacing: 1px;
        line-height: 20px;
        font-size: 14px;
    }

    #section ul {
        text-align: left;
    }

    a {
        color: #58a1f3;
        text-decoration: none;
        font-weight: 500;
    }

    .modal-container {
        z-index: 2;
        border-radius: 15px;
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
        width: 90%;
        height: 80vh;
        max-width: 400px;
        background-color: rgba(19, 13, 31, 0.795);
        backdrop-filter: blur(12px);
        box-shadow: 2px 2px 25px rgba(3, 4, 19, 0.534);
    }

    .log-message-container {
        letter-spacing: 0.5px;
        margin-top: 30px;
        font-size: 10px;
        font-weight: 300;
        color: rgba(255, 255, 255, 0.3);
    }
</style>
