<script>
    import { router, useForm, usePage } from "@inertiajs/svelte";

    import ActionSection from "@/Components/ActionSection.svelte";
    import ConfirmsPassword from "@/Components/ConfirmsPassword.svelte";
    import DangerButton from "@/Components/DangerButton.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import SecondaryButton from "@/Components/SecondaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let requiresConfirmation = false;

    let enabling = false,
        confirming = false,
        disabling = false,
        qrCode = null,
        setupKey = null,
        recoveryCodes = [];

    const confirmationForm = useForm({
        code: "",
    });

    const twoFactorEnabled =
        enabling && usePage().props.auth.user?.two_factor_enabled;

    // watch(twoFactorEnabled, () => {
    //     if (! twoFactorEnabled) {
    //         confirmationForm.reset();
    //         confirmationForm.clearErrors();
    //     }
    // });

    const enableTwoFactorAuthentication = () => {
        enabling = true;

        router.post(
            route("two-factor.enable"),
            {},
            {
                preserveScroll: true,
                onSuccess: () =>
                    Promise.all([
                        showQrCode(),
                        showSetupKey(),
                        showRecoveryCodes(),
                    ]),
                onFinish: () => {
                    enabling = false;
                    confirming = props.requiresConfirmation;
                },
            }
        );
    };

    const showQrCode = () => {
        return axios.get(route("two-factor.qr-code")).then((response) => {
            qrCode = response.data.svg;
        });
    };

    const showSetupKey = () => {
        return axios.get(route("two-factor.secret-key")).then((response) => {
            setupKey = response.data.secretKey;
        });
    };

    const showRecoveryCodes = () => {
        return axios
            .get(route("two-factor.recovery-codes"))
            .then((response) => {
                recoveryCodes = response.data;
            });
    };

    const confirmTwoFactorAuthentication = () => {
        confirmationForm.post(route("two-factor.confirm"), {
            errorBag: "confirmTwoFactorAuthentication",
            preserveScroll: true,
            preserveState: true,
            onSuccess: () => {
                confirming = false;
                qrCode = null;
                setupKey = null;
            },
        });
    };

    const regenerateRecoveryCodes = () => {
        axios
            .post(route("two-factor.recovery-codes"))
            .then(() => showRecoveryCodes());
    };

    const disableTwoFactorAuthentication = () => {
        disabling = true;

        router.delete(route("two-factor.disable"), {
            preserveScroll: true,
            onSuccess: () => {
                disabling = false;
                confirming = false;
            },
        });
    };
</script>

<ActionSection>
    <svelte:fragment slot="title">Two Factor Authentication</svelte:fragment>

    <svelte:fragment slot="description">
        Add additional security to your account using two factor authentication.
    </svelte:fragment>

    <svelte:fragment slot="content">
        {#if twoFactorEnabled && !confirming}
            <h3 class="text-lg font-medium text-gray-900 dark:text-gray-100">
                You have enabled two factor authentication.
            </h3>
        {:else if twoFactorEnabled && confirming}
            <h3 class="text-lg font-medium text-gray-900 dark:text-gray-100">
                Finish enabling two factor authentication.
            </h3>
        {:else}
            <h3 class="text-lg font-medium text-gray-900 dark:text-gray-100">
                You have not enabled two factor authentication.
            </h3>
        {/if}

        <div class="mt-3 max-w-xl text-sm text-gray-600 dark:text-gray-400">
            <p>
                When two factor authentication is enabled, you will be prompted
                for a secure, random token during authentication. You may
                retrieve this token from your phone's Google Authenticator
                application.
            </p>
        </div>

        {#if twoFactorEnabled}
            <div>
                {#if qrCode}
                    <div>
                        <div
                            class="mt-4 max-w-xl text-sm text-gray-600 dark:text-gray-400"
                        >
                            {#if confirming}
                                <p class="font-semibold">
                                    To finish enabling two factor
                                    authentication, scan the following QR code
                                    using your phone's authenticator application
                                    or enter the setup key and provide the
                                    generated OTP code.
                                </p>
                            {:else}
                                <p>
                                    Two factor authentication is now enabled.
                                    Scan the following QR code using your
                                    phone's authenticator application or enter
                                    the setup key.
                                </p>
                            {/if}
                        </div>

                        <div class="mt-4 p-2 inline-block bg-white">
                            {@html qrCode}
                        </div>

                        {#if setupKey}
                            <div
                                class="mt-4 max-w-xl text-sm text-gray-600 dark:text-gray-400"
                            >
                                <p class="font-semibold">
                                    Setup Key: <span>{@html setupKey}</span>
                                </p>
                            </div>
                        {/if}

                        {#if confirming}
                            <div class="mt-4">
                                <InputLabel for="code" value="Code" />

                                <TextInput
                                    id="code"
                                    bind:value={confirmationForm.code}
                                    type="text"
                                    name="code"
                                    class="block mt-1 w-1/2"
                                    inputmode="numeric"
                                    autofocus
                                    autocomplete="one-time-code"
                                    on:keyup.enter={confirmTwoFactorAuthentication}
                                />

                                <InputError
                                    message={confirmationForm.errors.code}
                                    class="mt-2"
                                />
                            </div>
                        {/if}
                    </div>
                {/if}

                {#if recoveryCodes.length > 0 && !confirming}
                    <div
                        class="mt-4 max-w-xl text-sm text-gray-600 dark:text-gray-400"
                    >
                        <p class="font-semibold">
                            Store these recovery codes in a secure password
                            manager. They can be used to recover access to your
                            account if your two factor authentication device is
                            lost.
                        </p>
                    </div>

                    <div
                        class="grid gap-1 max-w-xl mt-4 px-4 py-4 font-mono text-sm bg-gray-100 dark:bg-gray-900 dark:text-gray-100 rounded-lg"
                    >
                        {#each recoveryCodes as code (code)}
                            <div>
                                {code}
                            </div>
                        {/each}
                    </div>
                {/if}
            </div>

            <div class="mt-5">
                {#if !twoFactorEnabled}
                    <div>
                        <ConfirmsPassword
                            @confirmed="enableTwoFactorAuthentication"
                        >
                            <PrimaryButton
                                type="button"
                                class:opacity-25={enabling}
                                disabled={enabling}
                            >
                                Enable
                            </PrimaryButton>
                        </ConfirmsPassword>
                    </div>
                {:else}
                    <div>
                        <ConfirmsPassword
                            @confirmed={confirmTwoFactorAuthentication}
                        >
                            {#if confirming}
                                <PrimaryButton
                                    type="button"
                                    class="mr-3"
                                    class:opacity-25={enabling}
                                    disabled={enabling}
                                >
                                    Confirm
                                </PrimaryButton>
                            {/if}
                        </ConfirmsPassword>

                        <ConfirmsPassword @confirmed="regenerateRecoveryCodes">
                            {#if recoveryCodes.length > 0 && !confirming}
                                <SecondaryButton class="mr-3">
                                    Regenerate Recovery Codes
                                </SecondaryButton>
                            {/if}
                        </ConfirmsPassword>

                        <!-- implement events for confirmed -->
                        <ConfirmsPassword @confirmed={showRecoveryCodes}>
                            {#if recoveryCodes.length === 0 && !confirming}
                                <SecondaryButton class="mr-3">
                                    Show Recovery Codes
                                </SecondaryButton>
                            {/if}
                        </ConfirmsPassword>

                        <ConfirmsPassword
                            @confirmed={disableTwoFactorAuthentication}
                        >
                            {#if confirming}
                                <SecondaryButton
                                    class:opacity-25={disabling}
                                    disabled={disabling}
                                >
                                    Cancel
                                </SecondaryButton>
                            {/if}
                        </ConfirmsPassword>

                        <ConfirmsPassword
                            @confirmed={disableTwoFactorAuthentication}
                        >
                            {#if !confirming}
                                <DangerButton
                                    class:opacity-25={disabling}
                                    disabled={disabling}
                                >
                                    Disable
                                </DangerButton>
                            {/if}
                        </ConfirmsPassword>
                    </div>
                {/if}
            </div>
        {/if}
    </svelte:fragment>
</ActionSection>
