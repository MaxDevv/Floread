<script>
import Floread from "./floread.svelte";
	import Home from "./home.svelte";
</script>
<Floread></Floread>
 <!-- <Home></Home> -->
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
</style>