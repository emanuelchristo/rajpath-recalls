<script>
    export let onclick
    export let color = "black"

    let colors = {
        opacityHex: "1f",
        black: {
            D: "#222222",
            L: "#666666"
        },
        orange: {
            D: "#e1480e",
            L: "#f19731"
        },
        violet: {
            D: "#6037D4",
            L: "#7C5BD6"
        },
        blue: {
            D: "#174DCB",
            L: "#55AAF5"
        },
        pink: {
            D: "#B22F68",
            L: "#DF6290"
        },
        green: {
            D: "#1F8767",
            L: "#37B891"
        }
    }

    let colorT, colorD, colorL;

    if(!colors[color])
        color = "black"

    colorT = colors[color].D + colors.opacityHex
    colorD = colors[color].D
    colorL = colors[color].L
</script>

<style>
    .bg {
        display: flex;
        padding: 5px;
        border-radius: 100px;
        background-color: var(--colorT);
    
    }

    button {
        all: unset;
        user-select: none;
        cursor: pointer;
        background-color: #000;
        color: #fff;
        box-sizing: border-box;
        font-weight: 600;
        font-family: Helvetica;
        letter-spacing: 1px;
        font-size: 12pt;
        padding: 13px 25px;
        border-radius: 100px;
        text-transform: uppercase;
        transition: 0.1s ease all;
        background: var(--colorD);
        box-sizing: border-box;
        text-align: center;
        text-shadow: 0px 1px 3px var(--colorD);
        background: linear-gradient(90deg, var(--colorD) 0%, var(--colorL) 50%, var(--colorD) 100%);
    }

    button:hover {
        padding: 13px 30px;
    }

    button:active {
        padding: 13px 25px;
    }
</style>

<div class="container" style="--colorT: {colorT}; --colorD: {colorD}; --colorL: {colorL}">
    <div class="bg">
        <div class="bg">
            <div class="bg">
                <button on:click={onclick}><slot/></button>
            </div>
        </div>
    </div>
</div>