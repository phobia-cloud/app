<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import TrezorLogin from '$lib/TrezorLogin.svelte';
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>
	<TrezorLogin />
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 1;
	}
</style>
