<script lang="ts">
    import { page } from "$app/stores";
    import logo from "$lib/wac_logo.png";

    import "./style.css";

    interface Route {
        name: string;
        path: string;
    }

    const routes: Route[] = [
        { name: "Home", path: "/" },
        { name: "Speakers", path: "/speakers" },
        { name: "Schedule", path: "/schedule" },
        { name: "Team", path: "/team" },
        { name: "FAQ", path: "/faq" },
    ];
</script>

<header
    class="flex items-center justify-between px-6 lg:px-16 h-[8.5rem] absolute w-full z-50"
>
    <a href="/" class="hover:brightness-110 transition-all">
        <img src={logo} alt="logo" class="h-14" />
    </a>
    <nav class="flex items-center gap-12 lg:gap-20">
        <ul class="hidden lg:flex gap-16 text-zinc-300">
            {#each routes as route}
                <li>
                    <a
                        href={route.path}
                        class="text-zinc-300 hover:text-white transition-colors duration-100 hover:shadow-glow
                        {$page.url.pathname === route.path &&
                            'underline decoration-primary underline-offset-8'}
                        "
                    >
                        {route.name}
                    </a>
                </li>{/each}
        </ul>
        <button
            class="bg-gradient-to-r from-primary to-secondary rounded-full px-12 py-3 text-white"
        >
            Login
        </button>
        <div class="block lg:hidden">
            <svg
                height="32px"
                id="Layer_1"
                style="enable-background:new 0 0 32 32;"
                version="1.1"
                viewBox="0 0 32 32"
                width="32px"
                xml:space="preserve"
                xmlns="http://www.w3.org/2000/svg"
                xmlns:xlink="http://www.w3.org/1999/xlink"
                class="h-10 w-10 fill-zinc-300"
                ><path
                    d="M4,10h24c1.104,0,2-0.896,2-2s-0.896-2-2-2H4C2.896,6,2,6.896,2,8S2.896,10,4,10z M28,14H4c-1.104,0-2,0.896-2,2  s0.896,2,2,2h24c1.104,0,2-0.896,2-2S29.104,14,28,14z M28,22H4c-1.104,0-2,0.896-2,2s0.896,2,2,2h24c1.104,0,2-0.896,2-2  S29.104,22,28,22z"
                /></svg
            >
        </div>
    </nav>
</header>

<main class="mx-auto w-5/6 grow">
    <slot />
</main>
