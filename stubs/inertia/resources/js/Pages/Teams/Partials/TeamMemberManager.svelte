<script>
    import { router, useForm, usePage } from "@inertiajs/svelte";

    import ActionMessage from "@/Components/ActionMessage.svelte";
    import ActionSection from "@/Components/ActionSection.svelte";
    import ConfirmationModal from "@/Components/ConfirmationModal.svelte";
    import DangerButton from "@/Components/DangerButton.svelte";
    import DialogModal from "@/Components/DialogModal.svelte";
    import FormSection from "@/Components/FormSection.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import SecondaryButton from "@/Components/SecondaryButton.svelte";
    import SectionBorder from "@/Components/SectionBorder.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let team,
        availableRoles = [],
        userPermissions;

    const addTeamMemberForm = useForm({
        email: "",
        role: null,
    });

    const updateRoleForm = useForm({
        role: null,
    });

    const leaveTeamForm = useForm({});
    const removeTeamMemberForm = useForm({});

    let currentlyManagingRole = false,
        managingRoleFor = null,
        confirmingLeavingTeam = false,
        teamMemberBeingRemoved = null;

    const addTeamMember = () => {
        addTeamMemberForm.post(route("team-members.store", props.team), {
            errorBag: "addTeamMember",
            preserveScroll: true,
            onSuccess: () => addTeamMemberForm.reset(),
        });
    };

    const cancelTeamInvitation = (invitation) => {
        router.delete(route("team-invitations.destroy", invitation), {
            preserveScroll: true,
        });
    };

    const manageRole = (teamMember) => {
        managingRoleFor = teamMember;
        updateRoleForm.role = teamMember.membership.role;
        currentlyManagingRole = true;
    };

    const updateRole = () => {
        updateRoleForm.put(
            route("team-members.update", [team, managingRoleFor]),
            {
                preserveScroll: true,
                onSuccess: () => (currentlyManagingRole = false),
            }
        );
    };

    const confirmLeavingTeam = () => {
        confirmingLeavingTeam = true;
    };

    const leaveTeam = () => {
        leaveTeamForm.delete(
            route("team-members.destroy", [team, usePage().auth.user])
        );
    };

    const confirmTeamMemberRemoval = (teamMember) => {
        teamMemberBeingRemoved = teamMember;
    };

    const removeTeamMember = () => {
        removeTeamMemberForm.delete(
            route("team-members.destroy", [team, teamMemberBeingRemoved]),
            {
                errorBag: "removeTeamMember",
                preserveScroll: true,
                preserveState: true,
                onSuccess: () => (teamMemberBeingRemoved = null),
            }
        );
    };

    const displayableRole = (role) => {
        return availableRoles.find((r) => r.key === role).name;
    };
</script>

<div>
    {#if userPermissions.canAddTeamMembers}
        <div>
            <SectionBorder />

            <!-- Add Team Member -->
            <FormSection @submitted={addTeamMember}>
                <svelte:fragment slot="title">Add Team Member</svelte:fragment>

                <svelte:fragment slot="description">
                    Add a new team member to your team, allowing them to
                    collaborate with you.
                </svelte:fragment>

                <svelte:fragment slot="form">
                    <div class="col-span-6">
                        <div
                            class="max-w-xl text-sm text-gray-600 dark:text-gray-400"
                        >
                            Please provide the email address of the person you
                            would like to add to this team.
                        </div>
                    </div>

                    <!-- Member Email -->
                    <div class="col-span-6 sm:col-span-4">
                        <InputLabel for="email" value="Email" />
                        <TextInput
                            id="email"
                            bind:value={$addTeamMemberForm.email}
                            type="email"
                            class="mt-1 block w-full"
                        />
                        <InputError
                            message={$addTeamMemberForm.errors.email}
                            class="mt-2"
                        />
                    </div>

                    <!-- Role -->
                    {#if availableRoles.length > 0}
                        <div class="col-span-6 lg:col-span-4">
                            <InputLabel for="roles" value="Role" />
                            <InputError
                                message={$addTeamMemberForm.errors.role}
                                class="mt-2"
                            />

                            <div
                                class="relative z-0 mt-1 border border-gray-200 dark:border-gray-700 rounded-lg cursor-pointer"
                            >
                                {#each availableRoles as role, i (role.key)}
                                    <button
                                        type="button"
                                        class="relative px-4 py-3 inline-flex w-full rounded-lg focus:z-10 focus:outline-none focus:border-indigo-500 dark:focus:border-indigo-600 focus:ring-2 focus:ring-indigo-500 dark:focus:ring-indigo-600 {i >
                                        0
                                            ? 'border-t border-gray-200 dark:border-gray-700 focus:border-none rounded-t-none'
                                            : ''}
                                    {i != Object.keys(availableRoles).length - 1
                                            ? 'rounded-b-none'
                                            : ''}"
                                        on:click={(addTeamMemberForm.role =
                                            role.key)}
                                    >
                                        <div
                                            class:opacity-50={addTeamMemberForm.role &&
                                                addTeamMemberForm.role !=
                                                    role.key}
                                        >
                                            <!-- Role Name -->
                                            <div class="flex items-center">
                                                <div
                                                    class="text-sm text-gray-600 dark:text-gray-400"
                                                    class:font-semibold={addTeamMemberForm.role ==
                                                        role.key}
                                                >
                                                    {role.name}
                                                </div>

                                                {#if addTeamMemberForm.role == role.key}
                                                    <svg
                                                        class="ml-2 h-5 w-5 text-green-400"
                                                        xmlns="http://www.w3.org/2000/svg"
                                                        fill="none"
                                                        viewBox="0 0 24 24"
                                                        stroke-width="1.5"
                                                        stroke="currentColor"
                                                    >
                                                        <path
                                                            stroke-linecap="round"
                                                            stroke-linejoin="round"
                                                            d="M9 12.75L11.25 15 15 9.75M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                                                        />
                                                    </svg>
                                                {/if}
                                            </div>

                                            <!-- Role Description -->
                                            <div
                                                class="mt-2 text-xs text-gray-600 dark:text-gray-400 text-left"
                                            >
                                                {role.description}
                                            </div>
                                        </div>
                                    </button>
                                {/each}
                            </div>
                        </div>
                    {/if}
                </svelte:fragment>

                <svelte:fragment slot="actions">
                    <ActionMessage
                        on={addTeamMemberForm.recentlySuccessful}
                        class="mr-3"
                    >
                        Added.
                    </ActionMessage>

                    <PrimaryButton
                        class:opacity-25={addTeamMemberForm.processing}
                        disabled={addTeamMemberForm.processing}
                    >
                        Add
                    </PrimaryButton>
                </svelte:fragment>
            </FormSection>
        </div>
    {/if}

    {#if team.team_invitations.length > 0 && userPermissions.canAddTeamMembers}
        <div>
            <SectionBorder />

            <!-- Team Member Invitations -->
            <ActionSection class="mt-10 sm:mt-0">
                <svelte:fragment slot="title">
                    Pending Team Invitations
                </svelte:fragment>

                <svelte:fragment slot="description">
                    These people have been invited to your team and have been
                    sent an invitation email. They may join the team by
                    accepting the email invitation.
                </svelte:fragment>

                <!-- Pending Team Member Invitation List -->
                <svelte:fragment slot="content">
                    <div class="space-y-6">
                        {#each team.team_invitations as invitation (invitation.id)}
                            <div class="flex items-center justify-between">
                                <div class="text-gray-600 dark:text-gray-400">
                                    {invitation.email}
                                </div>

                                <div class="flex items-center">
                                    <!-- Cancel Team Invitation -->
                                    {#if userPermissions.canRemoveTeamMembers}
                                        <button
                                            class="cursor-pointer ml-6 text-sm text-red-500 focus:outline-none"
                                            on:click={cancelTeamInvitation(
                                                invitation
                                            )}
                                        >
                                            Cancel
                                        </button>
                                    {/if}
                                </div>
                            </div>
                        {/each}
                    </div>
                </svelte:fragment>
            </ActionSection>
        </div>
    {/if}

    {#if team.users.length > 0}
        <div>
            <SectionBorder />

            <!-- Manage Team Members -->
            <ActionSection class="mt-10 sm:mt-0">
                <svelte:fragment slot="title">Team Members</svelte:fragment>

                <svelte:fragment slot="description">
                    All of the people that are part of this team.
                </svelte:fragment>

                <!-- Team Member List -->
                <svelte:fragment slot="content">
                    <div class="space-y-6">
                        {#each team.users as user (user.id)}
                            <div class="flex items-center justify-between">
                                <div class="flex items-center">
                                    <img
                                        class="w-8 h-8 rounded-full object-cover"
                                        src={user.profile_photo_url}
                                        alt={user.name}
                                    />
                                    <div class="ml-4 dark:text-white">
                                        {user.name}
                                    </div>
                                </div>

                                <div class="flex items-center">
                                    <!-- Manage Team Member Role -->
                                    {#if userPermissions.canUpdateTeamMembers && availableRoles.length}
                                        <button
                                            class="ml-2 text-sm text-gray-400 underline"
                                            on:click={manageRole(user)}
                                        >
                                            {displayableRole(
                                                user.membership.role
                                            )}
                                        </button>
                                    {:else if availableRoles.length}
                                        <div class="ml-2 text-sm text-gray-400">
                                            {displayableRole(
                                                user.membership.role
                                            )}
                                        </div>
                                    {/if}

                                    <!-- Leave Team -->
                                    {#if $page.props.auth.user.id === user.id}
                                        <button
                                            class="cursor-pointer ml-6 text-sm text-red-500"
                                            on:click={confirmLeavingTeam}
                                        >
                                            Leave
                                        </button>
                                    {:else if userPermissions.canRemoveTeamMembers}
                                        <!-- Remove Team Member -->
                                        <button
                                            class="cursor-pointer ml-6 text-sm text-red-500"
                                            on:click={confirmTeamMemberRemoval(
                                                user
                                            )}
                                        >
                                            Remove
                                        </button>
                                    {/if}
                                </div>
                            </div>
                        {/each}
                    </div>
                </svelte:fragment>
            </ActionSection>
        </div>
    {/if}

    <!-- Role Management Modal -->
    <DialogModal
        show={currentlyManagingRole}
        @close={(currentlyManagingRole = false)}
    >
        <svelte:fragment slot="title">Manage Role</svelte:fragment>

        <svelte:fragment slot="content">
            {#if managingRoleFor}
                <div>
                    <div
                        class="relative z-0 mt-1 border border-gray-200 dark:border-gray-700 rounded-lg cursor-pointer"
                    >
                        {#each availableRoles as role, i (role.key)}
                            <button
                                type="button"
                                class="relative px-4 py-3 inline-flex w-full rounded-lg focus:z-10 focus:outline-none focus:border-indigo-500 dark:focus:border-indigo-600 focus:ring-2 focus:ring-indigo-500 dark:focus:ring-indigo-600 {i >
                                0
                                    ? 'border-t border-gray-200 dark:border-gray-700 focus:border-none rounded-t-none'
                                    : ''}
                                {i != Object.keys(availableRoles).length - 1
                                    ? 'rounded-b-none'
                                    : ''}"
                                on:click={(updateRoleForm.role = role.key)}
                            >
                                <div
                                    class:opacity-25={updateRoleForm.role &&
                                        updateRoleForm.role !== role.key}
                                >
                                    <!-- Role Name -->
                                    <div class="flex items-center">
                                        <div
                                            class="text-sm text-gray-600 dark:text-gray-400"
                                            class:font-semibold={updateRoleForm.role ===
                                                role.key}
                                        >
                                            {role.name}
                                        </div>

                                        {#if updateRoleForm.role == role.key}
                                            <svg
                                                class="ml-2 h-5 w-5 text-green-400"
                                                xmlns="http://www.w3.org/2000/svg"
                                                fill="none"
                                                viewBox="0 0 24 24"
                                                stroke-width="1.5"
                                                stroke="currentColor"
                                            >
                                                <path
                                                    stroke-linecap="round"
                                                    stroke-linejoin="round"
                                                    d="M9 12.75L11.25 15 15 9.75M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                                                />
                                            </svg>
                                        {/if}
                                    </div>

                                    <!-- Role Description -->
                                    <div
                                        class="mt-2 text-xs text-gray-600 dark:text-gray-400"
                                    >
                                        {role.description}
                                    </div>
                                </div>
                            </button>
                        {/each}
                    </div>
                </div>
            {/if}
        </svelte:fragment>

        <svelte:fragment slot="footer">
            <SecondaryButton on:click={(currentlyManagingRole = false)}>
                Cancel
            </SecondaryButton>

            <PrimaryButton
                class="ml-3"
                class:opacity-25={updateRoleForm.processing}
                disabled={updateRoleForm.processing}
                on:click={updateRole}
            >
                Save
            </PrimaryButton>
        </svelte:fragment>
    </DialogModal>

    <!-- Leave Team Confirmation Modal -->
    <ConfirmationModal
        show={confirmingLeavingTeam}
        @close={(confirmingLeavingTeam = false)}
    >
        <svelte:fragment slot="title">Leave Team</svelte:fragment>

        <svelte:fragment slot="content">
            Are you sure you would like to leave this team?
        </svelte:fragment>

        <svelte:fragment slot="footer">
            Delete Account
            <SecondaryButton on:click={(confirmingLeavingTeam = false)}>
                Cancel
            </SecondaryButton>

            <DangerButton
                class="ml-3"
                class:opacity-25={leaveTeamForm.processing}
                disabled={leaveTeamForm.processing}
                on:click={leaveTeam}
            >
                Leave
            </DangerButton>
        </svelte:fragment>
    </ConfirmationModal>

    <!-- Remove Team Member Confirmation Modal -->
    <ConfirmationModal
        show={teamMemberBeingRemoved}
        @close={(teamMemberBeingRemoved = null)}
    >
        <svelte:fragment slot="title">Remove Team Member</svelte:fragment>

        <svelte:fragment slot="content">
            Are you sure you would like to remove this person from the team?
        </svelte:fragment>

        <svelte:fragment slot="footer">
            <SecondaryButton on:click={(teamMemberBeingRemoved = null)}>
                Cancel
            </SecondaryButton>

            <DangerButton
                class="ml-3"
                class:opacity-25={removeTeamMemberForm.processing}
                disabled={removeTeamMemberForm.processing}
                on:click={removeTeamMember}
            >
                Remove
            </DangerButton>
        </svelte:fragment>
    </ConfirmationModal>
</div>
