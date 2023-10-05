<script>
    import { useForm } from "@inertiajs/svelte";
    import AuthenticationCard from "@/Components/AuthenticationCard.svelte";
    import AuthenticationCardLogo from "@/Components/AuthenticationCardLogo.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    const form = useForm({
        password: "",
    });

    let passwordInput;

    const submit = () => {
        $form.post(route("password.confirm"), {
            onFinish: () => {
                $form.reset();
                // TODO: work on this
                // passwordInput.value.focus();
            },
        });
    };
</script>

<svelte:head>
    <title>Secure Area</title>
</svelte:head>

<AuthenticationCard>
    <svelte:fragment slot="logo">
        <AuthenticationCardLogo />
    </svelte:fragment>

    <div class="mb-4 text-sm text-gray-600 dark:text-gray-400">
        This is a secure area of the application. Please confirm your password
        before continuing.
    </div>

    <form on:submit|preventDefault={submit}>
        <div>
            <InputLabel for="password" value="Password" />
            <TextInput
                id="password"
                bind:this={passwordInput}
                bind:value={$form.password}
                type="password"
                class="mt-1 block w-full"
                required
                autocomplete="current-password"
                autofocus
            />
            <InputError class="mt-2" message={$form.errors.password} />
        </div>

        <div class="flex justify-end mt-4">
            <PrimaryButton
                class="ml-4 {$form.processing ? 'opacity-25' : ''}"
                disabled={$form.processing}
            >
                Confirm
            </PrimaryButton>
        </div>
    </form>
</AuthenticationCard>
