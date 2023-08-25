<script>
    import AppLayout from "@/Layouts/AppLayout.svelte";
    import DeleteTeamForm from "@/Pages/Teams/Partials/DeleteTeamForm.svelte";
    import SectionBorder from "@/Components/SectionBorder.svelte";
    import TeamMemberManager from "@/Pages/Teams/Partials/TeamMemberManager.svelte";
    import UpdateTeamNameForm from "@/Pages/Teams/Partials/UpdateTeamNameForm.svelte";

    export let team,
        availableRoles = [],
        permissions;
</script>

<AppLayout title="Team Settings">
    <svelte:fragment slot="header">
        <h2
            class="font-semibold text-xl text-gray-800 dark:text-gray-200 leading-tight"
        >
            Team Settings
        </h2>
    </svelte:fragment>

    <div>
        <div class="max-w-7xl mx-auto py-10 sm:px-6 lg:px-8">
            <UpdateTeamNameForm {team} {permissions} />

            <TeamMemberManager
                class="mt-10 sm:mt-0"
                {team}
                available-roles={availableRoles}
                user-permissions={permissions}
            />

            {#if permissions.canDeleteTeam && !team.personal_team}
                <SectionBorder />

                <DeleteTeamForm class="mt-10 sm:mt-0" {team} />
            {/if}
        </div>
    </div>
</AppLayout>
