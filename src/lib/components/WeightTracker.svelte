<script lang="ts">
	let scanning = $state(false);
	let size = $state<number | null>(null);
	let emotionalWeight = $state('');

	const equivalents = [
		'4,000 unread documentation pages',
		'12 years of questioning your career choice',
		"3,000 cups of coffee you didn't need",
		'the weight of your imposter syndrome'
	];

	async function scan() {
		scanning = true;
		size = null;

		// Simulate scan
		await new Promise((resolve) => setTimeout(resolve, 2000));

		size = Math.random() * 2; // 0 to 2 GB
		emotionalWeight = equivalents[Math.floor(Math.random() * equivalents.length)];
		scanning = false;
	}

	let isTooLarge = $derived(size !== null && size > 1);
</script>

<div
	class="p-6 rounded-2xl border transition-colors duration-500 {isTooLarge
		? 'bg-[#f85149]/5 border-[#f85149]/30'
		: 'bg-[#161b22] border-[#30363d]'}"
	class:animate-shake={isTooLarge}
>
	<h2 class="text-lg font-bold text-white mb-4">node_modules Emotional Weight</h2>

	{#if scanning}
		<div class="h-3 bg-[#21262d] rounded-full overflow-hidden">
			<div class="h-full bg-[#58a6ff] animate-pulse w-full"></div>
		</div>
		<p class="mt-2 text-sm text-[#8b949e]">Scanning your dependencies...</p>
	{:else if size !== null}
		<div class="text-3xl font-mono font-bold mb-2 {isTooLarge ? 'text-[#f85149]' : 'text-[#7ee787]'}">
			{size.toFixed(2)} GB
		</div>
		<p class="text-[#8b949e]">
			Equivalent to <span class="text-[#c9d1d9]">{emotionalWeight}</span>.
		</p>
	{:else}
		<p class="text-[#6e7681] mb-4">Ready to scan your node_modules.</p>
	{/if}

	<button
		onclick={scan}
		disabled={scanning}
		class="mt-4 px-4 py-2 bg-[#58a6ff] text-white rounded-lg hover:bg-[#58a6ff]/80 disabled:bg-[#21262d] disabled:text-[#6e7681] transition-colors font-medium"
	>
		{scanning ? 'Scanning...' : 'Scan node_modules'}
	</button>
</div>

<style>
	@keyframes shake {
		0%,
		100% {
			transform: translateX(0);
		}
		25% {
			transform: translateX(-5px);
		}
		75% {
			transform: translateX(5px);
		}
	}
	.animate-shake {
		animation: shake 0.2s ease-in-out infinite;
	}
</style>