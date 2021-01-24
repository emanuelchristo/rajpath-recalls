<script>
    import { onMount } from 'svelte'
    export let viewCalendar

    let closeButton

    onMount(() => {
        closeButton.onclick = () => {viewCalendar(false)}
    })
</script>

<div class="chat-container">
    <div class="username-container">
        <div>
            <span class="schedule">Schedule</span>
        </div>
        <div bind:this={ closeButton } class="close-icon-wrapper">
            <img src="icons/x.svg" alt="Close">
        </div>
    </div>
    <div class="events-container">
    
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

    .events-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        width: 100%;
        flex-grow: 1;
        overflow-y: scroll;
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

    .schedule {
        font-size: 14px;
        margin-right: 3px;
        font-weight: 600;
        letter-spacing: 0.5px;
        color: rgba(255, 255, 255, 0.8);
    }
</style>