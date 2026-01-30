<script lang="ts">
	import '../app.css';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
	<link
		href="https://fonts.googleapis.com/css2?family=Bitter:ital,wght@0,100..900;1,100..900&family=Source+Sans+3:ital,wght@0,200..900;1,200..900&display=swap"
		rel="stylesheet"
	/>
	<title>Vibe Coders Anonymous | Recovery is Possible</title>
	<meta
		name="description"
		content="A support community for developers recovering from AI-dependent coding. You're not alone. The first step is admitting you don't know what your code does."
	/>
</svelte:head>

<div class="font-sans antialiased">
	{@render children()}
</div>
