<script lang="ts">
	import { fly } from 'svelte/transition';
	import { cubicOut, backOut } from 'svelte/easing';
	import { onMount } from 'svelte';

	interface Confession {
		id: number;
		username: string;
		daysSober: number;
		text: string;
		meToos: number;
		category: string;
		timestamp: string;
		rotation: number;
	}

	const CATEGORIES = [
		'Prompt Abuse',
		'Copy-Paste Crime',
		'Identity Crisis',
		'Dependency',
		'Deploy Recklessness'
	] as const;

	const CATEGORY_COLORS: Record<string, string> = {
		'Prompt Abuse': 'text-[#f97316] bg-[#f97316]/10 border-[#f97316]/20',
		'Copy-Paste Crime': 'text-purple-400 bg-purple-400/10 border-purple-400/20',
		'Identity Crisis': 'text-[#58a6ff] bg-[#58a6ff]/10 border-[#58a6ff]/20',
		Dependency: 'text-pink-400 bg-pink-400/10 border-pink-400/20',
		'Deploy Recklessness': 'text-red-400 bg-red-400/10 border-red-400/20'
	};

	const ANON_BASES = [
		'anonymous_dev',
		'recovering_viber',
		'ex_10x_engineer',
		'prompt_abuser',
		'loop_amnesiac',
		'git_blamer',
		'full_stack_of_lies',
		'stack_overflow_refugee',
		'senior_viber',
		'junior_guesser',
		'tab_hoarder',
		'daily_relapser',
		'rubber_ducky_replacement',
		'context_window_enjoyer'
	];

	let confessions = $state<Confession[]>([
		{
			id: 1,
			username: 'anonymous_dev_2847',
			daysSober: 0,
			text: "I asked Claude to write my git commit messages for 3 months. My team thinks I'm some kind of philosopher.",
			meToos: 847,
			category: 'Prompt Abuse',
			timestamp: '2 hours ago',
			rotation: -1.2
		},
		{
			id: 2,
			username: 'senior_viber_99',
			daysSober: 4,
			text: "I submitted a pull request last Tuesday. I still don't know what's in it. It got approved with praise.",
			meToos: 1203,
			category: 'Copy-Paste Crime',
			timestamp: '5 hours ago',
			rotation: 0.8
		},
		{
			id: 3,
			username: 'reformed_rubber_ducker',
			daysSober: 12,
			text: "My rubber duck is now just a Claude tab I keep open. I haven't spoken to an actual duck in weeks.",
			meToos: 445,
			category: 'Dependency',
			timestamp: '1 day ago',
			rotation: -0.5
		},
		{
			id: 4,
			username: 'infinite_loop_larry',
			daysSober: 1,
			text: "I asked Claude to explain code that Claude wrote for me. It apologized.",
			meToos: 2891,
			category: 'Identity Crisis',
			timestamp: '1 day ago',
			rotation: 1.5
		},
		{
			id: 5,
			username: 'ex_10x_engineer_7731',
			daysSober: 0,
			text: "I told my interviewer the work was entirely my own. It was. I just have no idea what any of it does.",
			meToos: 3102,
			category: 'Identity Crisis',
			timestamp: '2 days ago',
			rotation: -1.8
		},
		{
			id: 6,
			username: 'loop_amnesiac_4420',
			daysSober: 7,
			text: "I forgot what a for loop looks like. I know conceptually what it does. I could not write one from memory with a gun to my head.",
			meToos: 672,
			category: 'Dependency',
			timestamp: '3 days ago',
			rotation: 0.3
		},
		{
			id: 7,
			username: 'quantum_debugger_0011',
			daysSober: 2,
			text: "I described a bug to Claude before I even looked at it myself. My description was wrong. Claude fixed a completely different bug. It was the right one.",
			meToos: 1847,
			category: 'Prompt Abuse',
			timestamp: '3 days ago',
			rotation: -0.7
		},
		{
			id: 8,
			username: 'naming_is_hard_5519',
			daysSober: 3,
			text: "I asked AI to name my variables. It named one 'userDataProcessingUtilityHelperManagerFactory'. I left it. It is in production.",
			meToos: 567,
			category: 'Prompt Abuse',
			timestamp: '4 days ago',
			rotation: 1.1
		},
		{
			id: 9,
			username: 'living_dangerously_404',
			daysSober: 0,
			text: "I copy-pasted 400 lines of code into production without reading a single one. It worked. I am terrified of what I have created.",
			meToos: 4201,
			category: 'Deploy Recklessness',
			timestamp: '4 days ago',
			rotation: -1.4
		},
		{
			id: 10,
			username: 'polite_to_a_fault_3388',
			daysSober: 8,
			text: "I have typed the word 'please' in a prompt 47 times. I am genuinely anxious the model might get upset with me.",
			meToos: 2034,
			category: 'Dependency',
			timestamp: '5 days ago',
			rotation: 0.6
		},
		{
			id: 11,
			username: 'ex_code_reviewer_1102',
			daysSober: 5,
			text: "My pull request review comments are now just: 'Claude says this looks fine.' Nobody has questioned this.",
			meToos: 891,
			category: 'Prompt Abuse',
			timestamp: '6 days ago',
			rotation: -0.9
		},
		{
			id: 12,
			username: 'full_stack_of_lies_9900',
			daysSober: 0,
			text: "I put 'full-stack developer' on my resume last week and immediately opened a new chat to figure out what that means.",
			meToos: 5678,
			category: 'Identity Crisis',
			timestamp: '1 week ago',
			rotation: 1.7
		},
		{
			id: 13,
			username: 'merge_conflict_mike_99',
			daysSober: 0,
			text: "I resolved a merge conflict by asking Claude which version was correct. It chose one. I don't know if it was right. We shipped.",
			meToos: 1123,
			category: 'Deploy Recklessness',
			timestamp: '1 week ago',
			rotation: -0.4
		},
		{
			id: 14,
			username: 'codependent_coder_7743',
			daysSober: 14,
			text: "I asked Claude if my code was good. It said yes. I knew it was lying. We have an understanding now.",
			meToos: 3401,
			category: 'Dependency',
			timestamp: '2 weeks ago',
			rotation: 0.9
		}
	]);

	let myMeToos = $state<Set<number>>(new Set());
	let showForm = $state(false);
	let newConfessionText = $state('');
	let mounted = $state(false);
	let submitting = $state(false);
	let justSubmitted = $state(false);
	let activeFilter = $state('All');

	let filteredConfessions = $derived(
		activeFilter === 'All'
			? confessions
			: confessions.filter((c) => c.category === activeFilter)
	);

	let totalMeToos = $derived(confessions.reduce((sum, c) => sum + c.meToos, 0));

	function toggleMeToo(id: number) {
		const newSet = new Set(myMeToos);
		const confession = confessions.find((c) => c.id === id);
		if (!confession) return;

		if (newSet.has(id)) {
			newSet.delete(id);
			confession.meToos--;
		} else {
			newSet.add(id);
			confession.meToos++;
		}
		myMeToos = newSet;
	}

	function getDaysBadgeClass(days: number): string {
		if (days === 0) return 'text-red-400 bg-red-400/10 border-red-400/20';
		if (days < 7) return 'text-yellow-400 bg-yellow-400/10 border-yellow-400/20';
		return 'text-[#7ee787] bg-[#7ee787]/10 border-[#7ee787]/20';
	}

	function generateUsername(): string {
		const base = ANON_BASES[Math.floor(Math.random() * ANON_BASES.length)];
		return `${base}_${Math.floor(Math.random() * 9000) + 1000}`;
	}

	function submitConfession() {
		if (!newConfessionText.trim() || newConfessionText.trim().length < 10) return;
		submitting = true;

		setTimeout(() => {
			const newItem: Confession = {
				id: Date.now(),
				username: generateUsername(),
				daysSober: Math.floor(Math.random() * 4),
				text: newConfessionText.trim(),
				meToos: 1,
				category: CATEGORIES[Math.floor(Math.random() * CATEGORIES.length)],
				timestamp: 'just now',
				rotation: (Math.random() - 0.5) * 3
			};
			confessions = [newItem, ...confessions];
			newConfessionText = '';
			submitting = false;
			showForm = false;
			justSubmitted = true;
			activeFilter = 'All';
			setTimeout(() => (justSubmitted = false), 4000);
		}, 900);
	}

	onMount(() => {
		mounted = true;
	});
</script>

<svelte:head>
	<title>The Confession Booth — Vibe Coders Anonymous</title>
</svelte:head>

<div class="min-h-screen bg-[#0d1117] text-[#c9d1d9] font-['Source_Sans_3',sans-serif]">
	<!-- Top nav -->
	<div class="border-b border-[#30363d] px-6 py-4">
		<a href="/" class="text-[#8b949e] hover:text-[#58a6ff] text-sm transition-colors">
			← Back to Recovery
		</a>
	</div>

	<!-- Hero -->
	<section class="relative overflow-hidden px-6 py-20 text-center">
		<div class="absolute inset-0 pointer-events-none">
			<div
				class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[700px] h-[300px] bg-orange-500/5 rounded-full blur-3xl"
			></div>
		</div>

		{#if mounted}
			<div in:fly={{ y: -20, duration: 600, easing: cubicOut }}>
				<div class="text-6xl mb-5">🕯️</div>
				<h1
					class="font-['Bitter',serif] text-5xl md:text-6xl font-bold mb-4 text-[#c9d1d9] tracking-tight"
				>
					The Confession Booth
				</h1>
				<p class="text-[#8b949e] text-xl max-w-lg mx-auto mb-2">
					What you share here stays here.
				</p>
				<p class="text-[#8b949e]/50 text-sm mb-12">
					(it doesn't. but your name is randomly generated so we feel better about it.)
				</p>

				<!-- Stats -->
				<div class="flex justify-center gap-10 mb-12">
					<div class="text-center">
						<div class="text-3xl font-bold text-[#c9d1d9] font-mono tabular-nums">
							{confessions.length}
						</div>
						<div class="text-xs text-[#8b949e] mt-1">confessions</div>
					</div>
					<div class="w-px bg-[#30363d]"></div>
					<div class="text-center">
						<div class="text-3xl font-bold text-[#c9d1d9] font-mono tabular-nums">
							{totalMeToos.toLocaleString()}
						</div>
						<div class="text-xs text-[#8b949e] mt-1">collective "me too"s</div>
					</div>
					<div class="w-px bg-[#30363d]"></div>
					<div class="text-center">
						<div class="text-3xl font-bold text-red-400 font-mono">∞</div>
						<div class="text-xs text-[#8b949e] mt-1">shame</div>
					</div>
				</div>

				<!-- CTA button -->
				<button
					onclick={() => (showForm = !showForm)}
					class="bg-[#f97316] hover:bg-[#ea6c0a] text-white font-semibold px-8 py-3 rounded-lg transition-all hover:scale-105 active:scale-95 shadow-lg shadow-orange-500/20"
				>
					{showForm ? "Never mind, I'm not ready" : 'Make Your Confession'}
				</button>
			</div>
		{/if}
	</section>

	<!-- Confession Form -->
	{#if showForm}
		<div transition:fly={{ y: -10, duration: 300, easing: cubicOut }} class="max-w-2xl mx-auto px-6 mb-12">
			<div
				class="bg-[#161b22] border border-[#f97316]/30 rounded-xl p-6 shadow-2xl shadow-orange-500/5"
			>
				<p class="text-xs text-[#8b949e]/70 font-mono mb-4">
					// speak your truth. a random name will be assigned. no one will know it's you.
				</p>
				<textarea
					bind:value={newConfessionText}
					placeholder="I once asked Claude to... and I am not proud of what happened next."
					class="w-full bg-[#0d1117] border border-[#30363d] rounded-lg p-4 text-[#c9d1d9] placeholder-[#8b949e]/40 resize-none h-32 focus:outline-none focus:border-[#f97316]/50 transition-colors text-sm leading-relaxed"
					maxlength="280"
				></textarea>
				<div class="flex items-center justify-between mt-3">
					<span class="text-xs text-[#8b949e]/50 font-mono"
						>{newConfessionText.length}/280 characters of shame</span
					>
					<button
						onclick={submitConfession}
						disabled={submitting || newConfessionText.trim().length < 10}
						class="bg-[#f97316] hover:bg-[#ea6c0a] disabled:opacity-40 disabled:cursor-not-allowed text-white font-semibold px-6 py-2 rounded-lg transition-all hover:scale-105 active:scale-95 text-sm"
					>
						{submitting ? 'Submitting your shame...' : 'Confess Anonymously'}
					</button>
				</div>
			</div>
		</div>
	{/if}

	<!-- Absolution toast -->
	{#if justSubmitted}
		<div
			transition:fly={{ y: 20, duration: 300, easing: backOut }}
			class="fixed bottom-6 right-6 z-50 bg-[#161b22] border border-[#7ee787]/30 rounded-xl px-6 py-4 shadow-2xl max-w-xs"
		>
			<p class="text-[#7ee787] font-semibold text-sm">🕊️ You are heard.</p>
			<p class="text-[#8b949e] text-xs mt-1">
				Your confession has been pinned. The healing begins now. Maybe.
			</p>
		</div>
	{/if}

	<!-- Filter tabs -->
	<div class="max-w-7xl mx-auto px-6 mb-8">
		<div class="flex gap-2 flex-wrap">
			{#each ['All', ...CATEGORIES] as filter}
				<button
					onclick={() => (activeFilter = filter)}
					class="px-3 py-1.5 rounded-full text-xs font-medium border transition-all {activeFilter ===
					filter
						? 'bg-[#f97316] border-[#f97316] text-white'
						: 'border-[#30363d] text-[#8b949e] hover:border-[#58a6ff]/50 hover:text-[#58a6ff]'}"
				>
					{filter}
				</button>
			{/each}
		</div>
	</div>

	<!-- Confession Wall -->
	<div class="max-w-7xl mx-auto px-6 pb-24">
		{#if filteredConfessions.length === 0}
			<div class="text-center py-20 text-[#8b949e]">
				<p class="text-4xl mb-4">🤫</p>
				<p>No confessions in this category yet.</p>
				<p class="text-sm opacity-60 mt-1">Be the first to break the silence.</p>
			</div>
		{:else}
			<div class="columns-1 sm:columns-2 lg:columns-3 gap-5">
				{#each filteredConfessions as confession (confession.id)}
					<div class="break-inside-avoid mb-5" in:fly={{ y: 20, duration: 400, easing: cubicOut }}>
						<div
							class="bg-[#161b22] border border-[#30363d] rounded-xl p-5 hover:border-[#58a6ff]/30 transition-all duration-300 relative shadow-lg"
							style="transform: rotate({confession.rotation}deg);"
						>
							<!-- Thumbtack -->
							<div
								class="absolute -top-2.5 left-1/2 -translate-x-1/2 w-5 h-5 rounded-full bg-[#21262d] border-2 border-[#58a6ff]/40 shadow-md z-10"
							></div>

							<!-- Header row -->
							<div class="flex items-start justify-between mb-3 mt-1">
								<span
									class="text-xs px-2 py-0.5 rounded border font-medium {CATEGORY_COLORS[
										confession.category
									]}"
								>
									{confession.category}
								</span>
								<span class="text-xs text-[#8b949e]/50 font-mono">{confession.timestamp}</span>
							</div>

							<!-- Confession text -->
							<p class="text-[#c9d1d9] text-sm leading-relaxed mb-4 italic">
								"{confession.text}"
							</p>

							<!-- Username row -->
							<div class="flex items-center gap-2 mb-3">
								<div
									class="w-5 h-5 rounded-full bg-[#30363d] flex items-center justify-center text-[9px] text-[#8b949e] font-bold shrink-0"
								>
									{confession.username.charAt(0).toUpperCase()}
								</div>
								<span class="text-xs text-[#8b949e] font-mono truncate">{confession.username}</span>
							</div>

							<!-- Footer row -->
							<div
								class="flex items-center justify-between pt-3 border-t border-[#30363d]"
							>
								<span
									class="text-xs px-2 py-0.5 rounded-full border font-mono {getDaysBadgeClass(
										confession.daysSober
									)}"
								>
									{confession.daysSober === 0
										? '0 days clean'
										: confession.daysSober === 1
											? '1 day clean'
											: `${confession.daysSober} days clean`}
								</span>
								<button
									onclick={() => toggleMeToo(confession.id)}
									class="flex items-center gap-1.5 text-xs px-3 py-1.5 rounded-lg border transition-all active:scale-90 hover:scale-105 {myMeToos.has(
										confession.id
									)
										? 'bg-[#58a6ff]/10 border-[#58a6ff]/30 text-[#58a6ff]'
										: 'border-[#30363d] text-[#8b949e] hover:border-[#58a6ff]/40 hover:text-[#58a6ff]'}"
								>
									🤝 <span class="font-mono">{confession.meToos.toLocaleString()}</span>
								</button>
							</div>
						</div>
					</div>
				{/each}
			</div>
		{/if}
	</div>

	<!-- Footer -->
	<div class="border-t border-[#30363d] px-6 py-10 text-center">
		<p class="text-[#8b949e]/50 text-xs mb-6">
			Your shame is encrypted end-to-end. Probably. We didn't actually build that part.
		</p>
		<a href="/" class="text-[#f97316] hover:underline text-sm transition-colors">
			← Return to the 12-Step Program
		</a>
	</div>
</div>
