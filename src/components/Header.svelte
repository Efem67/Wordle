<script>
    import { createEventDispatcher } from "svelte";
    export let gameStarted;
    export let hintArray;
    const dispatch = createEventDispatcher();
</script>

<div
    class="w-full h-20  border-gray-300 border-b flex items-center shadow-lg bg-white"
>
    <div class="w-2/5" />
    <div class="w-1/5 text-center  flex flex-row items-center justify-center">
        <img
            src="src/assets/wordleIcon.png"
            alt="settings"
            class="w-14 h-14 mr-2"
        />
        <h1 class="text-4xl font-sans font-bold ">Wordle</h1>
    </div>

    <div class="w-2/5 flex flex-row items-center justify-end px-6">
        <div class="w-auto flex justify-center items-center mx-4">
            {#each hintArray as letter}
                <div
                    class="w-7 h-7 border-2 border-gray-300 mx-1 flex justify-center items-center font-bold {letter ==
                    0
                        ? 'border-gray-300'
                        : 'border-green-600'}"
                >
                    {letter == 0 ? "" : letter}
                </div>
            {/each}
        </div>

        <button on:click={() => dispatch("clickIntoHint", true)}>
            <img
                src="src/assets/lamp.png"
                alt="hint"
                class="w-7 h-7 cursor-pointer mr-4"
            />
        </button>
        <button on:click={() => (gameStarted = false)}>
            <img
                src="src/assets/back.png"
                alt="back"
                class="w-7 h-7 cursor-pointer mr-4"
            />
        </button>

        <button>
            <img
                src="src/assets/settings.png"
                alt="settings"
                class="w-7 h-7 cursor-pointer"
            />
        </button>
    </div>
</div>
