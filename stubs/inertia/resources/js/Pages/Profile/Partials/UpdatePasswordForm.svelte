<script>
    import { useForm } from "@inertiajs/svelte";

    import ActionMessage from "@/Components/ActionMessage.svelte";
    import FormSection from "@/Components/FormSection.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    let passwordInput = null,
        currentPasswordInput = null;

    const form = useForm({
        current_password: "",
        password: "",
        password_confirmation: "",
    });

    const updatePassword = () => {
        $form.put(route("user-password.update"), {
            errorBag: "updatePassword",
            preserveScroll: true,
            onSuccess: () => $form.reset(),
            onError: () => {
                if ($form.errors.password) {
                    $form.reset("password", "password_confirmation");
                    passwordInput.focus();
                }

                if ($form.errors.current_password) {
                    $form.reset("current_password");
                    currentPasswordInput.focus();
                }
            },
        });
    };
</script>

<!-- TODO:event -->
<FormSection @submitted="updatePassword">
    <svelte:fragment slot="title">Update Password</svelte:fragment>

    <svelte:fragment slot="description">
        Ensure your account is using a long, random password to stay secure.
    </svelte:fragment>

    <svelte:fragment slot="form">
        <div class="col-span-6 sm:col-span-4">
            <InputLabel for="current_password" value="Current Password" />
            <TextInput
                id="current_password"
                bind:this={currentPasswordInput}
                bind:value={$$form.current_password}
                type="password"
                class="mt-1 block w-full"
                autocomplete="current-password"
            />
            <InputError message={$$form.errors.current_password} class="mt-2" />
        </div>

        <div class="col-span-6 sm:col-span-4">
            <InputLabel for="password" value="New Password" />
            <TextInput
                id="password"
                bind:this={passwordInput}
                bind:value={$$form.password}
                type="password"
                class="mt-1 block w-full"
                autocomplete="new-password"
            />
            <InputError message={$$form.errors.password} class="mt-2" />
        </div>

        <div class="col-span-6 sm:col-span-4">
            <InputLabel for="password_confirmation" value="Confirm Password" />
            <TextInput
                id="password_confirmation"
                bind:value={$$form.password_confirmation}
                type="password"
                class="mt-1 block w-full"
                autocomplete="new-password"
            />
            <InputError
                message={$$form.errors.password_confirmation}
                class="mt-2"
            />
        </div>
    </svelte:fragment>

    <svelte:fragment slot="actions">
        <ActionMessage on={$$form.recentlySuccessful} class="mr-3">
            Saved.
        </ActionMessage>

        <PrimaryButton
            class:opacity-25={$$form.processing}
            disabled={$$form.processing}
        >
            Save
        </PrimaryButton>
    </svelte:fragment>
</FormSection>
