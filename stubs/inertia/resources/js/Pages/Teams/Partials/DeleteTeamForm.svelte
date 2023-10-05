<script>
    import { useForm } from "@inertiajs/svelte";
    import ActionSection from "@/Components/ActionSection.svelte";
    import ConfirmationModal from "@/Components/ConfirmationModal.svelte";
    import DangerButton from "@/Components/DangerButton.svelte";
    import SecondaryButton from "@/Components/SecondaryButton.svelte";

    export let team;

    let confirmingTeamDeletion = false;
    const form = useForm({});

    const confirmTeamDeletion = () => {
        confirmingTeamDeletion = true;
    };

    const deleteTeam = () => {
        $form.delete(route("teams.destroy", props.team), {
            errorBag: "deleteTeam",
        });
    };
</script>

<ActionSection>
    <svelte:fragment slot="title">Delete Team</svelte:fragment>

    <svelte:fragment slot="description">
        Permanently delete this team.
    </svelte:fragment>

    <svelte:fragment slot="content">
        <div class="max-w-xl text-sm text-gray-600 dark:text-gray-400">
            Once a team is deleted, all of its resources and data will be
            permanently deleted. Before deleting this team, please download any
            data or information regarding this team that you wish to retain.
        </div>

        <div class="mt-5">
            <DangerButton on:click={confirmTeamDeletion}>
                Delete Team
            </DangerButton>
        </div>

        <!-- Delete Team Confirmation Modal -->
        <ConfirmationModal
            show={confirmingTeamDeletion}
            onClose={(confirmingTeamDeletion = false)}
        >
            <svelte:fragment slot="title">Delete Team</svelte:fragment>

            <svelte:fragment slot="content">
                Are you sure you want to delete this team? Once a team is
                deleted, all of its resources and data will be permanently
                deleted.
            </svelte:fragment>

            <svelte:fragment slot="footer">
                <SecondaryButton @click="confirmingTeamDeletion = false">
                    Cancel
                </SecondaryButton>

                <DangerButton
                    class="ml-3 {$form.processing ? 'opacity-25' : ''}"
                    disabled={$form.processing}
                    on:click={deleteTeam}
                >
                    Delete Team
                </DangerButton>
            </svelte:fragment>
        </ConfirmationModal>
    </svelte:fragment>
</ActionSection>
