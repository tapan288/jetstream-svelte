<script>
    import { Link, useForm } from "@inertiajs/svelte";
    import AuthenticationCard from "@/Components/AuthenticationCard.svelte";
    import AuthenticationCardLogo from "@/Components/AuthenticationCardLogo.svelte";
    import Checkbox from "@/Components/Checkbox.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let canResetPassword,
        status = "";

    const form = useForm({
        email: "",
        password: "",
        remember: false,
    });

    const submit = () => {
        $form
            .transform((data) => ({
                ...data,
                remember: $form.remember ? "on" : "",
            }))
            .post(route("login"), {
                onFinish: () => $form.reset("password"),
            });
    };
</script>

<svelte:head>
    <title>Log in</title>
</svelte:head>

<AuthenticationCard>
    <svelte:fragment slot="logo">
        <AuthenticationCardLogo />
    </svelte:fragment>

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
                autocomplete="current-password"
            />
            <InputError class="mt-2" message={$$form.errors.password} />
        </div>

        <div class="block mt-4">
            <label class="flex items-center">
                <Checkbox bind:checked={$$form.remember} name="remember" />
                <span class="ml-2 text-sm text-gray-600 dark:text-gray-400"
                    >Remember me</span
                >
            </label>
        </div>

        <div class="flex items-center justify-end mt-4">
            {#if canResetPassword}
                <Link
                    href={route("password.request")}
                    class="underline text-sm text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-gray-100 rounded-md focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 dark:focus:ring-offset-gray-800"
                >
                    Forgot your password?
                </Link>
            {/if}

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
