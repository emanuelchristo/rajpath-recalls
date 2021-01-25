<script>
    export let event

    let eventDate = new Date(event.time)
    eventDate = new Date(eventDate.getFullYear(), eventDate.getMonth(), eventDate.getDate())
    let today = new Date()
    today = new Date(today.getFullYear(), today.getMonth(), today.getDate())
    let dayDiff = Math.round((eventDate - today)/(24*60*60*1000))
    let date 
    if(dayDiff == 0)
        date = "Today"
    else if(dayDiff == 1)
        date = "Tomorrow"
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
    let evntDate = new Date(event.time)
    let hrs = evntDate.getHours()
    let min = evntDate.getMinutes()
    if(hrs < 12)
        time = `${hrs}:${min}am`
    else if(hrs == 0)
        time = `12:${min}am`
    else if(hrs > 12)
        time = `${hrs-12}:${min}pm`
    else if(hrs == 12)
        time = `12:${min}pm`
    else
        time = ""
</script>

<div class="event-container">
    <div class="top">
        <h3>{event.main}</h3>
        <p>{event.sub}</p>
    </div>
    <div class="bottom">
        <div class="date-wrapper">
            <p class="date">{date}</p>
        </div>
        <div class="time-wrapper">
            <p class="time">{time}</p>
        </div>
    </div>
</div>

<style>
    .event-container {
        box-sizing: border-box;  
        width: 100%;
        display: flex;
        flex-direction: column;
        padding: 15px;
        border-radius: 10px;
        background-color: rgba(255, 255, 255, 0.05);
        margin: 5px 0;
        color: rgba(255, 255, 255, 0.9);
    }

    h3 {
        all: unset;
        font-size: 15px;
        font-weight: 600;
        letter-spacing: 0.5px;
    }

    p {
        all: unset;
        font-size: 13px;
        font-weight: 300;
        letter-spacing: 0.5px;
    }

    .bottom {
        display: flex;
        align-items: center;
        justify-content: flex-end;
    }

    .time, .date {
        color: rgba(255, 255, 255, 0.8);
        font-size: 10px;
        font-weight: 500;
        margin: 0 3px;
    }

    .date {
        color: rgba(252, 206, 2, 0.9);
    }
</style>