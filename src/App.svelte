<script>
    import { onMount } from "svelte";
    import Slidy from "svelte-slidy";
    import Chat from "./Chat.svelte";
    import Calendar from "./Calendar.svelte";

    let chat, chatButton, chatBox, newMsgIcon;
    let downloadApp, slidyWrapper;
    let mouseDown = false;
    let showSync = false;
    let chatOpen = false;
    let calendarButton, calendar;
    let playButton, playIcon, syncButton, tagline, logMessage;
    let onlineUsersEle;
    let scheduleEvents = [];

    const events = [
        {
            id: 1,
            src: "./images/event_1.jpg",
            header: "The Not So Funny Show",
            text:
                "A show for the ones who crave for a daily dosage of laughter is right here. If you have a gloomy day, fret not, as this show is a concoction of carefully curated episodes to lighten you up.",
        },
        {
            id: 2,
            src: "./images/event_1.jpg",
            header: "Unfiltered",
            text:
                "The world is filled with commotion and uncertainty, and in the midst of that, there are different opinions that often go unheard. This is where we come together and share views on topics within our own campus and beyond.",
        },
        {
            id: 3,
            src: "./images/event_1.jpg",
            header: "Chai Un-Cut",
            text:
                "A campus that has begun its journey more than six decades ago, along with its transition from REC to NITC, it surely has a rich history. Hop right in to reminisce memories of our campus through the art of storytelling.",
        },
        {
            id: 4,
            src: "./images/event_1.jpg",
            header: "Center Circle Scoop",
            text:
                "Change is constant on our campus, and we often tend to get lost in the ever-changing dynamics. Fear not, as Center Circle Scoop is there to update you.",
        },
        {
            id: 5,
            src: "./images/event_1.jpg",
            header: "RECalling",
            text:
                "Campus life can often be filled with indecisiveness, constant failures, and anxiety. Sometimes all we need are words of assurance from the ones who’ve been through it all. We present RECalling, a one on one session with an alumnus every fortnight.",
        },
        {
            id: 6,
            src: "./images/event_1.jpg",
            header: "Weekly Checklist",
            text:
                "Constant assignments, tests, and quizzes has become a routine for us. In the midst of that, we wish for a checklist of the week. Tune in to know the latest trends, places, and binge-worthy shows.",
        },
        {
            id: 7,
            src: "./images/event_1.jpg",
            header: "Serenade",
            text:
                "Want to dedicate a song for someone special (anonymously, of course) or want everyone to discover a song you love. This is your space.",
        },
        {
            id: 8,
            src: "./images/event_1.jpg",
            header: "Voice",
            text:
                "NITC is a pool of incredibly talented artists. This is an event where we play original/covers sent in by the students. Jump right in to seize the stage.",
        },
        {
            id: 9,
            src: "./images/event_1.jpg",
            header: "Music Cloud",
            text:
                "Wanna discover new songs and escape from the songs that you’ve played numerous times. Tune in to a Handpicked sound cloud and listen to the latest hits and diverse selections.",
        },
    ];

    // const events = [
    //     {id: 1, src: "./images/event_1.jpg", header: "Music Cloud", text: "This is some description"},
    //     {id: 1, src: "./images/event_1.jpg", header: "Serenade", text: "This is some description"},
    //     {id: 1, src: "./images/event_1.jpg", header: "Weekly Checklist", text: "This is some description"},
    //     {id: 1, src: "./images/event_1.jpg", header: "Unfiltered", text: "This is some description"},
    // ]

    let name = "Slidy";

    const slidy = {
        slides: events,
        wrap: {
            id: "slidy",
            width: "100%",
            height: "350px",
            padding: "0",
            align: "middle",
            alignmargin: 0,
        },
        slide: {
            gap: 20,
            width: "100%",
            height: "max-content",
            backimg: false,
            imgsrckey: "src",
            objectfit: "cover",
            overflow: "hidden",
        },
        controls: {
            dots: false,
            dotsnum: false,
            dotsarrow: false,
            dotspure: false,
            arrows: false,
            keys: false,
            drag: false,
            wheel: false,
        },
        options: {
            axis: "x",
            loop: true,
            duration: 350,
        },
    };
    let index = 0;

    onMount(() => {
        chatButton.onclick = () => {
            viewChatBox(true);
            chat.scrlBtm();
        };
        calendarButton.onclick = () => {
            viewCalendar(true);
        };

        playButton.onclick = () => {
            play();
        };

        syncButton.onclick = () => {
            sync(true);
        };
        syncButton.style.display = "none";
        updateCurrentEvent();
        updateCalendar();

        slidyWrapper.onmousedown = () => {
            mouseDown = true;
        };
        slidyWrapper.onmouseup = () => {
            mouseDown = false;
        };
        slidyWrapper.ontouchstart = () => {
            mouseDown = true;
        };
        slidyWrapper.ontouchend = () => {
            mouseDown = false;
        };

        setInterval(() => {
            if (mouseDown) return;
            if (slidyWrapper.mousedown) return;
            index++;
            if (index == events.length) index = 0;
        }, 3000);

        let userAgent = navigator.userAgent || navigator.vendor;
        if (userAgent.match(/Android/i)) downloadApp.style.display = "block";
    });

    $: if(slidyWrapper) {
        if (!mouseDown)
            slidyWrapper.style.backgroundColor = "rgba(80, 87, 97, 0.1)";
        else    
            slidyWrapper.style.backgroundColor = "rgba(150, 70, 87, 0.15)";
    }
   

    const viewChatBox = (show) => {
        if (show) {
            chatBox.style.display = "block";
            chatOpen = true;
        } else {
            chatBox.style.display = "none";
            chatOpen = false;
            newMsgIcon.style.display = "none";
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
                scheduleEvents = notOverPrograms;
            });
    };

    let ref = firebase.database().ref("ActiveUsers/").push({
        platform: "web-github.io",
    });
    ref.onDisconnect().remove();

    //Player
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
        if (!force) return;
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
            showSync = false;
            logMessage.innerText = "Pausing causes lagging";
        } else {
            showSync = false;
            if (lagging == 0) {
                logMessage.innerText = "Establishing connection";
            } else {
                logMessage.innerText = "Network error causes lagging";
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
                            showSync = false;
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
                            showSync = false;
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
                            logMessage.innerText =
                                "Lagging by " +
                                Math.floor(lagging * 100) / 100 +
                                "s with livestream";
                            showSync = true;
                        }
                    })
                    .catch((error) => {
                        playIcon.src = "./icons/play.svg";
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

    $: {
        if (syncButton) {
            if (showSync) syncButton.style.display = "flex";
            else syncButton.style.display = "none";
        }
    }
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
                    src: "./cover.jpg",
                    sizes: "640x640",
                    type: "image/jpg",
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
            messages = [];
            sanapshot.forEach((childsnap) => {
                messages.push(childsnap.val());
            });
            messages = messages;
            if (!chatOpen) newMsgIcon.style.display = "block";
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
                    <div bind:this={syncButton} class="control-icon-wrapper">
                        <img
                            class="control-icon"
                            src="icons/refresh-cw.svg"
                            alt="Sync"
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
            <Calendar {viewCalendar} events={scheduleEvents} />
        </div>

        <section id="section">
            <div class="section-content-container">
                <div class="download-app-container" bind:this={downloadApp}>
                    <p>Download our android app</p>
                    <a
                        href="https://play.google.com/store/apps/details?id=com.nitc.rajpathrecalls"
                        target="__blank"
                    >
                        <button>Go to PlayStore</button>
                    </a>
                </div>
                <div bind:this={slidyWrapper} class="slidy-wrapper">
                    <Slidy {...slidy} bind:index let:item>
                        <div class="slide">
                            <!-- wrapper for new skin -->
                            <article>
                                <h2>{item.header}</h2>
                                <p>
                                    {item.text}
                                </p>
                            </article>
                        </div>
                    </Slidy>
                </div>
            </div>
        </section>

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
                <h2>Recall & Request</h2>
                <p>
                    A radio without its listeners is like food without salt and
                    spices. We would love to be able to connect with all of our
                    listeners and help improve your experience. Here you will
                    find a <a href="https://forms.gle/ZnMw9rX1yKfxeNBS6"
                        >google form</a
                    > where you can make song requests, song dedications for Serenade,
                    share stories for our Chai Un-Cut or give topics to be discussed
                    in Unfiltered.
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
                        target="__blank"
                    >
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
                        target="__blank"
                    >
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
                        target="__blank"
                    >
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
                        target="__blank"
                    >
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
        margin: 90px 0;
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
        letter-spacing: 0.5px;
        line-height: 20px;
        font-size: 13px;
    }

    #section ul {
        text-align: left;
        line-height: 20px;
        font-weight: 100;
        letter-spacing: 0.5px;
        line-height: 20px;
        font-size: 13px;
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
        color: rgba(224, 224, 224, 0.3);
    }

    .slidy-wrapper {
        width: 100%;
        background-color: rgba(87, 87, 87, 0.1);
        padding: 0 15px;
        border-radius: 15px;
    }

    .slide p {
        color: rgba(255, 255, 255, 0.4);
    }

    .slide h2 {
        line-height: 20px;
        color: rgba(255, 255, 255, 0.6);
    }

    .active .slide h2 {
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

    .active .slide p {
        color: rgba(255, 255, 255, 0.6);
    }

    .download-app-container {
        display: none;
        margin-bottom: 90px;
    }

    button {
        padding: 15px 25px;
        border-radius: 30px;
        text-transform: uppercase;
        letter-spacing: 1px;
        font-weight: 600;
        font-family: Poppins;
        font-size: 11pt;
        background: none;
        color: rgba(255, 255, 255, 0.8);
        border: 2px solid rgba(255, 255, 255, 0.8);
    }
</style>
