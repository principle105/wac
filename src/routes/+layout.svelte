<script lang="ts">
    // Components
    import Header from "./Header.svelte";
    import Footer from "./Footer.svelte";

    import "./style.css";
</script>

<Header />

<main class="mx-auto w-full grow">
    <slot />
</main>

<Footer />
