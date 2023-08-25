<script>
    import AppLayout from "@/Layouts/AppLayout.svelte";
    import CreateTeamForm from "@/Pages/Teams/Partials/CreateTeamForm.svelte";
</script>

<AppLayout title="Create Team">
    <svelte:fragment slot="header">
        <h2
            class="font-semibold text-xl text-gray-800 dark:text-gray-200 leading-tight"
        >
            Create Team
        </h2>
    </svelte:fragment>

    <div>
        <div class="max-w-7xl mx-auto py-10 sm:px-6 lg:px-8">
            <CreateTeamForm />
        </div>
    </div>
</AppLayout>
