<script>
    import { useForm } from "@inertiajs/svelte";
    import AuthenticationCard from "@/Components/AuthenticationCard.svelte";
    import AuthenticationCardLogo from "@/Components/AuthenticationCardLogo.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let status = "";

    const form = useForm({
        email: "",
    });

    const submit = () => {
        $form.post(route("password.email"));
    };
</script>

<svelte:head>
    <title>Forgot Password</title>
</svelte:head>

<AuthenticationCard>
    <svelte:fragment slot="logo">
        <AuthenticationCardLogo />
    </svelte:fragment>

    <div class="mb-4 text-sm text-gray-600 dark:text-gray-400">
        Forgot your password? No problem. Just let us know your email address
        and we will email you a password reset link that will allow you to
        choose a new one.
    </div>

    {#if status}
        <div
            class="mb-4 font-medium text-sm text-green-600 dark:text-green-400"
        >
            {status}
        </div>
    {/if}

    <form on:submit|preventDefault={submit}>
        <div>
            <InputLabel for="email" value="Email" />
            <TextInput
                id="email"
                bind:value={$form.email}
                type="email"
                class="mt-1 block w-full"
                required
                autofocus
                autocomplete="username"
            />
            <InputError class="mt-2" message={$form.errors.email} />
        </div>

        <div class="flex items-center justify-end mt-4">
            <PrimaryButton
                class={$form.processing ? "opacity-25" : ""}
                disabled={$form.processing}
            >
                Email Password Reset Link
            </PrimaryButton>
        </div>
    </form>
</AuthenticationCard>
