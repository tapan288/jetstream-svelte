<script>
    import { useForm } from "@inertiajs/svelte";
    import ActionMessage from "@/Components/ActionMessage.svelte";
    import ActionSection from "@/Components/ActionSection.svelte";
    import DialogModal from "@/Components/DialogModal.svelte";
    import InputError from "@/Components/InputError.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import SecondaryButton from "@/Components/SecondaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let sessions = [];

    let confirmingLogout = false;
    let passwordInput = null;

    const form = useForm({
        password: "",
    });

    const confirmLogout = () => {
        confirmingLogout = true;

        setTimeout(() => passwordInput.focus(), 250);
    };

    const logoutOtherBrowserSessions = () => {
        $form.delete(route("other-browser-sessions.destroy"), {
            preserveScroll: true,
            onSuccess: () => closeModal(),
            onError: () => passwordInput.focus(),
            onFinish: () => $form.reset(),
        });
    };

    const closeModal = () => {
        confirmingLogout = false;

        $form.reset();
    };
</script>

<ActionSection>
    <svelte:fragment slot="title">Browser Sessions</svelte:fragment>

    <svelte:fragment slot="description">
        Manage and log out your active sessions on other browsers and devices.
    </svelte:fragment>

    <svelte:fragment slot="content">
        <div class="max-w-xl text-sm text-gray-600 dark:text-gray-400">
            If necessary, you may log out of all of your other browser sessions
            across all of your devices. Some of your recent sessions are listed
            below; however, this list may not be exhaustive. If you feel your
            account has been compromised, you should also update your password.
        </div>

        <!-- Other Browser Sessions -->
        {#if sessions.length > 0}
            <div class="mt-5 space-y-6">
                {#each sessions as session, i (i)}
                    <div class="flex items-center">
                        <div>
                            {#if session.agent.is_desktop}
                                <svg
                                    class="w-8 h-8 text-gray-500"
                                    xmlns="http://www.w3.org/2000/svg"
                                    fill="none"
                                    viewBox="0 0 24 24"
                                    stroke-width="1.5"
                                    stroke="currentColor"
                                >
                                    <path
                                        stroke-linecap="round"
                                        stroke-linejoin="round"
                                        d="M9 17.25v1.007a3 3 0 01-.879 2.122L7.5 21h9l-.621-.621A3 3 0 0115 18.257V17.25m6-12V15a2.25 2.25 0 01-2.25 2.25H5.25A2.25 2.25 0 013 15V5.25m18 0A2.25 2.25 0 0018.75 3H5.25A2.25 2.25 0 003 5.25m18 0V12a2.25 2.25 0 01-2.25 2.25H5.25A2.25 2.25 0 013 12V5.25"
                                    />
                                </svg>
                            {:else}
                                <svg
                                    class="w-8 h-8 text-gray-500"
                                    xmlns="http://www.w3.org/2000/svg"
                                    fill="none"
                                    viewBox="0 0 24 24"
                                    stroke-width="1.5"
                                    stroke="currentColor"
                                >
                                    <path
                                        stroke-linecap="round"
                                        stroke-linejoin="round"
                                        d="M10.5 1.5H8.25A2.25 2.25 0 006 3.75v16.5a2.25 2.25 0 002.25 2.25h7.5A2.25 2.25 0 0018 20.25V3.75a2.25 2.25 0 00-2.25-2.25H13.5m-3 0V3h3V1.5m-3 0h3m-3 18.75h3"
                                    />
                                </svg>
                            {/if}
                        </div>

                        <div class="ml-3">
                            <div
                                class="text-sm text-gray-600 dark:text-gray-400"
                            >
                                {session.agent.platform
                                    ? session.agent.platform
                                    : "Unknown"} - {session.agent.browser
                                    ? session.agent.browser
                                    : "Unknown"}
                            </div>

                            <div>
                                <div class="text-xs text-gray-500">
                                    {session.ip_address},

                                    {#if session.is_current_device}
                                        <span
                                            class="text-green-500 font-semibold"
                                            >This device</span
                                        >
                                    {:else}
                                        <span
                                            >Last active {session.last_active}</span
                                        >
                                    {/if}
                                </div>
                            </div>
                        </div>
                    </div>
                {/each}
            </div>
        {/if}

        <div class="flex items-center mt-5">
            <PrimaryButton on:click={confirmLogout}>
                Log Out Other Browser Sessions
            </PrimaryButton>

            <ActionMessage on={$$form.recentlySuccessful} class="ml-3">
                Done.
            </ActionMessage>
        </div>

        <!-- Log Out Other Devices Confirmation Modal -->
        <DialogModal show={confirmingLogout} onClose={closeModal}>
            <svelte:fragment slot="title">
                Log Out Other Browser Sessions
            </svelte:fragment>

            <svelte:fragment slot="content">
                Please enter your password to confirm you would like to log out
                of your other browser sessions across all of your devices.

                <div class="mt-4">
                    <TextInput
                        bind:this={passwordInput}
                        bind:value={$$form.password}
                        type="password"
                        class="mt-1 block w-3/4"
                        placeholder="Password"
                        autocomplete="current-password"
                        on:keyup.enter={logoutOtherBrowserSessions}
                    />

                    <InputError message={$$form.errors.password} class="mt-2" />
                </div>
            </svelte:fragment>

            <svelte:fragment slot="footer">
                <SecondaryButton on:click={closeModal}>Cancel</SecondaryButton>

                <PrimaryButton
                    class="ml-3"
                    class:opacity-25={$$form.processing}
                    disabled={$$form.processing}
                    on:click={logoutOtherBrowserSessions}
                >
                    Log Out Other Browser Sessions
                </PrimaryButton>
            </svelte:fragment>
        </DialogModal>
    </svelte:fragment>
</ActionSection>
