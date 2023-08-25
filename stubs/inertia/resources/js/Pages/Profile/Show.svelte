<script>
    import AppLayout from "@/Layouts/AppLayout.svelte";
    import DeleteUserForm from "@/Pages/Profile/Partials/DeleteUserForm.svelte";
    import LogoutOtherBrowserSessionsForm from "@/Pages/Profile/Partials/LogoutOtherBrowserSessionsForm.svelte";
    import SectionBorder from "@/Components/SectionBorder.svelte";
    import TwoFactorAuthenticationForm from "@/Pages/Profile/Partials/TwoFactorAuthenticationForm.svelte";
    import UpdatePasswordForm from "@/Pages/Profile/Partials/UpdatePasswordForm.svelte";
    import UpdateProfileInformationForm from "@/Pages/Profile/Partials/UpdateProfileInformationForm.svelte";

    export let confirmsTwoFactorAuthentication = false,
        sessions = [];
</script>

<AppLayout title="Profile">
    <svelte:fragment slot="header">
        <h2
            class="font-semibold text-xl text-gray-800 dark:text-gray-200 leading-tight"
        >
            Profile
        </h2>
    </svelte:fragment>

    <div>
        <div class="max-w-7xl mx-auto py-10 sm:px-6 lg:px-8">
            {#if $page.props.jetstream.canUpdateProfileInformation}
                <div>
                    <UpdateProfileInformationForm
                        user={$page.props.auth.user}
                    />

                    <SectionBorder />
                </div>
            {/if}

            {#if $page.props.jetstream.canUpdatePassword}
                <div>
                    <UpdatePasswordForm class="mt-10 sm:mt-0" />

                    <SectionBorder />
                </div>
            {/if}

            {#if $page.props.jetstream.canManageTwoFactorAuthentication}
                <div>
                    <TwoFactorAuthenticationForm
                        requires-confirmation={confirmsTwoFactorAuthentication}
                        class="mt-10 sm:mt-0"
                    />

                    <SectionBorder />
                </div>
            {/if}

            <LogoutOtherBrowserSessionsForm
                sessions="sessions"
                class="mt-10 sm:mt-0"
            />

            {#if $page.props.jetstream.hasAccountDeletionFeatures}
                <SectionBorder />

                <DeleteUserForm class="mt-10 sm:mt-0" />
            {/if}
        </div>
    </div>
</AppLayout>
