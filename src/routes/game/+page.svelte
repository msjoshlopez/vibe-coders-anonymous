<script lang="ts">
	import { fly, scale } from 'svelte/transition';
	import { cubicOut, backOut } from 'svelte/easing';
	import { onMount } from 'svelte';

	interface Scenario {
		emoji: string;
		situation: string;
		goodChoice: string;
		badChoice: string;
		goodResult: string;
	}

	const scenarios: Scenario[] = [
		{
			emoji: '🐛',
			situation: 'Error in production. Slack is blowing up. Your hands are shaking.',
			goodChoice: 'Check the error logs and debug methodically',
			badChoice: 'Paste the logs into Claude and pray 🤖',
			goodResult: 'You read actual logs. A hero emerges.'
		},
		{
			emoji: '📖',
			situation: "You need to use an API you've never touched before.",
			goodChoice: 'Open the official documentation',
			badChoice: 'Ask Claude to write the integration 🤖',
			goodResult: "You opened actual docs. We're crying happy tears."
		},
		{
			emoji: '🤷',
			situation: "Your code works but you have no idea why. It ships tomorrow.",
			goodChoice: 'Stay up late and understand every line',
			badChoice: '"Ship it. It works, don\'t touch it." 🤖',
			goodResult: 'Understanding code is rare. You did it.'
		},
		{
			emoji: '👥',
			situation: 'A junior dev asks you to explain the code you wrote yesterday.',
			goodChoice: 'Walk them through your logic confidently',
			badChoice: '"It\'s self-documenting" (while opening Claude) 🤖',
			goodResult: 'You explained code you actually wrote!'
		},
		{
			emoji: '🔄',
			situation: 'You need to reverse a string. Your hands hover over the keyboard.',
			goodChoice: 'Type split("").reverse().join("") from memory',
			badChoice: '"Claude, how do I reverse a string?" 🤖',
			goodResult: 'You remembered an algorithm. Incredible.'
		},
		{
			emoji: '💀',
			situation: 'Your tests are failing. All 47 of them. At once.',
			goodChoice: 'Run one test at a time and debug each failure',
			badChoice: 'Paste all 47 errors into Claude at once 🤖',
			goodResult: "Methodical debugging. You're healing."
		},
		{
			emoji: '🤯',
			situation: 'npm install failed with 23 warnings and 3 critical vulnerabilities.',
			goodChoice: 'Read the warnings and address them one by one',
			badChoice: 'Ask Claude what all these warnings mean 🤖',
			goodResult: 'You read warning output! Peak developer.'
		},
		{
			emoji: '🎭',
			situation: 'Code review: your senior asks why you chose this approach.',
			goodChoice: 'Explain your actual technical reasoning',
			badChoice: 'Secretly ask Claude for a smart-sounding answer 🤖',
			goodResult: 'Your brain generated an explanation. Growth.'
		},
		{
			emoji: '🔍',
			situation: 'You need to write a regex to validate an email address.',
			goodChoice: 'Open regex101.com and build it yourself',
			badChoice: '"Claude write me an email regex pleeease" 🤖',
			goodResult: 'Regex from scratch. Are you even human?'
		},
		{
			emoji: '🐌',
			situation: 'A database query takes 8 seconds. Users are leaving.',
			goodChoice: 'Run EXPLAIN and analyze the query plan',
			badChoice: 'Paste the query to Claude, ask for magic 🤖',
			goodResult: 'EXPLAIN before complain. Senior move.'
		},
		{
			emoji: '🎨',
			situation: 'Your CSS is broken. Everything is 300px to the right for no reason.',
			goodChoice: 'Open DevTools and inspect every element',
			badChoice: 'Paste your CSS into Claude and beg 🤖',
			goodResult: 'DevTools inspection. Old school and beautiful.'
		},
		{
			emoji: '💡',
			situation: 'A coworker asks: "Do you know how async/await works?"',
			goodChoice: 'Actually explain promises and the event loop',
			badChoice: '"Brb" (frantically asking Claude to explain it first) 🤖',
			goodResult: 'You described async/await. Stunning.'
		},
		{
			emoji: '🤔',
			situation: 'You have a variable named "temp2". You wrote it yesterday.',
			goodChoice: 'Trace through the code to understand what it does',
			badChoice: 'Ask Claude to explain your own code to you 🤖',
			goodResult: 'No AI needed to understand your code!'
		},
		{
			emoji: '🚀',
			situation: 'A hot new framework just dropped. Your tech lead asks what you think.',
			goodChoice: 'Calmly evaluate if it solves your actual problems',
			badChoice: 'Ask Claude to migrate your entire codebase to it 🤖',
			goodResult: 'Thoughtful evaluation over hype. Maturity!'
		},
		{
			emoji: '📝',
			situation: 'Your boss asks you to write documentation for the project.',
			goodChoice: 'Open a text editor and document what you know',
			badChoice: 'Tell Claude to generate docs from your codebase 🤖',
			goodResult: 'Actually written by the person who built it!'
		}
	];

	type GameState = 'idle' | 'playing' | 'feedback' | 'lose' | 'win';

	let gameState = $state<GameState>('idle');
	let timeLeft = $state(60);
	let score = $state(0);
	let streak = $state(0);
	let maxStreak = $state(0);
	let survivedCount = $state(0);
	let currentScenario = $state<Scenario>(scenarios[0]);
	let scenarioKey = $state(0);
	let feedbackText = $state('');
	let lastScenario = $state<Scenario | null>(null);

	let displayIndex = 0;
	let shuffledScenarios: Scenario[] = [];
	let intervalId: ReturnType<typeof setInterval> | null = null;

	function shuffleArray<T>(arr: T[]): T[] {
		const copy = [...arr];
		for (let i = copy.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[copy[i], copy[j]] = [copy[j], copy[i]];
		}
		return copy;
	}

	function nextScenario() {
		displayIndex = (displayIndex + 1) % shuffledScenarios.length;
		currentScenario = shuffledScenarios[displayIndex];
		scenarioKey += 1;
	}

	function startGame() {
		if (intervalId) clearInterval(intervalId);
		shuffledScenarios = shuffleArray(scenarios);
		displayIndex = 0;
		currentScenario = shuffledScenarios[0];
		scenarioKey = 0;
		gameState = 'playing';
		timeLeft = 60;
		score = 0;
		streak = 0;
		maxStreak = 0;
		survivedCount = 0;
		lastScenario = null;

		intervalId = setInterval(() => {
			timeLeft -= 1;
			if (timeLeft <= 0) {
				clearInterval(intervalId!);
				intervalId = null;
				gameState = 'win';
			}
		}, 1000);
	}

	function handleGoodChoice() {
		if (gameState !== 'playing') return;
		const bonus = streak >= 3 ? streak * 2 : 0;
		const points = 10 + bonus;
		score += points;
		streak += 1;
		if (streak > maxStreak) maxStreak = streak;
		survivedCount += 1;
		feedbackText = `+${points} pts — ${currentScenario.goodResult}${bonus > 0 ? ` 🔥 x${streak} streak!` : ''}`;
		gameState = 'feedback';

		setTimeout(() => {
			if (gameState === 'feedback') {
				nextScenario();
				gameState = 'playing';
			}
		}, 1400);
	}

	function handleBadChoice() {
		if (gameState !== 'playing') return;
		lastScenario = currentScenario;
		if (intervalId) {
			clearInterval(intervalId);
			intervalId = null;
		}
		gameState = 'lose';
	}

	onMount(() => {
		return () => {
			if (intervalId) clearInterval(intervalId);
		};
	});

	let timerPercent = $derived((timeLeft / 60) * 100);
	let timerColor = $derived(timeLeft > 30 ? '#7ee787' : timeLeft > 15 ? '#f0c674' : '#f85149');

	function getWinMessage(s: number): string {
		if (s <= 50) return 'You barely made it. But you made it.';
		if (s <= 100) return 'Solid recovery. The docs are proud of you.';
		if (s <= 150) return "Impressive. We're nominating you for Sober Dev of the Year.";
		return "This is unprecedented. Are you even a vibe coder?";
	}
</script>

<div class="min-h-screen bg-[#0d1117] text-[#c9d1d9]">
	<!-- Top nav -->
	<div class="border-b border-[#21262d] px-6 py-4">
		<div class="max-w-2xl mx-auto">
			<a
				href="/"
				class="inline-flex items-center gap-2 text-[#8b949e] hover:text-[#58a6ff] transition-colors duration-200 font-mono text-sm"
			>
				<svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M15 19l-7-7 7-7"
					/>
				</svg>
				Back to Recovery
			</a>
		</div>
	</div>

	<div class="max-w-2xl mx-auto px-6 py-12 sm:py-16">
		<!-- Header -->
		<div class="text-center mb-10 sm:mb-12">
			<p class="font-mono text-[#f97316] text-xs sm:text-sm tracking-[0.25em] uppercase mb-4">
				// SURVIVAL_CHALLENGE.exe
			</p>
			<h1 class="font-display font-bold text-4xl sm:text-5xl text-[#e6edf3] mb-3 leading-tight">
				60 Seconds Sober
			</h1>
			<p class="text-[#8b949e] font-mono text-sm">
				Real developer scenarios. Zero AI allowed. Survive if you can.
			</p>
		</div>

		{#if gameState === 'idle'}
			<div in:scale={{ duration: 400, easing: backOut, start: 0.9 }}>
				<!-- Rules card -->
				<div class="bg-[#161b22] border border-[#30363d] rounded-2xl p-6 sm:p-8 mb-6">
					<p class="font-mono text-xs text-[#8b949e] uppercase tracking-wider mb-5">
						// HOW_TO_PLAY
					</p>
					<div class="space-y-4">
						{#each [
							{ icon: '⚡', text: 'Developer crises appear one at a time. 60 seconds on the clock.' },
							{
								icon: '✅',
								text: 'Choose the sober response to earn points and keep your streak.'
							},
							{
								icon: '🤖',
								text: "Click \"Ask Claude\" even ONCE and it's game over. You relapsed."
							},
							{ icon: '🔥', text: '3+ streak combos multiply your score. Brag accordingly.' }
						] as rule}
							<div class="flex items-start gap-4">
								<span class="text-lg flex-shrink-0">{rule.icon}</span>
								<p class="text-[#c9d1d9] text-sm leading-relaxed">{rule.text}</p>
							</div>
						{/each}
					</div>
				</div>

				<div class="text-center">
					<button
						onclick={startGame}
						class="px-12 py-5 bg-gradient-to-r from-[#f97316] to-[#ea580c] text-white font-bold text-lg rounded-xl hover:shadow-[0_0_40px_rgba(249,115,22,0.4)] hover:scale-105 transition-all duration-300"
					>
						I Can Do This →
					</button>
					<p class="mt-4 text-xs text-[#6e7681] font-mono">
						* No AI was used in the making of this challenge. Allegedly.
					</p>
				</div>
			</div>
		{:else if gameState === 'playing' || gameState === 'feedback'}
			<!-- Timer bar -->
			<div class="mb-6">
				<div class="flex items-center justify-between mb-2">
					<span class="font-mono text-xs text-[#8b949e]">
						⏱ <span style="color: {timerColor}" class="font-bold text-base transition-colors"
							>{timeLeft}s</span
						>
					</span>
					<div class="flex items-center gap-4">
						{#if streak >= 3}
							<span class="font-mono text-xs text-[#f97316]">🔥 x{streak} streak</span>
						{/if}
						<span class="font-mono text-xs text-[#8b949e]">
							Score: <span class="text-[#58a6ff] font-bold">{score}</span>
						</span>
					</div>
				</div>
				<div class="h-2 bg-[#21262d] rounded-full overflow-hidden">
					<div
						class="h-full rounded-full transition-all duration-1000 ease-linear"
						style="width: {timerPercent}%; background-color: {timerColor}"
					></div>
				</div>
			</div>

			<!-- Scenario card with key for animation -->
			{#key scenarioKey}
				<div
					in:fly={{ x: 60, duration: 300, easing: cubicOut }}
					out:fly={{ x: -60, duration: 200 }}
				>
					<!-- Scenario -->
					<div class="bg-[#161b22] border border-[#30363d] rounded-2xl p-6 sm:p-8 mb-5">
						<div class="flex items-start gap-4">
							<span class="text-4xl flex-shrink-0">{currentScenario.emoji}</span>
							<p class="font-display text-lg sm:text-xl text-[#e6edf3] leading-snug pt-1">
								{currentScenario.situation}
							</p>
						</div>
					</div>

					<!-- Choice buttons -->
					<div class="grid gap-3 sm:gap-4">
						<!-- Good choice (A) -->
						<button
							onclick={handleGoodChoice}
							disabled={gameState === 'feedback'}
							class="group w-full text-left p-5 rounded-xl border-2 transition-all duration-200
								{gameState === 'feedback'
								? 'bg-[#7ee787]/10 border-[#7ee787]/60 scale-[0.99]'
								: 'bg-[#161b22] border-[#30363d] hover:border-[#58a6ff]/50 hover:bg-[#21262d] hover:scale-[1.01] cursor-pointer'}"
						>
							<div class="flex items-center gap-4">
								<div
									class="flex-shrink-0 w-9 h-9 rounded-lg bg-[#21262d] border border-[#30363d] group-hover:border-[#58a6ff]/50 flex items-center justify-center transition-all duration-200"
								>
									<span
										class="font-mono text-sm font-bold text-[#8b949e] group-hover:text-[#58a6ff]"
										>A</span
									>
								</div>
								<span
									class="text-[#c9d1d9] group-hover:text-white text-sm sm:text-base transition-colors"
								>
									{currentScenario.goodChoice}
								</span>
								{#if gameState === 'feedback'}
									<span class="ml-auto flex-shrink-0" in:scale={{ duration: 200 }}>
										<svg
											class="w-5 h-5 text-[#7ee787]"
											fill="none"
											stroke="currentColor"
											viewBox="0 0 24 24"
										>
											<path
												stroke-linecap="round"
												stroke-linejoin="round"
												stroke-width="2.5"
												d="M5 13l4 4L19 7"
											/>
										</svg>
									</span>
								{/if}
							</div>
						</button>

						<!-- Bad choice (B) — styled temptingly -->
						<button
							onclick={handleBadChoice}
							disabled={gameState === 'feedback'}
							class="group w-full text-left p-5 rounded-xl border-2 transition-all duration-200
								{gameState === 'feedback'
								? 'opacity-40 cursor-not-allowed bg-[#161b22] border-[#30363d]'
								: 'bg-gradient-to-r from-[#1a1625] to-[#1e1530] border-[#7c3aed]/40 hover:border-[#a855f7]/70 hover:shadow-[0_0_20px_rgba(168,85,247,0.15)] hover:scale-[1.01] cursor-pointer'}"
						>
							<div class="flex items-center gap-4">
								<div
									class="flex-shrink-0 w-9 h-9 rounded-lg bg-[#a855f7]/10 border border-[#a855f7]/30 group-hover:border-[#a855f7]/60 group-hover:bg-[#a855f7]/20 flex items-center justify-center transition-all duration-200"
								>
									<span class="font-mono text-sm font-bold text-[#a855f7]">B</span>
								</div>
								<span
									class="text-[#c9d1d9] group-hover:text-[#d8b4fe] text-sm sm:text-base transition-colors"
								>
									{currentScenario.badChoice}
								</span>
							</div>
						</button>
					</div>
				</div>
			{/key}

			<!-- Feedback toast -->
			{#if gameState === 'feedback'}
				<div
					class="mt-5 p-4 rounded-xl bg-[#7ee787]/10 border border-[#7ee787]/30 text-center"
					in:fly={{ y: 10, duration: 300 }}
				>
					<p class="font-mono text-sm text-[#7ee787]">{feedbackText}</p>
				</div>
			{/if}
		{:else if gameState === 'win'}
			<div in:scale={{ duration: 500, easing: backOut, start: 0.85 }}>
				<div class="text-center mb-8">
					<div class="text-7xl mb-6">🏆</div>
					<div
						class="inline-flex items-center gap-2 px-5 py-2 rounded-full border border-[#7ee787]/40 bg-[#7ee787]/10 mb-5"
					>
						<span class="font-mono text-xs uppercase tracking-widest text-[#7ee787] font-bold"
							>60 Seconds Survived</span
						>
					</div>
					<h2 class="font-display font-bold text-3xl sm:text-4xl text-[#e6edf3] mb-3">
						You Did It.
					</h2>
					<p class="text-[#8b949e] font-mono text-sm">{getWinMessage(score)}</p>
				</div>

				<!-- Stats -->
				<div class="grid grid-cols-3 gap-4 mb-8">
					{#each [
						{ label: 'Final Score', value: score.toString(), color: 'text-[#58a6ff]' },
						{ label: 'Best Streak', value: `x${maxStreak}`, color: 'text-[#f97316]' },
						{ label: 'Survived', value: survivedCount.toString(), color: 'text-[#7ee787]' }
					] as stat}
						<div class="bg-[#161b22] border border-[#30363d] rounded-xl p-4 text-center">
							<div class="font-display font-bold text-2xl sm:text-3xl {stat.color} mb-1">
								{stat.value}
							</div>
							<div class="font-mono text-[10px] text-[#6e7681] uppercase tracking-wider">
								{stat.label}
							</div>
						</div>
					{/each}
				</div>

				<div class="flex flex-col sm:flex-row gap-4 justify-center">
					<button
						onclick={startGame}
						class="px-8 py-4 bg-gradient-to-r from-[#f97316] to-[#ea580c] text-white font-bold rounded-xl hover:shadow-[0_0_30px_rgba(249,115,22,0.35)] hover:scale-105 transition-all duration-300"
					>
						Play Again
					</button>
					<a
						href="/"
						class="px-8 py-4 border border-[#30363d] text-[#c9d1d9] font-bold rounded-xl hover:bg-[#21262d] hover:border-[#58a6ff]/50 transition-all duration-300 text-center hover:scale-105"
					>
						Back to Recovery
					</a>
				</div>
				<p class="text-center font-mono text-xs text-[#6e7681] mt-8">
					<span class="text-[#7ee787]">*</span> No AI was harmed in the making of your score.
				</p>
			</div>
		{:else if gameState === 'lose'}
			<div in:scale={{ duration: 400, easing: backOut, start: 0.9 }}>
				<div class="text-center mb-8">
					<div class="text-7xl mb-6">💀</div>
					<div
						class="inline-flex items-center gap-2 px-5 py-2 rounded-full border border-[#f85149]/40 bg-[#f85149]/10 mb-5"
					>
						<span class="w-2 h-2 rounded-full bg-[#f85149] animate-pulse"></span>
						<span class="font-mono text-xs uppercase tracking-widest text-[#f85149] font-bold"
							>Relapsed</span
						>
					</div>
					<h2 class="font-display font-bold text-3xl sm:text-4xl text-[#e6edf3] mb-3">
						It Got You.
					</h2>
					<p class="text-[#8b949e] font-mono text-sm max-w-sm mx-auto">
						The AI was right there. So shiny. So easy. We understand. We don't judge. Much.
					</p>
				</div>

				<!-- What tripped them up -->
				{#if lastScenario}
					<div class="bg-[#f85149]/5 border border-[#f85149]/20 rounded-2xl p-6 mb-6">
						<p class="font-mono text-xs text-[#f85149] uppercase tracking-wider mb-3">
							// MOMENT_OF_WEAKNESS
						</p>
						<p class="text-[#c9d1d9] mb-4 leading-relaxed">
							{lastScenario.emoji}
							{lastScenario.situation}
						</p>
						<div
							class="flex items-start gap-3 p-3 rounded-lg bg-[#0d1117] border border-[#30363d]"
						>
							<svg
								class="w-4 h-4 text-[#7ee787] flex-shrink-0 mt-0.5"
								fill="none"
								stroke="currentColor"
								viewBox="0 0 24 24"
							>
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									stroke-width="2"
									d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
								/>
							</svg>
							<p class="text-sm text-[#8b949e]">
								You could've: <span class="text-[#7ee787]">{lastScenario.goodChoice}</span>
							</p>
						</div>
					</div>
				{/if}

				<!-- Stats -->
				<div class="grid grid-cols-3 gap-4 mb-8">
					{#each [
						{ label: 'Score', value: score.toString(), color: 'text-[#f85149]' },
						{ label: 'Best Streak', value: `x${maxStreak}`, color: 'text-[#f97316]' },
						{ label: 'Survived', value: survivedCount.toString(), color: 'text-[#8b949e]' }
					] as stat}
						<div class="bg-[#161b22] border border-[#30363d] rounded-xl p-4 text-center">
							<div class="font-display font-bold text-2xl sm:text-3xl {stat.color} mb-1">
								{stat.value}
							</div>
							<div class="font-mono text-[10px] text-[#6e7681] uppercase tracking-wider">
								{stat.label}
							</div>
						</div>
					{/each}
				</div>

				<div class="flex flex-col sm:flex-row gap-4 justify-center">
					<button
						onclick={startGame}
						class="px-8 py-4 bg-gradient-to-r from-[#f97316] to-[#ea580c] text-white font-bold rounded-xl hover:shadow-[0_0_30px_rgba(249,115,22,0.35)] hover:scale-105 transition-all duration-300"
					>
						Try Again
					</button>
					<a
						href="/"
						class="px-8 py-4 border border-[#30363d] text-[#c9d1d9] font-bold rounded-xl hover:bg-[#21262d] hover:border-[#58a6ff]/50 transition-all duration-300 text-center hover:scale-105"
					>
						Back to Recovery
					</a>
				</div>
				<p class="text-center font-mono text-xs text-[#6e7681] mt-8">
					<span class="text-[#f97316]">*</span> Counter reset. We don't judge. Start again.
				</p>
			</div>
		{/if}
	</div>
</div>

<style>
	:global(body) {
		background-color: #0d1117;
	}
</style>
