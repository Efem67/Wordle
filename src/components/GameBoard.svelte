<script>
    export let array = [];
</script>

<div class="w-auto h-4/6 flex flex-col items-center justify-center ">
    <div class="overflow-y-auto">
        {#each array as row}
            <div class=" flex flex-row">
                {#each row as el}
                    <div
                        class="w-16 h-16 border-2 {el.border} m-1 flex items-center justify-center text-2xl font-bold {el.color}"
                    >
                        <span>{el.value == 0 ? "" : el.value}</span>
                    </div>
                {/each}
            </div>
        {/each}
    </div>
</div>
