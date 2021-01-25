<script>
    import { onMount } from 'svelte'
    export let msg
    export let scrollToBottom

    onMount(scrollToBottom)

    let eventDate = new Date(msg.time)
    eventDate = new Date(eventDate.getFullYear(), eventDate.getMonth(), eventDate.getDate())
    let today = new Date()
    today = new Date(today.getFullYear(), today.getMonth(), today.getDate())
    let dayDiff = Math.round((today - eventDate)/(24*60*60*1000))
    let date 
    if(dayDiff == 0)
        date = "Today"
    else if(dayDiff == 1)
        date = "Yesterday"
    else {
        let month
        switch(eventDate.getMonth()) {
            case 0:
                month = "Jan"
                break;
            case 1:
                month = "Feb"
                break;
            case 2:
                month = "Mar"
                break;
            case 3:
                month = "Apr"
                break;
            case 4:
                month = "May"
                break;
            case 5:
                month = "Jun"
                break;
            case 6:
                month = "Jul"
                break;
            case 7:
                month = "Aug"
                break;
            case 8:
                month = "Sep"
                break;
            case 9:
                month = "Oct"
                break;
            case 10:
                month = "Nov"
                break;
            case 11:
                month = "Dec"
                break;
        }
        date = `${eventDate.getDate()} ${month}`
    }
    let time
    let evntDate = new Date(msg.time)
    let hrs = evntDate.getHours()
    let min = evntDate.getMinutes()
    if(hrs == 0)
        time = `12:${min}am`
    else if(hrs < 12)
        time = `${hrs}:${min}am`
    else if(hrs > 12)
        time = `${hrs-12}:${min}pm`
    else if(hrs == 12)
        time = `12:${min}pm`
    else
        time = ""
</script>

<div class="message">
    <h6>{ msg.sender }</h6>
    <p>{ msg.message }</p>
    <p class="time">{ `${date} ${time}` }</p>
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