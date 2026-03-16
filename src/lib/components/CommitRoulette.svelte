<script lang="ts">
	let message = $state('Click the button to generate a vibe-based commit message.');

	const messages = [
		'Replaced logic with vibes',
		'I have no idea what this does, but the tests passed',
		'Sacrificed a goat to the dependency tree gods',
		'This is the last time I trust the AI',
		'Fixed a bug by deleting the code',
		'It works on my machine (and the AI\'s machine)'
	];

	function generateMessage() {
		const randomIndex = Math.floor(Math.random() * messages.length);
		message = messages[randomIndex];
	}
</script>

<div class="p-6 border border-gray-700 rounded-lg bg-gray-900 text-gray-100 shadow-xl">
	<div class="mb-4 p-4 bg-black rounded font-mono text-sm text-green-400 border border-gray-800 min-h-[3rem] flex items-center">
		{message}
	</div>
	<button
		onclick={generateMessage}
		class="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white font-semibold rounded transition-colors"
	>
		Generate Commit Message
	</button>
</div>
