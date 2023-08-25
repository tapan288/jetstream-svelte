<script>
    import { createEventDispatcher } from "svelte";
    // import { computed, useSlots } from "vue";
    import SectionTitle from "./SectionTitle.svelte";

    const dispatch = createEventDispatcher();

    // defineEmits(["submitted"]);

    // TODO: work on events

    // const hasActions = computed(() => !!useSlots().actions);
</script>

<div class="md:grid md:grid-cols-3 md:gap-6">
    <SectionTitle>
        <svelte:fragment slot="title">
            <slot name="title" />
        </svelte:fragment>

        <svelte:fragment slot="description">
            <slot name="description" />
        </svelte:fragment>
    </SectionTitle>

    <div class="mt-5 md:mt-0 md:col-span-2">
        <form on:submit|preventDefault={() => dispatch("submitted")}>
            <div
                class="px-4 py-5 bg-white dark:bg-gray-800 sm:p-6 shadow {hasActions
                    ? 'sm:rounded-tl-md sm:rounded-tr-md'
                    : 'sm:rounded-md'}"
            >
                <div class="grid grid-cols-6 gap-6">
                    <slot name="form" />
                </div>
            </div>

            {#if hasActions}
                <div
                    class="flex items-center justify-end px-4 py-3 bg-gray-50 dark:bg-gray-800 text-right sm:px-6 shadow sm:rounded-bl-md sm:rounded-br-md"
                >
                    <slot name="actions" />
                </div>
            {/if}
        </form>
    </div>
</div>
