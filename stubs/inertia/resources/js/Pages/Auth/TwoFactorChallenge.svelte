<script>
    import { nextTick, ref } from "vue";
    import { useForm } from "@inertiajs/svelte";
    import AuthenticationCard from "@/Components/AuthenticationCard.svelte";
    import AuthenticationCardLogo from "@/Components/AuthenticationCardLogo.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    // TODO: work on these comments
    // const recovery = ref(false);

    const form = useForm({
        code: "",
        recovery_code: "",
    });

    // const recoveryCodeInput = ref(null);
    // const codeInput = ref(null);

    // const toggleRecovery = async () => {
    //     recovery.value ^= true;

    //     await nextTick();

    //     if (recovery.value) {
    //         recoveryCodeInput.value.focus();
    //         $form.code = "";
    //     } else {
    //         codeInput.value.focus();
    //         $form.recovery_code = "";
    //     }
    // };

    const submit = () => {
        $form.post(route("two-factor.login"));
    };
</script>

<svelte:head>
    <title>Two-factor Confirmation</title>
</svelte:head>

<AuthenticationCard>
    <svelte:fragment slot="logo">
        <AuthenticationCardLogo />
    </svelte:fragment>

    <div class="mb-4 text-sm text-gray-600 dark:text-gray-400">
        {#if !recovery}
            Please confirm access to your account by entering the authentication
            code provided by your authenticator application.
        {:else}
            Please confirm access to your account by entering one of your
            emergency recovery codes.
        {/if}
    </div>

    <form on:submit|preventDefault={submit}>
        {#if !recovery}
            <div>
                <InputLabel for="code" value="Code" />
                <TextInput
                    id="code"
                    ref="codeInput"
                    bind:value={$$form.code}
                    type="text"
                    inputmode="numeric"
                    class="mt-1 block w-full"
                    autofocus
                    autocomplete="one-time-code"
                />
                <InputError class="mt-2" message={$$form.errors.code} />
            </div>
        {:else}
            <div>
                <InputLabel for="recovery_code" value="Recovery Code" />
                <TextInput
                    id="recovery_code"
                    ref="recoveryCodeInput"
                    bind:value={$$form.recovery_code}
                    type="text"
                    class="mt-1 block w-full"
                    autocomplete="one-time-code"
                />
                <InputError class="mt-2" message={$$form.errors.recovery_code} />
            </div>
        {/if}

        <div class="flex items-center justify-end mt-4">
            <button
                type="button"
                class="text-sm text-gray-600 dark:text-gray-400 hover:text-gray-900 underline cursor-pointer"
                on:click|preventDefault={toggleRecovery}
            >
                {#if !recovery}
                    Use a recovery code
                {:else}
                    Use an authentication code
                {/if}
            </button>

            <PrimaryButton
                class="ml-4"
                class:opacity-25={$$form.processing}
                disabled={$$form.processing}
            >
                Log in
            </PrimaryButton>
        </div>
    </form>
</AuthenticationCard>
