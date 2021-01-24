<script>
    import { onMount } from 'svelte'
    import Message from './Message.svelte'
    export let viewChatBox

    let messages = [
        {
            username: "emanuel.christo",
            body: "This is a dummy message",
            time: "3:54 pm"
        },
        {
            username: "bottlecap",
            body: "This is a dummy message",
            time: "3:54 pm"
        },
        {
            username: "toothbrush",
            body: "This is a dummy message",
            time: "3:54 pm"
        },
        {
            username: "mouse",
            body: "This is a dummy message",
            time: "3:54 pm"
        },
        {
            username: "laptop",
            body: "This is a dummy message",
            time: "3:54 pm"
        },
        {
            username: "warrior",
            body: "This is a dummy message",
            time: "3:54 pm"
        }
    ]

    let closeButton

    onMount(() => {
        closeButton.onclick = () => {viewChatBox(false)}
    })
</script>

<div class="chat-container">
    <div class="username-container">
        <div>
            <span class="at">@</span>
            <input id="username-input" type="text" placeholder="Username" value="cris">
        </div>
        <div bind:this={ closeButton } class="close-icon-wrapper">
            <img src="icons/x.svg" alt="Close">
        </div>
    </div>
    <div class="messages-container">
        {#each messages as message}
            <Message msg={ message } />
        {/each}
    </div>
    <div class="message-input-container">
        <input id="message-input" type="text" placeholder="Type something...">
        <div class="send-button-wrapper"><img src="./icons/send.svg" alt="Send"></div>
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
</style>