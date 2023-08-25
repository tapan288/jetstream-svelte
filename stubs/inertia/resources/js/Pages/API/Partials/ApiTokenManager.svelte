<script>
    import { useForm } from "@inertiajs/svelte";

    import ActionMessage from "@/Components/ActionMessage.svelte";
    import ActionSection from "@/Components/ActionSection.svelte";
    import Checkbox from "@/Components/Checkbox.svelte";
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

    export let tokens, availablePermissions, defaultPermissions;

    let displayingToken, managingPermissionsFor, apiTokenBeingDeleted;

    const createApiTokenForm = useForm({
        name: "",
        permissions: defaultPermissions,
    });

    const updateApiTokenForm = useForm({
        permissions: [],
    });

    const deleteApiTokenForm = useForm({});

    const createApiToken = () => {
        createApiTokenForm.post(route("api-tokens.store"), {
            preserveScroll: true,
            onSuccess: () => {
                displayingToken = true;
                createApiTokenForm.reset();
            },
        });
    };

    const manageApiTokenPermissions = (token) => {
        updateApiTokenForm.permissions = token.abilities;
        managingPermissionsFor = token;
    };

    const updateApiToken = () => {
        updateApiTokenForm.put(
            route("api-tokens.update", managingPermissionsFor),
            {
                preserveScroll: true,
                preserveState: true,
                onSuccess: () => (managingPermissionsFor = null),
            }
        );
    };

    const confirmApiTokenDeletion = (token) => {
        apiTokenBeingDeleted = token;
    };

    const deleteApiToken = () => {
        deleteApiTokenForm.delete(
            route("api-tokens.destroy", apiTokenBeingDeleted),
            {
                preserveScroll: true,
                preserveState: true,
                onSuccess: () => (apiTokenBeingDeleted = null),
            }
        );
    };
</script>

<!-- TODO: fix issues if any -->
<div>
    <!-- Generate API Token -->
    <FormSection on:submit={createApiToken}>
        <svelte:fragment slot="title">Create API Token</svelte:fragment>

        <svelte:fragment slot="description">
            API tokens allow third-party services to authenticate with our
            application on your behalf.
        </svelte:fragment>

        <svelte:fragment slot="form">
            <!-- Token Name -->
            <div class="col-span-6 sm:col-span-4">
                <InputLabel for="name" value="Name" />
                <TextInput
                    id="name"
                    bind:value={createApiTokenForm.name}
                    type="text"
                    class="mt-1 block w-full"
                    autofocus
                />
                <InputError
                    message={createApiTokenForm.errors.name}
                    class="mt-2"
                />
            </div>

            <!-- Token Permissions -->
            {#if availablePermissions.length > 0}
                <div class="col-span-6">
                    <InputLabel for="permissions" value="Permissions" />
                    <div class="mt-2 grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div
                            v-for="permission in availablePermissions"
                            :key="permission"
                        >
                            <label class="flex items-center">
                                <Checkbox
                                    bind:checked={createApiTokenForm.permissions}
                                    value={permission}
                                />
                                <span
                                    class="ml-2 text-sm text-gray-600 dark:text-gray-400"
                                    >{permission}</span
                                >
                            </label>
                        </div>
                    </div>
                </div>
            {/if}
        </svelte:fragment>

        <svelte:fragment slot="actions">
            <ActionMessage
                on={createApiTokenForm.recentlySuccessful}
                class="mr-3"
            >
                Created.
            </ActionMessage>

            <PrimaryButton
                class={createApiTokenForm.processing ? "opacity-25" : ""}
                disabled={createApiTokenForm.processing}
            >
                Create
            </PrimaryButton>
        </svelte:fragment>
    </FormSection>

    {#if tokens.length > 0}
        <div>
            <SectionBorder />

            <!-- Manage API Tokens -->
            <div class="mt-10 sm:mt-0">
                <ActionSection>
                    <svelte:fragment slot="title">
                        Manage API Tokens
                    </svelte:fragment>

                    <svelte:fragment slot="description">
                        You may delete any of your existing tokens if they are
                        no longer needed.
                    </svelte:fragment>

                    <!-- API Token List -->
                    <svelte:fragment slot="content">
                        <div class="space-y-6">
                            {#each tokens as token (token.id)}
                                <div class="flex items-center justify-between">
                                    <div class="break-all dark:text-white">
                                        {token.name}
                                    </div>

                                    <div class="flex items-center ml-2">
                                        {#if token.last_used_ago}
                                            <div class="text-sm text-gray-400">
                                                Last used {token.last_used_ago}
                                            </div>
                                        {/if}

                                        {#if availablePermissions.length > 0}
                                            <button
                                                class="cursor-pointer ml-6 text-sm text-gray-400 underline"
                                                on:click={manageApiTokenPermissions(
                                                    token
                                                )}
                                            >
                                                Permissions
                                            </button>
                                        {/if}

                                        <button
                                            class="cursor-pointer ml-6 text-sm text-red-500"
                                            on:click={confirmApiTokenDeletion(
                                                token
                                            )}
                                        >
                                            Delete
                                        </button>
                                    </div>
                                </div>
                            {/each}
                        </div>
                    </svelte:fragment>
                </ActionSection>
            </div>
        </div>
    {/if}

    <!-- Token Value Modal -->
    <DialogModal show={displayingToken} close={(displayingToken = false)}>
        <svelte:fragment slot="title">API Token</svelte:fragment>

        <svelte:fragment slot="content">
            <div>
                Please copy your new API token. For your security, it won't be
                shown again.
            </div>

            {#if $page.props.jetstream.flash.token}
                <div
                    class="mt-4 bg-gray-100 dark:bg-gray-900 px-4 py-2 rounded font-mono text-sm text-gray-500 break-all"
                >
                    {$page.props.jetstream.flash.token}
                </div>
            {/if}
        </svelte:fragment>

        <svelte:fragment slot="footer">
            <SecondaryButton on:click={(displayingToken = false)}>
                Close
            </SecondaryButton>
        </svelte:fragment>
    </DialogModal>

    <!-- API Token Permissions Modal -->
    <DialogModal
        show={managingPermissionsFor != null}
        close={(managingPermissionsFor = null)}
    >
        <svelte:fragment slot="title">API Token Permissions</svelte:fragment>

        <svelte:fragment slot="content">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                {#each availablePermissions as permission (permission)}
                    <div key="permission">
                        <label class="flex items-center">
                            <Checkbox
                                bind:checked={updateApiTokenForm.permissions}
                                value={permission}
                            />
                            <span
                                class="ml-2 text-sm text-gray-600 dark:text-gray-400"
                                >{permission}</span
                            >
                        </label>
                    </div>
                {/each}
            </div>
        </svelte:fragment>

        <svelte:fragment slot="footer">
            <SecondaryButton
                on:click={() => {
                    managingPermissionsFor = null;
                }}
            >
                Cancel
            </SecondaryButton>

            <PrimaryButton
                class="ml-3"
                class:opacity-25={updateApiTokenForm.processing}
                disabled={updateApiTokenForm.processing}
                on:click={updateApiToken}
            >
                Save
            </PrimaryButton>
        </svelte:fragment>
    </DialogModal>

    <!-- Delete Token Confirmation Modal -->
    <ConfirmationModal
        show={apiTokenBeingDeleted != null}
        close={(apiTokenBeingDeleted = null)}
    >
        <svelte:fragment slot="title">Delete API Token</svelte:fragment>

        <svelte:fragment slot="content">
            Are you sure you would like to delete this API token?
        </svelte:fragment>

        <svelte:fragment slot="footer">
            <SecondaryButton on:click={(apiTokenBeingDeleted = null)}>
                Cancel
            </SecondaryButton>

            <DangerButton
                class="ml-3"
                class:opacity-25={updateApiTokenForm.processing}
                disabled={deleteApiTokenForm.processing}
                on:click={deleteApiToken}
            >
                Delete
            </DangerButton>
        </svelte:fragment>
    </ConfirmationModal>
</div>
