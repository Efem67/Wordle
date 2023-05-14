<script>
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher();
    let lengths = [3, 4, 5, 6, 7, 8, 9, 10, 11, 12];
    let selectedLength = undefined;
    let files = undefined;
    let convertedJson = undefined;

    const logFile = (event) => {
        dispatch("setGamePlayOptions", {
            wordsLength: selectedLength,
            file: JSON.parse(event.target.result),
        });
    };

    const sendOptions = async () => {
        if (files) {
            let reader = new FileReader();
            reader.onload = logFile;
            reader.readAsText(files[0]);
        } else {
            dispatch("setGamePlayOptions", {
                wordsLength: selectedLength,
                file: undefined,
            });
        }
    };
</script>

<div class="w-full h-full flex justify-center items-center">
    <div
        class="border-2 rounded-lg border-gray-200 w-1/4 h-2/5 bg-gray-100 shadow-xl flex flex-col divide-y divide-gray-200"
    >
        <div
            class="w-full h-2/5 flex justify-center items-center text-3xl text-gray-600 font-bold text-center"
        >
            Opcje rozgrywki
        </div>
        <div
            class="w-full h-1/5 flex flex-row text-lg justify-start items-center text-gray-600 font-semibold px-6"
        >
            <div class="w-1/2 flex justify-start items-end">
                Długość wyrazów:
            </div>
            <div class="w-1/2 flex justify-center items-end">
                <select
                    class="w-1/2 border border-gray-300 cursor-pointer rounded-md text-center h-7"
                    bind:value={selectedLength}
                >
                    {#each lengths as length}
                        <option value={length}>
                            {length}
                        </option>
                    {/each}
                </select>
            </div>
        </div>

        <div
            class="w-full h-1/5 flex flex-row text-lg justify-start items-center text-gray-600 font-semibold px-6"
        >
            <div class="w-1/2 flex justify-start items-end">
                Własny plik JSON:
            </div>
            <div class="w-1/2 flex justify-center items-end">
                <input
                    bind:files
                    type="file"
                    id="actual-btn"
                    accept=".json"
                    hidden
                />
                <label
                    for="actual-btn"
                    class="text-base border border-gray-300 cursor-pointer p-1 rounded-md bg-white  w-auto  text-center px-4 truncate h-7 flex items-center justify-center"
                    >{#if files}
                        {files[0].name}
                    {:else}
                        ---
                    {/if}</label
                >
            </div>
        </div>

        <div
            class="w-full h-1/5 flex flex-row text-lg justify-center items-center text-gray-600 font-semibold px-6"
        >
            <button
                on:click={sendOptions}
                class="w-2/5 shadow-md bg-green-300 py-1 flex justify-center items-center rounded-lg hover:bg-green-200 hover:text-gray-500 border-green-500 focus:border-2 transition-transform"
            >
                Zagraj
            </button>
        </div>
    </div>
</div>
