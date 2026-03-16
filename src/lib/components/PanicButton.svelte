<script lang="ts">
	import Button from './Button.svelte';

	let showOverlay = $state(false);

	const docs = [
		'https://www.haskell.org/',
		'https://www.rust-lang.org/',
		'https://www.ibm.com/topics/cobol'
	];

	function triggerPanic() {
		showOverlay = true;
		const randomDoc = docs[Math.floor(Math.random() * docs.length)];
		window.open(randomDoc, '_blank');

		setTimeout(() => {
			showOverlay = false;
		}, 10000);
	}
</script>

<Button
	onclick={triggerPanic}
	class="animate-pulse bg-red-600 text-white hover:bg-red-700 text-xl font-bold px-8 py-6 rounded-full shadow-lg"
>
	EMERGENCY DETOX
</Button>

{#if showOverlay}
	<div
		class="fixed inset-0 z-50 flex items-center justify-center bg-red-900/95 text-white p-8 text-center"
	>
		<h1 class="text-4xl md:text-6xl font-bold">You don't need the prompt, Josh. You need a walk.</h1>
	</div>
{/if}
