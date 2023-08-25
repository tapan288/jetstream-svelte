<script>
    import { useForm } from "@inertiajs/svelte";
    import AuthenticationCard from "@/Components/AuthenticationCard.svelte";
    import AuthenticationCardLogo from "@/Components/AuthenticationCardLogo.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let email, token;

    const form = useForm({
        token: token,
        email: email,
        password: "",
        password_confirmation: "",
    });

    const submit = () => {
        $form.post(route("password.update"), {
            onFinish: () => $form.reset("password", "password_confirmation"),
        });
    };
</script>

<svelte:head>
    <title>Reset Password</title>
</svelte:head>

<AuthenticationCard>
    <svelte:fragment slot="logo">
        <AuthenticationCardLogo />
    </svelte:fragment>

    <$form on:submit|preventDefault={submit}>
        <div>
            <InputLabel for="email" value="Email" />
            <TextInput
                id="email"
                bind:value={$$form.email}
                type="email"
                class="mt-1 block w-full"
                required
                autofocus
                autocomplete="username"
            />
            <InputError class="mt-2" message={$$form.errors.email} />
        </div>

        <div class="mt-4">
            <InputLabel for="password" value="Password" />
            <TextInput
                id="password"
                bind:value={$$form.password}
                type="password"
                class="mt-1 block w-full"
                required
                autocomplete="new-password"
            />
            <InputError class="mt-2" message={$$form.errors.password} />
        </div>

        <div class="mt-4">
            <InputLabel for="password_confirmation" value="Confirm Password" />
            <TextInput
                id="password_confirmation"
                bind:value={$$form.password_confirmation}
                type="password"
                class="mt-1 block w-full"
                required
                autocomplete="new-password"
            />
            <InputError
                class="mt-2"
                message={$$form.errors.password_confirmation}
            />
        </div>

        <div class="flex items-center justify-end mt-4">
            <PrimaryButton
                class:opacity-25={$$form.processing}
                disabled={$$form.processing}
            >
                Reset Password
            </PrimaryButton>
        </div>
    </form>
</AuthenticationCard>
