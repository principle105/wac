<script lang="ts">
    import { onMount, SvelteComponent } from "svelte";
    import { page } from "$app/stores";
    import gsap, { Expo } from "gsap";

    // Icons
    import FaEnvelope from "svelte-icons/fa/FaEnvelope.svelte";
    import FaFacebook from "svelte-icons/fa/FaFacebook.svelte";
    import FaInstagram from "svelte-icons/fa/FaInstagram.svelte";
    import FaTwitter from "svelte-icons/fa/FaTwitter.svelte";
    import FaGithub from "svelte-icons/fa/FaGithub.svelte";

    import logo from "$lib/logos/wac_large.png";

    import "./style.css";

    interface Route {
        name: string;
        path: string;
    }

    interface Social {
        name: string;
        icon: typeof SvelteComponent;
        link: string;
    }

    const barTl = gsap.timeline({ reversed: true });
    let toggle: HTMLButtonElement;

    onMount(() => {
        barTl.set("#navbar", {
            className:
                "fixed right-0 bottom-0 z-50 bg-zinc-900 w-[68%] h-full bg-opacity-80 flex backdrop-blur-lg flex-col justify-center items-center text-xl gap-14 text-zinc-300",
        });
        barTl.set("#open", {
            display: "none",
            duration: 0,
        });
        barTl.to("#close", {
            display: "block",
            duration: 0,
        });
        barTl.to("#navbar", {
            duration: 0.3,
            ease: Expo.easeInOut,
        });
    });

    const toggleNav = () => {
        // Checking if the hamburger nav is visible
        if (
            window.getComputedStyle(toggle).getPropertyValue("display") ===
            "none"
        )
            return;

        if (barTl.reversed()) {
            barTl.timeScale(1).reversed(false);
        } else {
            barTl.reversed(true).time(0);
        }
    };

    const routes: Route[] = [
        { name: "Schedule", path: "/schedule" },
        { name: "Team", path: "/team" },
        { name: "Past Speakers", path: "/past-speakers" },
        { name: "FAQ", path: "/faq" },
    ];

    const socials: Social[] = [
        {
            name: "Email",
            icon: FaEnvelope,
            link: "mailto:wac@ucc.on.ca",
        },
        {
            name: "Facebook",
            icon: FaFacebook,
            link: "https://www.facebook.com/worldaffairsconference",
        },
        {
            name: "Instagram",
            icon: FaInstagram,
            link: "https://instagram.com/WorldAffairsCon",
        },
        {
            name: "Twitter",
            icon: FaTwitter,
            link: "https://twitter.com/WorldAffairsCon",
        },
        {
            name: "Github",
            icon: FaGithub,
            link: "https://github.com/worldaffairsconference/worldaffairs.ucc.on.ca",
        },
    ];
</script>

<header
    class="flex items-center justify-between px-6 lg:px-16 h-28 md:h-[8.5rem] absolute w-full z-50"
>
    <a href="/" class="hover:brightness-110 transition-all">
        <img src={logo} alt="logo" class="h-11 sm:h-14" />
    </a>
    <nav class="flex items-center gap-4 lg:gap-20">
        <ul class="hidden lg:flex gap-16 text-zinc-300" id="navbar">
            {#each routes as route}
                <li>
                    <a
                        href={route.path}
                        class="text-zinc-300 hover:text-white transition-colors duration-100 hover:shadow-glow
                        {$page.url.pathname === route.path &&
                            'underline decoration-primary underline-offset-8'}
                        "
                        on:click={toggleNav}
                    >
                        {route.name}
                    </a>
                </li>
            {/each}
        </ul>
        <button
            class="bg-gradient-to-r from-primary to-secondary rounded-full px-10 lg:px-12 py-3 text-white text-xs lg:text-base"
        >
            Login
        </button>
        <button
            class="block lg:hidden z-50"
            aria-label="Toggle navigation"
            bind:this={toggle}
            on:click={toggleNav}
        >
            <!-- Hamburger menu icon -->

            <svg
                height="32px"
                id="open"
                style="enable-background:new 0 0 32 32;"
                version="1.1"
                viewBox="0 0 32 32"
                width="32px"
                xml:space="preserve"
                xmlns="http://www.w3.org/2000/svg"
                xmlns:xlink="http://www.w3.org/1999/xlink"
                class="h-7 w-7 fill-zinc-300"
            >
                <path
                    d="M4,10h24c1.104,0,2-0.896,2-2s-0.896-2-2-2H4C2.896,6,2,6.896,2,8S2.896,10,4,10z M28,14H4c-1.104,0-2,0.896-2,2  s0.896,2,2,2h24c1.104,0,2-0.896,2-2S29.104,14,28,14z M28,22H4c-1.104,0-2,0.896-2,2s0.896,2,2,2h24c1.104,0,2-0.896,2-2  S29.104,22,28,22z"
                />
            </svg>

            <svg
                xmlns="http://www.w3.org/2000/svg"
                xmlns:xlink="http://www.w3.org/1999/xlink"
                width="32px"
                height="32px"
                viewBox="0 0 32 32"
                version="1.1"
                class="h-5 w-5 fill-zinc-300 hidden"
                id="close"
            >
                <g id="surface1">
                    <path
                        d="M 19.796875 16 L 31.683594 4.117188 C 32.105469 3.695312 32.105469 3.011719 31.683594 2.589844 L 29.410156 0.316406 C 29.210938 0.113281 28.933594 0 28.648438 0 C 28.363281 0 28.085938 0.113281 27.886719 0.316406 L 16 12.203125 L 4.113281 0.316406 C 3.914062 0.113281 3.636719 0 3.351562 0 C 3.066406 0 2.789062 0.113281 2.589844 0.316406 L 0.316406 2.589844 C -0.105469 3.011719 -0.105469 3.695312 0.316406 4.117188 L 12.203125 16 L 0.316406 27.882812 C -0.105469 28.304688 -0.105469 28.988281 0.316406 29.410156 L 2.589844 31.683594 C 2.792969 31.886719 3.066406 32 3.351562 32 C 3.640625 32 3.914062 31.886719 4.117188 31.683594 L 16 19.800781 L 27.882812 31.683594 C 28.085938 31.886719 28.359375 32 28.648438 32 C 28.933594 32 29.207031 31.886719 29.410156 31.683594 L 31.683594 29.410156 C 32.105469 28.988281 32.105469 28.304688 31.683594 27.882812 Z M 19.796875 16 "
                    />
                </g>
            </svg>
        </button>
    </nav>
</header>

<main class="mx-auto w-full grow">
    <slot />
</main>

<footer class="bg-zinc-950" id="footer">
    <div
        class="w-full mx-auto max-w-screen-xl p-4 md:flex md:items-center md:justify-between"
    >
        <span class="text-sm text-gray-500 sm:text-center dark:text-gray-400">
            © 2023 The World Affairs Conference. All Rights Reserved.
        </span>
        <div
            class="flex flex-wrap gap-2.5 items-center mt-3 text-sm font-medium text-zinc-200 sm:mt-0"
        >
            {#each socials as social}
                <a
                    href={social.link}
                    class="h-6 w-6"
                    target="_blank"
                    rel="noreferrer"
                >
                    <svelte:component this={social.icon} />
                </a>
            {/each}
        </div>
    </div>
</footer>
