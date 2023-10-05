<script>
    import { Link, router, useForm } from "@inertiajs/svelte";

    import ActionMessage from "@/Components/ActionMessage.svelte";
    import FormSection from "@/Components/FormSection.svelte";
    import InputError from "@/Components/InputError.svelte";
    import InputLabel from "@/Components/InputLabel.svelte";
    import PrimaryButton from "@/Components/PrimaryButton.svelte";
    import SecondaryButton from "@/Components/SecondaryButton.svelte";
    import TextInput from "@/Components/TextInput.svelte";

    export let user;

    const form = useForm({
        _method: "PUT",
        name: user.name,
        email: user.email,
        photo: null,
    });

    let verificationLinkSent = null,
        photoPreview = null,
        photoInput = null;

    const updateProfileInformation = () => {
        if (photoInput.value) {
            $form.photo = photoInput.value.files[0];
        }

        $form.post(route("user-profile-information.update"), {
            errorBag: "updateProfileInformation",
            preserveScroll: true,
            onSuccess: () => clearPhotoFileInput(),
        });
    };

    const sendEmailVerification = () => {
        verificationLinkSent = true;
    };

    const selectNewPhoto = () => {
        photoInput.click();
    };

    const updatePhotoPreview = () => {
        const photo = photoInput.files[0];

        if (!photo) return;

        const reader = new FileReader();

        reader.onload = (e) => {
            photoPreview = e.target.result;
        };

        reader.readAsDataURL(photo);
    };

    const deletePhoto = () => {
        router.delete(route("current-user-photo.destroy"), {
            preserveScroll: true,
            onSuccess: () => {
                photoPreview.value = null;
                clearPhotoFileInput();
            },
        });
    };

    const clearPhotoFileInput = () => {
        if (photoInput?.value) {
            photoInput.value = null;
        }
    };
</script>

<FormSection @submitted={updateProfileInformation}>
    <svelte:fragment slot="title">Profile Information</svelte:fragment>

    <svelte:fragment slot="description">
        Update your account's profile information and email address.
    </svelte:fragment>

    <svelte:fragment slot="form">
        <!-- Profile Photo -->
        {#if $page.props.jetstream.managesProfilePhotos}
            <div class="col-span-6 sm:col-span-4">
                <!-- Profile Photo File Input -->
                <input
                    bind:this={photoInput}
                    type="file"
                    class="hidden"
                    on:change={updatePhotoPreview}
                />

                <InputLabel for="photo" value="Photo" />

                <!-- Current Profile Photo -->
                {#if !photoPreview}
                    <div class="mt-2">
                        <img
                            src={user.profile_photo_url}
                            alt={user.name}
                            class="rounded-full h-20 w-20 object-cover"
                        />
                    </div>
                {/if}

                <!-- New Profile Photo Preview -->
                {#if photoPreview}
                    <div class="mt-2">
                        <span
                            class="block rounded-full w-20 h-20 bg-cover bg-no-repeat bg-center"
                            style:background-image={url(photoPreview)}
                        />
                    </div>
                {/if}

                <SecondaryButton
                    class="mt-2 mr-2"
                    type="button"
                    on:click|preventDefault={selectNewPhoto}
                >
                    Select A New Photo
                </SecondaryButton>

                {#if user.profile_photo_path}
                    <SecondaryButton
                        type="button"
                        class="mt-2"
                        on:click|preventDefault={deletePhoto}
                    >
                        Remove Photo
                    </SecondaryButton>
                {/if}

                <InputError message={$form.errors.photo} class="mt-2" />
            </div>
        {/if}

        <!-- Name -->
        <div class="col-span-6 sm:col-span-4">
            <InputLabel for="name" value="Name" />
            <TextInput
                id="name"
                bind:value={$form.name}
                type="text"
                class="mt-1 block w-full"
                required
                autocomplete="name"
            />
            <InputError message={$form.errors.name} class="mt-2" />
        </div>

        <!-- Email -->
        <div class="col-span-6 sm:col-span-4">
            <InputLabel for="email" value="Email" />
            <TextInput
                id="email"
                bind:value={$form.email}
                type="email"
                class="mt-1 block w-full"
                required
                autocomplete="username"
            />
            <InputError message={$form.errors.email} class="mt-2" />

            {#if $page.props.jetstream.hasEmailVerification && user.email_verified_at === null}
                <div>
                    <p class="text-sm mt-2 dark:text-white">
                        Your email address is unverified.

                        <Link
                            href={route("verification.send")}
                            method="post"
                            as="button"
                            class="underline text-sm text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-gray-100 rounded-md focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 dark:focus:ring-offset-gray-800"
                            on:click|preventDefault={sendEmailVerification}
                        >
                            Click here to re-send the verification email.
                        </Link>
                    </p>

                    {#if verificationLinkSent}
                        <div
                            class="mt-2 font-medium text-sm text-green-600 dark:text-green-400"
                        >
                            A new verification link has been sent to your email
                            address.
                        </div>
                    {/if}
                </div>
            {/if}
        </div>
    </svelte:fragment>

    <svelte:fragment slot="actions">
        <ActionMessage on={$form.recentlySuccessful} class="mr-3">
            Saved.
        </ActionMessage>

        <PrimaryButton
            class={$form.processing ? "opacity-25" : ""}
            disabled={$form.processing}
        >
            Save
        </PrimaryButton>
    </svelte:fragment>
</FormSection>
