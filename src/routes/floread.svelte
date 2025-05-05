<script>
    
    let elementValues = $state({});
    let readSpeed = $state(200);
    let text = $state("Welcome to FloRead...We make reading quick, easy, and enjoyable.Did you know the average person reads 200-300 words per minute, but with the right techniques, you could reach up to 700 wpm? That's an entire novel in just over an hour.FloRead brings you a simple, organized experience designed to work with your brain. Our smooth, animated display shows you one sentence at a time, keeping you focused and engaged.No more jumbled pages, flipping back and forth, or losing focus. With FloRead, you can tackle long documents, books, and papers in minutes while actually enjoying it.You can customize your reading experience using the controls above: adjust your reading speed with the WPM slider, toggle Single Word Mode to read one word at a time, and set your preferred line length to match your comfort level.Ready to transform your reading experience? Load your text using the load text button above and start reading at incredible speeds.");
    const animationDuration = 100;
    elementValues.currentLine = 0;
    elementValues.maxLineLength = 20;
    elementValues.singleWord = false;
    let playing = $state(false);
    function nextLine(animate = true) {
        elementValues.lineShift = animate;
        setTimeout(() => {
            if (elementValues.currentLine < elementValues.lines.length) elementValues.currentLine += 1;
            elementValues.lineShift = false;
        }, animate ? animationDuration : 1);
    }
    elementValues.lines = []
    

    function previousLine(animate = true) {
        // elementValues.lineShift = true;
        setTimeout(() => {
            if (elementValues.currentLine > 0) elementValues.currentLine -= 1;
            // elementValues.lineShift = false;
        }, animate ? animationDuration : 1);
    }

    function play(fromClick=false) {
        if (playing) {
            if (elementValues.currentLine >= elementValues.lines.length) {
                playing = false;
                if (fromClick) {
                    elementValues.currentLine = 0;
                }
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
            let duration = 1000 * 60 * averageWords * slightAdjustment / Math.pow(readSpeed, 1.005);
            let animate = true;
            if (duration < animationDuration + 1) {
                animate = false;
            }
            // duration = Math.max(duration, animationDuration+1);
            nextLine(animate);
            setTimeout(play, duration);
        }
    }

    function splitText(input, maxLineLength = 20, singleWord = false) {
        const lines = [];
        if (singleWord) {
            input.split(/[.,;:—–…\s]/).forEach((line) => {
                if (line.trim()) lines.push(line.trim());
            })
        }
        else {
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
        }

        return lines;
    }

    $effect(() => {
        elementValues.lines = splitText(text, elementValues.maxLineLength, elementValues.singleWord);
        // console.log(elementValues.lines);
    })
    $effect(() => {
        elementValues.durationEstimate = Number((text.split(" ").length/readSpeed).toFixed(1));
    })
</script>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=arrow_back_2" />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=play_circle" />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=pause_circle" />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=play_arrow" />
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&icon_names=forward_circle" />
<div class="mainContainer">
    <div class="cover">

        <div class="info subtleGlow">
            <p>~{elementValues.durationEstimate} minute{elementValues.durationEstimate != 1 ? "s": ""} read...</p>
            <button onclick={() => {
                text = prompt("Please enter the text you would like to read. This text will be displayed one line at a time to help improve reading speed and comprehension.");
            }}>Load Text</button>
        </div>
        <div class="slidecontainer subtleGlow">
            <div>
                <input type="range" min="50" max="1250" step="50" bind:value={readSpeed}>
                <!-- <span>&mdash;</span> -->
                {readSpeed} WPM
            </div>
            <div>
                Single Word Mode: <input type="checkbox" name="" id=""  bind:checked={elementValues.singleWord}>
            </div>
            <div>
                <input type="range" min="1" max="50" step="2" bind:value={elementValues.maxLineLength}>
                Max Line Length 
                <!-- <span>&mdash;</span> -->
                {elementValues.maxLineLength} Letters 
            </div>
        </div>
        <div class="line"></div>
        <div class="controls">
            <button onclick={() => {previousLine(false)}}>
                <span class="material-symbols-outlined">
                    &#xf43a;
                </span>
            </button>
            <button onclick={() => {
                    playing = !playing;
                    play(true);
                }
            }>
            {#if elementValues.currentLine >= elementValues.lines.length - 1}
                <span class="material-symbols-outlined flipped">
                    &#xf6f5;
                </span>
            {:else if !playing}
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
        * {

            text-shadow: 0px 0px 10px color-mix(in srgb, var(--cosmic-latte) 50%, transparent 50%);
        
        }
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
        height: calc(30vh - var(--pHeight) + 4em);

        position: fixed;
        z-index: 1;
        
        display: flex;
        align-items: center;
        flex-direction: column;
        /* justify-content: space-around; */
        /* padding-top: 10em; */
        gap: .5em;
        div {
            display: flex;
            justify-content: center;
        }
        .info {
            display: flex;
            flex-direction: row-reverse;
            justify-content: space-around;
            align-items: center;
            width: 80%;
            font-size: 1.15rem;
            button {

                font-size: 1.15rem;
                font-weight: 900;
                height: fit-content;
                padding: 0.20em;
                padding-right: 0.5em;
                padding-left: 0.5em;
                border: none;
                border: 0.15em solid color-mix(in srgb, var(--cosmic-latte) 50%, transparent 50%);
                box-shadow: 0px 0px 10px color-mix(in srgb, var(--cosmic-latte) 50%, transparent 50%);

                background-color: transparent;
                border-radius: 0.5em;

            }
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
            div {
                display: flex;
                gap: 0.25em;
                flex-direction: row;
            }
            div:nth-child(odd) {

                @media (min-width: 36em) {
                flex-direction: column;
                }
                align-items: center;
            }
            div:nth-child(even){
                align-items: center;
            }
            div:nth-child(even)::after {
                /* content: "!"; */
            }
            div:nth-child(even)::before {
                content: "";
                background-color: var(--cosmic-latte);
                height: 0.7em;
                margin-right: -1px;
                width: 2px;
                box-shadow: 0px 0px 10px 1px var(--cosmic-latte);
                opacity: 60%;
            }
            div:nth-child(even)::after {
                content: "";
                background-color: var(--cosmic-latte);
                height: 0.7em;
                margin-left: -1px;
                width: 2px;
                box-shadow: 0px 0px 10px 1px var(--cosmic-latte);
                opacity: 60%;

            }
            display: flex;
            /* gap: 0.25em; */
            justify-content: space-evenly;

            flex-direction: row;
            @media (max-width: 36em) {
                flex-direction: column;
                gap: 0.5em;
                padding-bottom: 1.5em;
            }
            width: 100%;

        }
    }
    div.textContainer {
        overflow: hidden;
        margin-top: calc(30vh - var(--pHeight) * 1);
        font-size: 2rem;
        padding: 0px;
        @media (max-width: 36em) {
            padding-top: .5em;
        }
        p {
            padding: 0px;
            margin: 0px;
            opacity: 10%;
            text-shadow: 0px 0px 15px;
            line-height: var(--pHeight);
        }
        p.focus {
            opacity: 80%;
        }
        font-weight: 400;
        text-align: center;
        /* cubic-bezier(0.2, 1.25, 0.8, 0)  */
    }

    .lineShift {
        animation: nextLine 95ms linear; /*cubic-bezier(0.974, 0.397, 0.989, 1.655);*/
        animation-fill-mode: forwards;  
    }
    .flipped {
        transform: scale(-1, 1);
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