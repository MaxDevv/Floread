<script>
    
    let elementValues = $state({});
    let readSpeed = $state(200);
    let text = "Welcome to FloRead...We make reading quick, easy, and enjoyable.Did you know the average person reads 200-300 words per minute, but with the right techniques, you could reach up to 700 wpm? That's an entire novel in just over an hour.FloRead brings you a simple, organized experience designed to work with your brain. Our smooth, animated display shows you one sentence at a time, keeping you focused and engaged.No more jumbled pages, flipping back and forth, or losing focus. With FloRead, you can tackle long documents, books, and papers in minutes while actually enjoying it.Ready to transform your reading experience? Paste your text below and start reading at incredible speeds.";
    const animationDuration = 80;
    elementValues.currentLine = 0;
    let playing = $state(false);
    function nextLine(animate = true) {
        elementValues.lineShift = animate;
        setTimeout(() => {
            if (elementValues.currentLine < elementValues.lines.length) elementValues.currentLine += 1;
            elementValues.lineShift = false;
        }, animate ? animationDuration : 1);
    }
    

    function previousLine(animate = true) {
        // elementValues.lineShift = true;
        setTimeout(() => {
            if (elementValues.currentLine > 0) elementValues.currentLine -= 1;
            // elementValues.lineShift = false;
        }, animate ? animationDuration : 1);
    }

    function play() {
        if (playing) {
            if (elementValues.currentLine == elementValues.lines.length) {
                
                playing = false;
                return;
            }
            let averageWords = 0;
            elementValues.lines.forEach(line => {
                averageWords += (line.split(" ").length)/elementValues.lines.length;
            });
            // console.log(averageWords);
            let slightAdjustment = 1 + ((averageWords/(elementValues.lines[elementValues.currentLine].split(" ").length) - 1)/3)
            slightAdjustment = Math.min(1.25, Math.max(slightAdjustment, 0.75));
            console.log(slightAdjustment);
            let duration = 1000 * 60 * averageWords * slightAdjustment / readSpeed;
            duration = Math.max(duration, animationDuration+20);
            nextLine();
            setTimeout(play, duration);
        }
    }

    function splitText(input, maxLineLength = 35) {
        const lines = [];

        while (input.length > maxLineLength) {
            let breakPoint = maxLineLength;

            // Find the closest period, then comma, then space within the range
            // const lineBreakIndex = input.lastIndexOf('\n', maxLineLength);
            const periodIndex = input.lastIndexOf('.', maxLineLength);
            const commaIndex = input.lastIndexOf(',', maxLineLength);
            const spaceIndex = input.lastIndexOf(' ', maxLineLength);

            // if (lineBreakIndex > -1) {
            //     breakPoint = lineBreakIndex;
            // } else
            if (periodIndex > -1) {
                breakPoint = periodIndex + 1; // Include the period in the line
            } else if (commaIndex > -1) {
                breakPoint = commaIndex + 1; // Include the comma in the line
            } else if (spaceIndex > -1) {
                breakPoint = spaceIndex;
            }

            // Add the line to the result array
            lines.push(input.slice(0, breakPoint).trim());

            // Remove the processed part from the input
            input = input.slice(breakPoint).trim();
        }

        // Add any remaining text as the last line
        if (input.length > 0) {
            lines.push(input);
        }

        return lines;
    }

    $effect(() => {
        elementValues.lines = splitText(text);
    })
</script>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=arrow_back_2" />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=play_circle" />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=pause_circle" />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=play_arrow" />
<div class="mainContainer">
    <div class="cover">

        <div class="slidecontainer subtleGlow">
            <input type="range" min="50" max="700" bind:value={readSpeed}>
            <span>&mdash;</span>
            {readSpeed} WPM
        </div>
        <div class="controls">
            <button onclick={() => {previousLine(false)}}>
                <span class="material-symbols-outlined">
                    &#xf43a;
                </span>
            </button>
            <button onclick={() => {
                    playing = !playing;
                    play();
                }
            }>
            {#if !playing}
                <span class="material-symbols-outlined">
                    &#xE1C4;
                </span>
            {:else}
                <span class="material-symbols-outlined">
                    &#xe1a2;
                </span>
                {/if}
        </button>
            <button onclick={() => {nextLine(false)}}>
                <span class="material-symbols-outlined">
                    &#xe037;
                </span>
            </button>
        </div>
    </div>
    <div class="textContainer {elementValues.lineShift ? "lineShift" : ""}" 
    style="margin-top: calc(30vh - var(--pHeight) * {elementValues.currentLine});">
        {#each elementValues.lines as line, i}
            <p class="{((i) == elementValues.currentLine) ? "focus" : ""}">{line}</p>
        {/each}
    </div>

</div>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Cal+Sans&display=swap');

    :root {
        --cosmic-latte: hsla(49, 60%, 93%, 1);
        --cerise: hsla(345, 76%, 58%, 1);
        --coral: hsla(14, 100%, 70%, 1);
        --paynes-gray: hsla(216, 14%, 40%, 1);
        --raisin-black: hsla(231, 23%, 16%, 1);
        --pHeight: calc(2.625rem);

    }
    
    :global(html, body) {
        background-color: var(--raisin-black);
        height: 99%;
        font-family: "Cal Sans", sans-serif;
    }
    :global(*) {
        color: var(--cosmic-latte);
    }
    .subtleGlow {

        text-shadow: 0px 0px 10px color-mix(in srgb, var(--cosmic-latte) 50%, transparent 50%);
    
    }
    div.mainContainer {
        display: flex;
        align-items: center;
        /* justify-content: center; */
        flex-direction: column;
        height: 100%;
        top: 0px;
        left: 0px;
    }
    div.cover {
        top: 0px;
        left: 0px;
        background: linear-gradient(180deg, var(--raisin-black) 20%,  color-mix(in srgb, var(--raisin-black) 90%, transparent 10%) 92.5%, rgba(0, 0, 0, 0) 100%);
        width: 100%;
        height: calc(30vh - var(--pHeight));
        position: fixed;
        z-index: 1;
        
        display: flex;
        align-items: center;
        flex-direction: column;
        justify-content: center;
        /* padding-top: 10em; */
        gap: 2em;
        div {
            display: flex;
            justify-content: center;
        }
        .controls { 
            * {
                /* color: black; */
            }
            display: flex;
            gap: 3em;
            flex-direction: row;
            button {
                display: flex;
                justify-content: center;
                align-items: center;
                background-color: transparent;
                border: none;
                scale: 2;
                text-shadow: 0px 0px 15px;
            }
        }

        div.slidecontainer {
            display: flex;
            gap: 0.25em;
            flex-direction: row;

        }
    }
    div.textContainer {
        overflow: hidden;
        margin-top: calc(30vh - var(--pHeight) * 1);
        font-size: 2rem;
        padding: 0px;
        p {
            padding: 0px;
            margin: 0px;
            opacity: 10%;
            text-shadow: 0px 0px 15px;
        }
        p.focus {
            opacity: 80%;
        }
        font-weight: 400;
        text-align: center;
        /* cubic-bezier(0.2, 1.25, 0.8, 0)  */
    }

    .lineShift {

        animation: nextLine 80ms ease-in; /*cubic-bezier(0.974, 0.397, 0.989, 1.655);*/
        animation-fill-mode: forwards;  
    }

    @keyframes nextLine {
        0% {
            transform: translateY(0em);

        }
        100% {
            transform: translateY(-2.625rem);

        }
    }
</style>