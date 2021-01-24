<script>
    export let msg
</script>

<div class="message">
    <h6>{ msg.username }</h6>
    <p>{ msg.body }</p>
    <p class="time">{ msg.time }</p>
</div>

<style>
    .message {
        box-sizing: border-box;  
        width: 100%;
        display: flex;
        flex-direction: column;
        padding: 13px;
        border-radius: 10px;
        background-color: rgba(255, 255, 255, 0.05);
        margin: 5px 0;
    }

    h6 {
        all: unset;
        letter-spacing: 0.5px;
        font-size: 11px;
        font-weight: 600;
        line-height: 15px;
        color:rgba(252, 206, 2, 0.9);
    }

    p {
        all: unset;
        font-size: 12px;
        padding: 5px 0;
        color:rgba(255, 255, 255, 0.8);
    }

    .time {
        font-size: 10px;
        font-weight: bold;
        color:rgba(255, 255, 255, 0.5);
        padding: 0;
    }
</style>