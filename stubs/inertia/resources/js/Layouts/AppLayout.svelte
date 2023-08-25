<script>
    import { Link, router, page } from "@inertiajs/svelte";
    import ApplicationMark from "@/Components/ApplicationMark.svelte";
    import Banner from "@/Components/Banner.svelte";
    import Dropdown from "@/Components/Dropdown.svelte";
    import DropdownLink from "@/Components/DropdownLink.svelte";
    import NavLink from "@/Components/NavLink.svelte";
    import ResponsiveNavLink from "@/Components/ResponsiveNavLink.svelte";

    // defineProps({
    //     title: String,
    // });

    // const showingNavigationDropdown = ref(false);

    const switchToTeam = (team) => {
        router.put(
            route("current-team.update"),
            {
                team_id: team.id,
            },
            {
                preserveState: false,
            }
        );
    };

    const logout = () => {
        router.post(route("logout"));
    };
</script>

<div>
    <Banner />

    <div class="min-h-screen bg-gray-100 dark:bg-gray-900">
        <nav
            class="bg-white dark:bg-gray-800 border-b border-gray-100 dark:border-gray-700"
        >
            <!-- Primary Navigation Menu -->
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div class="flex justify-between h-16">
                    <div class="flex">
                        <!-- Logo -->
                        <div class="shrink-0 flex items-center">
                            <Link href={route("dashboard")}>
                                <ApplicationMark class="block h-9 w-auto" />
                            </Link>
                        </div>

                        <!-- Navigation Links -->
                        <div
                            class="hidden space-x-8 sm:-my-px sm:ml-10 sm:flex"
                        >
                            <NavLink
                                href={route("dashboard")}
                                active={route().current("dashboard")}
                            >
                                Dashboard
                            </NavLink>
                        </div>
                    </div>

                    <div class="hidden sm:flex sm:items-center sm:ml-6">
                        <div class="ml-3 relative">
                            <!-- Teams Dropdown -->
                            {#if $page.props.jetstream.hasTeamFeatures}
                                <Dropdown align="right" width="60">
                                    <svelte:fragment slot="trigger">
                                        <span class="inline-flex rounded-md">
                                            <button
                                                type="button"
                                                class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md text-gray-500 dark:text-gray-400 bg-white dark:bg-gray-800 hover:text-gray-700 dark:hover:text-gray-300 focus:outline-none focus:bg-gray-50 dark:focus:bg-gray-700 active:bg-gray-50 dark:active:bg-gray-700 transition ease-in-out duration-150"
                                            >
                                                {$page.props.auth.user
                                                    .current_team.name}

                                                <svg
                                                    class="ml-2 -mr-0.5 h-4 w-4"
                                                    xmlns="http://www.w3.org/2000/svg"
                                                    fill="none"
                                                    viewBox="0 0 24 24"
                                                    stroke-width="1.5"
                                                    stroke="currentColor"
                                                >
                                                    <path
                                                        stroke-linecap="round"
                                                        stroke-linejoin="round"
                                                        d="M8.25 15L12 18.75 15.75 15m-7.5-6L12 5.25 15.75 9"
                                                    />
                                                </svg>
                                            </button>
                                        </span>
                                    </svelte:fragment>

                                    <svelte:fragment slot="content">
                                        <div class="w-60">
                                            <!-- Team Management -->
                                            <div
                                                class="block px-4 py-2 text-xs text-gray-400"
                                            >
                                                Manage Team
                                            </div>

                                            <!-- Team Settings -->
                                            <DropdownLink
                                                href={route(
                                                    "teams.show",
                                                    $page.props.auth.user
                                                        .current_team
                                                )}
                                            >
                                                Team Settings
                                            </DropdownLink>

                                            {#if $page.props.jetstream.canCreateTeams}
                                                <DropdownLink
                                                    href={route("teams.create")}
                                                >
                                                    Create New Team
                                                </DropdownLink>
                                            {/if}

                                            <!-- Team Switcher -->
                                            {#if $page.props.auth.user.all_teams.length > 1}
                                                <div
                                                    class="border-t border-gray-200 dark:border-gray-600"
                                                />

                                                <div
                                                    class="block px-4 py-2 text-xs text-gray-400"
                                                >
                                                    Switch Teams
                                                </div>

                                                {#each $page.props.auth.user.all_teams as team (team.id)}
                                                    <form
                                                        on:submit|preventDefault={switchToTeam(
                                                            team
                                                        )}
                                                    >
                                                        <DropdownLink
                                                            as="button"
                                                        >
                                                            <div
                                                                class="flex items-center"
                                                            >
                                                                {#if team.id == $page.props.auth.user.current_team_id}
                                                                    <svg
                                                                        class="mr-2 h-5 w-5 text-green-400"
                                                                        xmlns="http://www.w3.org/2000/svg"
                                                                        fill="none"
                                                                        viewBox="0 0 24 24"
                                                                        stroke-width="1.5"
                                                                        stroke="currentColor"
                                                                    >
                                                                        <path
                                                                            stroke-linecap="round"
                                                                            stroke-linejoin="round"
                                                                            d="M9 12.75L11.25 15 15 9.75M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                                                                        />
                                                                    </svg>
                                                                {/if}

                                                                <div>
                                                                    {team.name}
                                                                </div>
                                                            </div>
                                                        </DropdownLink>
                                                    </form>
                                                {/each}
                                            {/if}
                                        </div>
                                    </svelte:fragment>
                                </Dropdown>
                            {/if}
                        </div>

                        <!-- Settings Dropdown -->
                        <div class="ml-3 relative">
                            <Dropdown align="right" width="48">
                                <svelte:fragment slot="trigger">
                                    {#if $page.props.jetstream.managesProfilePhotos}
                                        <button
                                            class="flex text-sm border-2 border-transparent rounded-full focus:outline-none focus:border-gray-300 transition"
                                        >
                                            <img
                                                class="h-8 w-8 rounded-full object-cover"
                                                src={$page.props.auth.user
                                                    .profile_photo_url}
                                                alt={$page.props.auth.user.name}
                                            />
                                        </button>
                                    {:else}
                                        <span class="inline-flex rounded-md">
                                            <button
                                                type="button"
                                                class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md text-gray-500 dark:text-gray-400 bg-white dark:bg-gray-800 hover:text-gray-700 dark:hover:text-gray-300 focus:outline-none focus:bg-gray-50 dark:focus:bg-gray-700 active:bg-gray-50 dark:active:bg-gray-700 transition ease-in-out duration-150"
                                            >
                                                {$page.props.auth.user.name}

                                                <svg
                                                    class="ml-2 -mr-0.5 h-4 w-4"
                                                    xmlns="http://www.w3.org/2000/svg"
                                                    fill="none"
                                                    viewBox="0 0 24 24"
                                                    stroke-width="1.5"
                                                    stroke="currentColor"
                                                >
                                                    <path
                                                        stroke-linecap="round"
                                                        stroke-linejoin="round"
                                                        d="M19.5 8.25l-7.5 7.5-7.5-7.5"
                                                    />
                                                </svg>
                                            </button>
                                        </span>
                                    {/if}
                                </svelte:fragment>

                                <svelte:fragment slot="content">
                                    <!-- Account Management -->
                                    <div
                                        class="block px-4 py-2 text-xs text-gray-400"
                                    >
                                        Manage Account
                                    </div>

                                    <DropdownLink href={route("profile.show")}>
                                        Profile
                                    </DropdownLink>

                                    {#if $page.props.jetstream.hasApiFeatures}
                                        <DropdownLink
                                            href={route("api-tokens.index")}
                                        >
                                            API Tokens
                                        </DropdownLink>
                                    {/if}

                                    <div
                                        class="border-t border-gray-200 dark:border-gray-600"
                                    />

                                    <!-- Authentication -->
                                    <form on:submit|preventDefault={logout}>
                                        <DropdownLink as="button">
                                            Log Out
                                        </DropdownLink>
                                    </form>
                                </svelte:fragment>
                            </Dropdown>
                        </div>
                    </div>

                    <!-- Hamburger -->
                    <div class="-mr-2 flex items-center sm:hidden">
                        <button
                            class="inline-flex items-center justify-center p-2 rounded-md text-gray-400 dark:text-gray-500 hover:text-gray-500 dark:hover:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-900 focus:outline-none focus:bg-gray-100 dark:focus:bg-gray-900 focus:text-gray-500 dark:focus:text-gray-400 transition duration-150 ease-in-out"
                            on:click={(showingNavigationDropdown =
                                !showingNavigationDropdown)}
                        >
                            <svg
                                class="h-6 w-6"
                                stroke="currentColor"
                                fill="none"
                                viewBox="0 0 24 24"
                            >
                                <path
                                    class:hidden={showingNavigationDropdown}
                                    class:inline-flex={!showingNavigationDropdown}
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                    stroke-width="2"
                                    d="M4 6h16M4 12h16M4 18h16"
                                />
                                <path
                                    class:hidden={!showingNavigationDropdown}
                                    class:inline-flex={showingNavigationDropdown}
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                    stroke-width="2"
                                    d="M6 18L18 6M6 6l12 12"
                                />
                            </svg>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Responsive Navigation Menu -->
            <div
                class:block={showingNavigationDropdown}
                class:hidden={!showingNavigationDropdown}
                class="sm:hidden"
            >
                <div class="pt-2 pb-3 space-y-1">
                    <ResponsiveNavLink
                        href={route("dashboard")}
                        active={route().current("dashboard")}
                    >
                        Dashboard
                    </ResponsiveNavLink>
                </div>

                <!-- Responsive Settings Options -->
                <div
                    class="pt-4 pb-1 border-t border-gray-200 dark:border-gray-600"
                >
                    <div class="flex items-center px-4">
                        {#if $page.props.jetstream.managesProfilePhotos}
                            <div class="shrink-0 mr-3">
                                <img
                                    class="h-10 w-10 rounded-full object-cover"
                                    src={$page.props.auth.user
                                        .profile_photo_url}
                                    alt={$page.props.auth.user.name}
                                />
                            </div>
                        {/if}

                        <div>
                            <div
                                class="font-medium text-base text-gray-800 dark:text-gray-200"
                            >
                                {$page.props.auth.user.name}
                            </div>
                            <div class="font-medium text-sm text-gray-500">
                                {$page.props.auth.user.email}
                            </div>
                        </div>
                    </div>

                    <div class="mt-3 space-y-1">
                        <ResponsiveNavLink
                            href={route("profile.show")}
                            active={route().current("profile.show")}
                        >
                            Profile
                        </ResponsiveNavLink>

                        {#if $page.props.jetstream.hasApiFeatures}
                            <ResponsiveNavLink
                                href={route("api-tokens.index")}
                                active={route().current("api-tokens.index")}
                            >
                                API Tokens
                            </ResponsiveNavLink>
                        {/if}

                        <!-- Authentication -->
                        <form method="POST" on:submit|preventDefault={logout}>
                            <ResponsiveNavLink as="button">
                                Log Out
                            </ResponsiveNavLink>
                        </form>

                        <!-- Team Management -->
                        <template v-if="$page.props.jetstream.hasTeamFeatures">
                            <div
                                class="border-t border-gray-200 dark:border-gray-600"
                            />

                            <div class="block px-4 py-2 text-xs text-gray-400">
                                Manage Team
                            </div>

                            <!-- Team Settings -->
                            <ResponsiveNavLink
                                href={route(
                                    "teams.show",
                                    $page.props.auth.user.current_team
                                )}
                                active={route().current("teams.show")}
                            >
                                Team Settings
                            </ResponsiveNavLink>

                            {#if $page.props.jetstream.canCreateTeams}
                                <ResponsiveNavLink
                                    href={route("teams.create")}
                                    active={route().current("teams.create")}
                                >
                                    Create New Team
                                </ResponsiveNavLink>
                            {/if}

                            <!-- Team Switcher -->
                            {#if $page.props.auth.user.all_teams.length > 1}
                                <div
                                    class="border-t border-gray-200 dark:border-gray-600"
                                />

                                <div
                                    class="block px-4 py-2 text-xs text-gray-400"
                                >
                                    Switch Teams
                                </div>

                                {#each $page.props.auth.user.all_teams as team (team.id)}
                                    <form
                                        on:submit|preventDefault={switchToTeam(
                                            team
                                        )}
                                    >
                                        <ResponsiveNavLink as="button">
                                            <div class="flex items-center">
                                                {#if team.id == $page.props.auth.user.current_team_id}
                                                    <svg
                                                        class="mr-2 h-5 w-5 text-green-400"
                                                        xmlns="http://www.w3.org/2000/svg"
                                                        fill="none"
                                                        viewBox="0 0 24 24"
                                                        stroke-width="1.5"
                                                        stroke="currentColor"
                                                    >
                                                        <path
                                                            stroke-linecap="round"
                                                            stroke-linejoin="round"
                                                            d="M9 12.75L11.25 15 15 9.75M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                                                        />
                                                    </svg>
                                                {/if}
                                                <div>{team.name}</div>
                                            </div>
                                        </ResponsiveNavLink>
                                    </form>
                                {/each}
                            {/if}
                        </template>
                    </div>
                </div>
            </div>
        </nav>

        <!-- Page Heading -->
        {#if $$slots.header}
            <header class="bg-white dark:bg-gray-800 shadow">
                <div class="max-w-7xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
                    <slot name="header" />
                </div>
            </header>
        {/if}

        <!-- Page Content -->
        <main>
            <slot />
        </main>
    </div>
</div>
