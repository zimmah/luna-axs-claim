<script lang="ts">
    import {ethers, Signer} from 'ethers'
    import { MerkleDistributor } from '../../abi/abiExporter'
    import Claim from './interactions/Claim.svelte'
    import WrongNetwork from './WrongNetwork.svelte'

    const provider = new ethers.providers.Web3Provider(window.ethereum)
    export const signer:Signer = provider.getSigner()
    const merkleDistributor = new ethers.Contract('0x93a0d0b23c3d045f395e26850c1b64df7f3ab783', MerkleDistributor, provider)

    let currentAccount:string
    $: claimed = merkleDistributor.callStatic.claimed(currentAccount || '0x0000000000000000000000000000000000000000')

    window.ethereum
    .on("accountsChanged", handleAccountsChanged)
    .on("chainChanged", () => window.location.reload())
    .on("disconnect", handleAccountsChanged)
    .request({ method: "eth_accounts"} )
    .then(handleAccountsChanged)
    .catch(err => console.error(err))
        
    async function handleAccountsChanged(accounts:Array<string>):Promise<void> {
        if (accounts.length === 0) {
            console.log("Please connect to MetaMask")
            currentAccount = undefined
        } else if (accounts[0] !== currentAccount) {
            currentAccount = accounts[0]
        }
    }

    function connect():void {
        window.ethereum
        .request({ method: "eth_requestAccounts" })
        .then(handleAccountsChanged)
        .catch(err => {
            if (err.code === 4001) {
                // EIP-1193 userRejectedRequest error
                // If this happens, the user rejected the connection request.
                console.log("Please connect to MetaMask.")
            } else {
                console.error(err)
            }
        })
    }
</script>

<main>
    {#if Number(window.ethereum.chainId) === 1}
        <h1>LUNA to AXS claiming tool</h1>
        {#if currentAccount}
            {#await claimed}
                checking if your account has claimed before, please wait
            {:then hasClaimed}
                <p>Your account {currentAccount} has {hasClaimed ? 'already claimed' : 'not claimed'}</p>
                {#if !hasClaimed}
                    <Claim {signer} />
                {/if}
            {/await}
        {:else}
            <button on:click={connect}>Connect</button>
        {/if}
    {:else}
        <WrongNetwork/>
    {/if}
</main>

<style>
        h1 {
        color: #ff3e00;
        text-transform: uppercase;
        font-size: 4em;
        font-weight: 100;
    }
</style>
