<script>
    import { onMount } from "svelte";
    import Message from "./Message.svelte";
    export let messages;
    export let viewChatBox;
    export function scrlBtm() {
        messagesContainer.scrollTop = messagesContainer.scrollHeight;
    };

    let closeButton,
        messagesContainer,
        bottomButton,
        usernameInput,
        sendButton,
        messageInput,
        onlineUserCount;
    let username = "";
    let usernamesList = [
        "Razor",
        "Colossus",
        "Manticore",
        "Hanzo",
        "Black_Hawk",
        "Redox",
        "Zombie",
        "Virus",
        "Archer",
        "Orion",
        "Titanium",
        "Xenon",
        "Amenet",
        "Caprice",
        "Comet",
        "Quistis",
        "Astaroth",
        "Hellcat",
        "Velvet",
        "Beanie",
        "NarendraModi",
        "DonaldTrump",
        "JoeBiden",
        "IronMan",
        "CaptianAmerica",
        "BalckWidow",
        "ScarlettWitch",
        "StarLord",
        "Groot",
        "DrStrange",
        "RedSparrow",
        "Minion",
        "Ratatouile",
        "Batman",
        "WonderWoman",
        "Gamora",
        "HarryPotter",
        "Hermionie",
        "RonWeasley"
    ];

    function randomUsername() {
        return usernamesList[Math.floor(Math.random() * usernamesList.length)];
    }
    onMount(() => {
        closeButton.onclick = () => {
            viewChatBox(false);
        };
        bottomButton.onclick = () => {
            scrollToBottom();
        };
        sendButton.onclick = () => {
            process();
        };
        usernameInput.onchange = () => {
            setUsername();
        };
        username = randomUsername();
        usernameInput.placeholder = username;
        usernameInput.addEventListener("keyup", function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                usernameInput.blur();
            }
        });
        messageInput.addEventListener("keyup", function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                messageInput.blur();
                sendButton.click();
            }
        });
    });

    const scrollToBottom = () => {
        messagesContainer.scrollTop = messagesContainer.scrollHeight;
    };

    function setUsername() {
        let regex = /^[a-zA-Z0-9]+(?:_[A-Za-z0-9]+)*$/;
        let usrname = usernameInput.value;
        usrname = trim(usrname);
        usrname = usrname.split(" ")[0];
        if (usrname.length >= 5 && usrname.match(regex)) {
            username = usrname;
        } else {
            usernameInput.value = "";
            username = randomUsername();
            usernameInput.placeholder = username;
        }
    }

    function trim(str) {
        if (str[0] == " ") {
            str = str.substr(1, str.length);
            str = this.trim_f(str);
        }
        if (str[str.length - 1] == " ") {
            str = str.substr(0, str.length - 1);
            str = this.trim_l(str);
        }
        return str;
    }

    function process() {
        let msg = messageInput.value;
        msg = trim(msg);
        if (msg.length == 0) msg = null;
        if (
            msg == null ||
            msg == undefined ||
            msg == "" ||
            msg == " " ||
            msg == []
        ) {
            return;
        }
        sendMessage(msg);
        messageInput.value = ""
        messageInput.focus();
    }

    function sendMessage(msg) {
        let message = {};
        message["sender"] = username;
        message["message"] = msg;
        let d = new Date();
        message["time"] = d.getTime();
        firebase.database().ref("Chat/").push(message);
    }

    firebase
            .database()
            .ref("ActiveUsers/")
            .on("value", (sanapshot) => {
                let onlineUsers = 0;
                sanapshot.forEach((childsnap) => {
                    onlineUsers++;
                });
                onlineUserCount.innerHTML = `${onlineUsers} online`
            });
</script>

<div class="chat-container">
    <div class="username-container">
        <div>
            <span class="at">@</span>
            <input
                bind:this={usernameInput}
                id="username-input"
                type="text"
                value=""
            />
        </div>
        <div>
            <p class="online-users-count" bind:this={onlineUserCount}></p>
            <div bind:this={closeButton} class="close-icon-wrapper">
                <img src="icons/x.svg" alt="Close" />
            </div>
        </div>
    </div>
    <div bind:this={messagesContainer} class="messages-container">
        <div bind:this={bottomButton} class="bottom-button">
            <img src="./icons/arrow-down.svg" alt="D">    
        </div>
        {#each messages as message}
            <Message msg={message} {scrollToBottom} />
        {/each}
    </div>
    <div class="message-input-container">
        <input
            bind:this={messageInput}
            id="message-input"
            type="text"
            placeholder="Type something..."
        />
        <div bind:this={sendButton} class="send-button-wrapper">
            <img src="./icons/send.svg" alt="Send" />
        </div>
    </div>
</div>


<style>
    .chat-container {
        width: 100%;
        height: 100%;
        border-radius: 15px;
        padding: 17px 20px 20px 20px;
        box-sizing: border-box;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
    }

    .messages-container {
        position: relative;
        display: flex;
        flex-direction: column;
        align-items: center;
        width: 100%;
        flex-grow: 1;
        overflow-y: scroll;
    }

    .message-input-container {
        margin: 0 auto;
        display: flex;
        align-items: center;
        justify-content: center;
        width: 100%;
        margin-top: 20px;
    }

    #message-input {
        outline: none;
        flex-grow: 1;
        margin-right: 10px;
        background: rgba(255, 255, 255, 0.1);
        border: none;
        color: rgba(255, 255, 255, 0.8);
        font-size: 14px;
        padding: 15px;
        border-radius: 7px;
    }

    .send-button-wrapper {
        position: relative;
        display: flex;
        align-items: center;
        justify-content: center;
        width: 40px;
        height: 40px;
        border-radius: 50%;
        cursor: pointer;
        transition: 0.1s ease all;
        background: rgb(255, 58, 58);
        background: linear-gradient(
            90deg,
            rgba(255, 58, 58, 1) 0%,
            rgba(200, 33, 126, 1) 100%
        );
    }

    .send-button-wrapper:hover {
        background: rgb(206, 48, 48);
        background: linear-gradient(
            45deg,
            rgba(255, 58, 58, 1) 0%,
            rgba(200, 33, 126, 1) 100%
        );
    }
    .send-button-wrapper:active {
        background: rgb(255, 58, 58);
        background: linear-gradient(
            90deg,
            rgba(255, 58, 58, 1) 0%,
            rgba(200, 33, 126, 1) 100%
        );
    }

    img {
        user-select: none;
        opacity: 0.8;
        width: 25px;
        height: 25px;
        margin-top: 3px;
        margin-left: -3px;
    }

    .username-container {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 15px;
        width: 100%;
    }

    #username-input {
        opacity: 0.7;
        background: none;
        padding: 6px 8px 7px 8px;
        font-size: 15px;
        color: #fff;
        border: 1px solid rgba(87, 87, 87, 0.589);
        position: relative;
        left: 0;
        border-radius: 5px;
        width: 150px;
        transition: 0.2s ease all;
    }

    #username-input:focus {
        width: 170px;
    }

    .at {
        font-size: 14px;
        margin-right: 3px;
        font-weight: 300;
        color: rgba(255, 255, 255, 0.8);
    }

    .username-container div {
        display: flex;
        align-items: center;
    }

    .online-users-count {
        all: unset;
        font-size: 11px;
        color: rgba(255, 255, 255, 0.5);
        margin-right: 15px;
        margin-top: 4px;
    }

    .bottom-button {
        display: flex;
        align-items: center;
        justify-content: center;
        z-index: 2;
        user-select: none;
        color: #fff;
        position: fixed;
        bottom: 110px;
        right: 40px;
        width: 30px;
        height: 30px;
        border-radius: 50%;
        background-color: rgba(255, 255, 255, 0.3);
    }

    .bottom-button img {
        margin-left: 0.25px;
        margin-bottom: 1px;
        width: 20px;
        height: 20px;
    }
</style>
