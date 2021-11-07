<script lang="ts">
    import Web3 from './components/web3/Web3.svelte'
    import NoWeb3 from './components/web3/NoWeb3.svelte'
</script>

<main>
    {#if window.ethereum}
        <Web3 />
    {:else}
        <NoWeb3 />
    {/if}
</main>

<footer>Community project made with ❤️ by zimmah</footer>

<style>
    main {
        text-align: center;
        padding: 1em;
        max-width: 240px;
        margin: 0 auto;
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }

    footer {
        text-align: center;
        position: absolute;
        bottom: 0;
        width: 100%;
        height: 2.5rem;            /* Footer height */
        background-color: #ff3e00;
        color: white;
    }
</style>