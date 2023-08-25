<script>
    export let on = false;
</script>

<div>
    <transition
        leave-active-class="transition ease-in duration-1000"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
    >
        {#if on}
            <div class="text-sm text-gray-600 dark:text-gray-400">
                <slot />
            </div>
        {/if}
    </transition>
</div>
