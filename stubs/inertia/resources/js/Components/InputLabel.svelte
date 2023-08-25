<script>
    export let value;
</script>

<label class="block font-medium text-sm text-gray-700 dark:text-gray-300">
    {#if value}
        <span>
            {value}
        </span>
    {:else}
        <span>
            <slot />
        </span>
    {/if}
</label>
