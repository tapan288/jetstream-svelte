<script>
    import Modal from "./Modal.svelte";

    // const emit = defineEmits(["close"]);

    export let show = false,
        maxWidth = "2xl",
        closeable = true;

    const close = () => {
        emit("close");
    };
</script>

<Modal {show} max-width={maxWidth} {closeable} @close="close">
    <div class="px-6 py-4">
        <div class="text-lg font-medium text-gray-900 dark:text-gray-100">
            <slot name="title" />
        </div>

        <div class="mt-4 text-sm text-gray-600 dark:text-gray-400">
            <slot name="content" />
        </div>
    </div>

    <div
        class="flex flex-row justify-end px-6 py-4 bg-gray-100 dark:bg-gray-800 text-right"
    >
        <slot name="footer" />
    </div>
</Modal>
