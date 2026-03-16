<script lang="ts">
	import { fade, fly } from 'svelte/transition';

	const responses = [
		"Have you tried turning it off and on again?",
		"Is the semicolon in the right place?",
		"Maybe it's a DNS issue. It's always DNS.",
		"Did you check the console? It's screaming at you.",
		"That's not a bug, it's an undocumented feature.",
		"I believe in your vibes, even if the compiler doesn't.",
		"Take a breath. Drink some water. Then delete the node_modules.",
		"What if you just... didn't fix it? Just vibe with the error.",
		"The code is fine. The universe is just misaligned today.",
		"Have you tried explaining it to me again? I'm listening.",
		"Just rewrite it in Rust. It won't fix the logic, but you'll feel superior.",
		"LGTM. Ship it to production and go to sleep.",
		"The vibes are off. Delete the last 3 commits.",
		"It worked on my machine (which is also a duck).",
		"Maybe the bug is actually a feature request from the future?",
		"Have you tried manifesting a successful build?",
		"Error 418: I'm a teapot. Wait, no, I'm a duck."
	];

	let currentResponse = $state("");
	let isVenting = $state(false);
	let inputMessage = $state("");

	function handleVent() {
		if (!inputMessage.trim()) return;
		
		// Play a subtle quack sound if we had one, but for now we'll just use a visual pop
		isVenting = true;
		currentResponse = responses[Math.floor(Math.random() * responses.length)];
		inputMessage = "";
		
		setTimeout(() => {
			isVenting = false;
		}, 4000);
	}

	function handleDuckClick() {
		isVenting = true;
		currentResponse = responses[Math.floor(Math.random() * responses.length)];
		
		setTimeout(() => {
			isVenting = false;
		}, 4000);
	}
</script>

<div class="duck-container p-6 border-2 border-yellow-500/30 bg-black/40 rounded-lg backdrop-blur-sm flex flex-col items-center gap-4 max-w-md mx-auto hover:border-yellow-500/50 transition-colors duration-500 shadow-[0_0_30px_rgba(234,179,8,0.1)]">
	<div class="duck-avatar text-6xl animate-bounce cursor-pointer hover:scale-125 transition-transform duration-300 active:scale-95" role="img" aria-label="Rubber Duck" onclick={handleDuckClick}>
		🦆
	</div>
	
	<div class="text-center">
		<h3 class="text-yellow-500 font-mono font-bold text-xl mb-2">Rubber Duck Debugger</h3>
		<p class="text-gray-400 text-sm font-mono">Vent your bugs to the duck. It won't judge. Much.</p>
	</div>

	{#if isVenting}
		<div in:fly={{ y: 20, duration: 400 }} out:fade class="response-bubble bg-yellow-500 text-black p-3 rounded-lg font-mono text-sm relative">
			{currentResponse}
			<div class="absolute -top-2 left-1/2 -translate-x-1/2 w-0 h-0 border-l-[8px] border-l-transparent border-r-[8px] border-r-transparent border-b-[8px] border-b-yellow-500"></div>
		</div>
	{:else}
		<div class="w-full flex flex-col gap-2">
			<textarea
				bind:value={inputMessage}
				placeholder="Explain your bug here..."
				class="w-full bg-black/60 border border-yellow-500/50 text-yellow-200 p-2 rounded font-mono text-sm focus:outline-none focus:border-yellow-500 h-20 resize-none"
			></textarea>
			<button
				onclick={handleVent}
				disabled={!inputMessage.trim()}
				class="bg-yellow-500 hover:bg-yellow-400 disabled:opacity-50 disabled:cursor-not-allowed text-black font-mono font-bold py-2 px-4 rounded transition-colors"
			>
				QUACK (VENT)
			</button>
		</div>
	{/if}
</div>

<style>
	.duck-avatar {
		filter: drop-shadow(0 0 10px rgba(234, 179, 8, 0.3));
	}
</style>
