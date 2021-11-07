<script lang="ts">
    import type { Signer } from 'ethers';
    import { MerkleDistributor } from '../../../abi/abiExporter'
    import TransactionHandler from '../utils/TransactionHandler.svelte'
    
    const url = 'https://mysterious-oasis-55905.herokuapp.com/getdata'

    export let signer:Signer
    export const address:string = '0x93A0d0b23C3D045F395e26850c1B64df7F3Ab783'
    export let index:string = ''
    export let amount:string = ''
    export let merkleProof:Array<string> = []

    function setArgs(_index:string, _amount:string, _merkleProof:Array<string>):boolean {
        index = _index
        amount = _amount
        merkleProof = _merkleProof
        return true
    }

    async function requestData(address) {
        return await fetch(url, {
            method: 'POST',
            headers: {"Content-Type": "application/json"},
            body: JSON.stringify({address})})
    }
</script>

<main>
    {#await signer.getAddress() then account}
        {#await requestData(account)}
            checking for eligibility, please wait
        {:then result}
            {#await result.json() then json} 
                {#if json.success}
                    {setArgs(json.data.index, json.data.balance, json.data.proof) && ""}
                    {console.log(json)}
                    <TransactionHandler 
                        abi={MerkleDistributor}
                        {signer}
                        {address}
                        method="claim"
                        args={[index, account, amount, merkleProof]}>
                        <p>Your OLD AXS is now in your wallet, please use <a href="https://bridge.roninchain.com" rel="noopener noreferrer">ronin bridge</a> to upgrade or deposit your OLD AXS</p>
                    </TransactionHandler>
                {:else}
                    The selected account is not able to claim.
                {/if}
            {/await}   
        {/await}
    {/await}
</main>