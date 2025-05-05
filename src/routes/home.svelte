<script>
    
</script>
<div class="mainContainer">
    <h1 class="title">
        FloRead
    </h1>
    <p>Unlock</p>
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
    div.mainContainer {
        display: flex;
        align-items: center;
        /* justify-content: center; */
        flex-direction: column;
        height: 100%;
        top: 0px;
        left: 0px;
    }
    .title {
        font-size: 5rem;
        font-weight: normal;
        text-shadow: 0px 0px 0.35em black;
        letter-spacing: .025em;
        margin-top: 1.5em;
        /* color-mix(in srgb, var(--cosmic-latte) 50%, transparent 50%); */
        
    }
</style>