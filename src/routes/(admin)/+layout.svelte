<script lang="ts">
    import { onMount } from 'svelte';
	
	let { children } = $props();

    let authenticated = $state(false)
    onMount(()=>{
        if(!authenticated) {
            let input = prompt("Enter password:");
            if (input == "CoolerMaster") authenticated = true;
        }
    })
</script>
{#if authenticated}
    {@render children()}
{/if}