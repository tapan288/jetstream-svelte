<script>
    import { useForm } from "@inertiajs/svelte";
    import ActionMessage from "@/Components/ActionMessage.svelte";
    import FormSection from "@/Components/FormSection.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let team, permissions;

    const form = useForm({
        name: props.team.name,
    });

    const updateTeamName = () => {
        $form.put(route("teams.update", props.team), {
            errorBag: "updateTeamName",
            preserveScroll: true,
        });
    };
</script>

<FormSection @submitted={updateTeamName}>
    <svelte:fragment slot="title">Team Name</svelte:fragment>

    <svelte:fragment slot="description">
        The team's name and owner information.
    </svelte:fragment>

    <svelte:fragment slot="form">
        <!-- Team Owner Information -->
        <div class="col-span-6">
            <InputLabel value="Team Owner" />

            <div class="flex items-center mt-2">
                <img
                    class="w-12 h-12 rounded-full object-cover"
                    src={team.owner.profile_photo_url}
                    alt={team.owner.name}
                />

                <div class="ml-4 leading-tight">
                    <div class="text-gray-900 dark:text-white">
                        {team.owner.name}
                    </div>
                    <div class="text-gray-700 dark:text-gray-300 text-sm">
                        {team.owner.email}
                    </div>
                </div>
            </div>
        </div>

        <!-- Team Name -->
        <div class="col-span-6 sm:col-span-4">
            <InputLabel for="name" value="Team Name" />

            <TextInput
                id="name"
                bind:value={$$form.name}
                type="text"
                class="mt-1 block w-full"
                disabled={!permissions.canUpdateTeam}
            />

            <InputError message={$$form.errors.name} class="mt-2" />
        </div>
    </svelte:fragment>

    <svelte:fragment slot="actions">
        {#if permissions.canUpdateTeam}
            <ActionMessage on={$$form.recentlySuccessful} class="mr-3">
                Saved.
            </ActionMessage>

            <PrimaryButton
                class:opacity-25={$$form.processing}
                disabled={$$form.processing}
            >
                Save
            </PrimaryButton>
        {/if}
    </svelte:fragment>
</FormSection>
