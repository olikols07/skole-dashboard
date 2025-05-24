<script lang="ts">
    import { onMount } from "svelte";

    let testListe: any[] = [
        {
            test: "HEI",
            hei: "test",
        },
        {
            hei: "HEI",
            test: "test",
        }
    ];
</script>
<h1>Test</h1>
<h1>{JSON.stringify(testListe)}</h1>

<table>
    <thead>
        <tr>
            <td class="p-4 border-2 border-white">Dag</td>
            <td class="p-4 border-2 border-white">måneded</td>
            <td class="p-4 border-2 border-white">år</td>
        </tr>
    </thead>
    <tbody>
        {#each testListe as element}    
            <tr>
                <td class="p-4 border-1 border-white">
                    <input type="text" class="text-black" bind:value={element.test}>
                </td>
                <td class="p-4 border-1 border-white">{element.hei}</td>
                <td class="p-4 border-1 border-white">idk</td>
            </tr>
        {/each}
    </tbody>
</table>
<button onclick={()=>{console.log(testListe)}}>Save</button>