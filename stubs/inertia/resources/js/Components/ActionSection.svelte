<script>
    import SectionTitle from "./SectionTitle.svelte";
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
        <div
            class="px-4 py-5 sm:p-6 bg-white dark:bg-gray-800 shadow sm:rounded-lg"
        >
            <slot name="content" />
        </div>
    </div>
</div>
