<script>
    import { useForm } from "@inertiajs/svelte";

    import DialogModal from "./DialogModal.svelte";
    import InputError from "./InputError.svelte";
    import PrimaryButton from "./PrimaryButton.svelte";
    import SecondaryButton from "./SecondaryButton.svelte";
    import TextInput from "./TextInput.svelte";

    // const emit = defineEmits(['confirmed']);

    // TODO: work on events

    export let title = "Confirm Password",
        content =
            "For your security, please confirm your password to continue.",
        button = "Confirm";

    let confirmingPassword = false,
        passwordInput = null;

    const form = useForm({
        password: "",
        error: "",
        processing: false,
    });

    const startConfirmingPassword = () => {
        axios.get(route("password.confirmation")).then((response) => {
            if (response.data.confirmed) {
                emit("confirmed");
            } else {
                confirmingPassword = true;

                setTimeout(() => passwordInput.focus(), 250);
            }
        });
    };

    const confirmPassword = () => {
        $form.processing = true;

        axios
            .post(route("password.confirm"), {
                password: $form.password,
            })
            .then(() => {
                $form.processing = false;

                closeModal();
                // nextTick().then(() => emit('confirmed'));
            })
            .catch((error) => {
                $form.processing = false;
                $form.error = error.response.data.errors.password[0];
                passwordInput.focus();
            });
    };

    const closeModal = () => {
        confirmingPassword = false;
        $form.password = "";
        $form.error = "";
    };
</script>

<span>
    <span on:click={startConfirmingPassword}>
        <slot />
    </span>

    <DialogModal show={confirmingPassword} @close="closeModal">
        <svelte:fragment slot="title">
            {title}
        </svelte:fragment>

        <svelte:fragment slot="content">
            {content}

            <div class="mt-4">
                <TextInput
                    bind:this={passwordInput}
                    bind:value={$form.password}
                    type="password"
                    class="mt-1 block w-3/4"
                    placeholder="Password"
                    autocomplete="current-password"
                    on:keyup.enter={confirmPassword}
                />

                <InputError message={$form.error} class="mt-2" />
            </div>
        </svelte:fragment>

        <svelte:fragment slot="footer">
            <SecondaryButton on:click={closeModal}>Cancel</SecondaryButton>

            <PrimaryButton
                class="ml-3"
                class:opacity-25={$form.processing}
                disabled={$form.processing}
                on:click={confirmPassword}
            >
                {button}
            </PrimaryButton>
        </svelte:fragment>
    </DialogModal>
</span>
