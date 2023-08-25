<script>
    import { useForm } from "@inertiajs/svelte";
    import ActionSection from "@/Components/ActionSection.svelte";
    import DangerButton from "@/Components/DangerButton.svelte";
    import DialogModal from "@/Components/DialogModal.svelte";
    import InputError from "@/Components/InputError.svelte";
    import SecondaryButton from "@/Components/SecondaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    let confirmingUserDeletion = false;

    let passwordInput;

    const form = useForm({
        password: "",
    });

    const confirmUserDeletion = () => {
        confirmingUserDeletion = true;

        setTimeout(() => passwordInput.focus(), 250);
    };

    const deleteUser = () => {
        $form.delete(route("current-user.destroy"), {
            preserveScroll: true,
            onSuccess: () => closeModal(),
            onError: () => passwordInput.focus(),
            onFinish: () => $form.reset(),
        });
    };

    const closeModal = () => {
        confirmingUserDeletion = false;

        $form.reset();
    };
</script>

<ActionSection>
    <svelte:fragment slot="title">Delete Account</svelte:fragment>

    <svelte:fragment slot="description">
        Permanently delete your account.
    </svelte:fragment>

    <svelte:fragment slot="content">
        <div class="max-w-xl text-sm text-gray-600 dark:text-gray-400">
            Once your account is deleted, all of its resources and data will be
            permanently deleted. Before deleting your account, please download
            any data or information that you wish to retain.
        </div>

        <div class="mt-5">
            <DangerButton on:click={confirmUserDeletion}>
                Delete Account
            </DangerButton>
        </div>

        <!-- Delete Account Confirmation Modal -->
        <DialogModal show="confirmingUserDeletion" @onClose={closeModal}>
            <svelte:fragment slot="title">Delete Account</svelte:fragment>

            <svelte:fragment slot="content">
                Are you sure you want to delete your account? Once your account
                is deleted, all of its resources and data will be permanently
                deleted. Please enter your password to confirm you would like to
                permanently delete your account.

                <div class="mt-4">
                    <TextInput
                        bind:this={passwordInput}
                        bind:value={$$form.password}
                        type="password"
                        class="mt-1 block w-3/4"
                        placeholder="Password"
                        autocomplete="current-password"
                        on:keyup.enter={deleteUser}
                    />

                    <InputError message={$$form.errors.password} class="mt-2" />
                </div>
            </svelte:fragment>

            <svelte:fragment slot="footer">
                <SecondaryButton on:click={closeModal}>Cancel</SecondaryButton>

                <DangerButton
                    class="ml-3"
                    class:opacity-25={$form.processing}
                    disabled={$$form.processing}
                    on:click={deleteUser}
                >
                    Delete Account
                </DangerButton>
            </svelte:fragment>
        </DialogModal>
    </svelte:fragment>
</ActionSection>
