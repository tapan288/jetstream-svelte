<script>
    import AppLayout from "@/Layouts/AppLayout.svelte";
    import ApiTokenManager from "@/Pages/API/Partials/ApiTokenManager.svelte";

    export let tokens = [],
        availablePermissions = [],
        defaultPermissions = [];
</script>

<AppLayout title="API Tokens">
    <svelte:fragment slot="header">
        <h2
            class="font-semibold text-xl text-gray-800 dark:text-gray-200 leading-tight"
        >
            API Tokens
        </h2>
    </svelte:fragment>

    <div>
        <div class="max-w-7xl mx-auto py-10 sm:px-6 lg:px-8">
            <ApiTokenManager
                {tokens}
                {availablePermissions}
                {defaultPermissions}
            />
        </div>
    </div>
</AppLayout>
