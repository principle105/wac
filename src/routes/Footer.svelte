<script lang="ts">
    import type { SvelteComponent } from "svelte";
    // Icons
    import FaEnvelope from "svelte-icons/fa/FaEnvelope.svelte";
    import FaInstagram from "svelte-icons/fa/FaInstagram.svelte";
    import FaLinkedin from "svelte-icons/fa/FaLinkedin.svelte";

    import branksomeWhite from "$lib/images/logos/branksome_white.webp";
    import uccWhite from "$lib/images/logos/ucc_white.png";

    interface Social {
        name: string;
        icon: typeof SvelteComponent;
        link: string;
    }

    const socials: Social[] = [
        {
            name: "Email",
            icon: FaEnvelope,
            link: "mailto:support@worldaffairscon.org",
        },
        {
            name: "Instagram",
            icon: FaInstagram,
            link: "https://instagram.com/WorldAffairsCon",
        },
        {
            name: "LinkedIn",
            icon: FaLinkedin,
            link: "https://ca.linkedin.com/company/world-affairs-conference",
        },
    ];
</script>

<footer class="bg-zinc-950" id="footer">
    <div
        class="w-full mx-auto max-w-screen-xl p-12 md:flex md:items-center md:justify-between gap-3"
    >
        <div>
            <div class="flex gap-7 items-center mb-6">
                <img src={uccWhite} alt="UCC Logo" class="h-12 sm:h-16" />
                <img
                    src={branksomeWhite}
                    alt="Branksome Hall Logo"
                    class="h-12 sm:h-16"
                />
            </div>
            <span
                class="text-sm text-gray-500 sm:text-center dark:text-gray-400"
            >
                © 2023 The World Affairs Conference. All Rights Reserved.
            </span>
        </div>
        <div>
            <span class="uppercase text-zinc-300">Social</span>
            <div
                class="flex flex-wrap gap-2.5 items-center mt-3 text-sm font-medium text-zinc-200 sm:mt-0"
            >
                {#each socials as social}
                    <a
                        href={social.link}
                        class="h-6 w-6"
                        target="_blank"
                        rel="noopener noreferrer"
                        aria-label="{social.name} page"
                    >
                        <svelte:component this={social.icon} />
                    </a>
                {/each}
            </div>
        </div>
    </div>
</footer>
