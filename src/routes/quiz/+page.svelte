<script lang="ts">
	import { fly, fade, scale } from 'svelte/transition';
	import { cubicOut, backOut } from 'svelte/easing';

	interface Answer {
		text: string;
		points: number;
		reaction: string; // snarky comment shown after selecting
	}

	interface Question {
		text: string;
		subtext?: string;
		answers: Answer[];
	}

	const questions: Question[] = [
		{
			text: 'Your code throws an error you\'ve never seen before. What do you do?',
			subtext: 'Be honest. Nobody is watching. (We are watching.)',
			answers: [
				{
					text: 'Read the stack trace and debug methodically',
					points: 0,
					reaction: 'A classic. Respect. You\'re probably fine.'
				},
				{
					text: 'Google the error message',
					points: 1,
					reaction: 'Healthy. Stack Overflow is still a relationship, not a dependency.'
				},
				{
					text: 'Paste the entire error into Claude and say "fix"',
					points: 3,
					reaction: 'Ah. There it is. The one-word prompt. A cry for help dressed as a command.'
				},
				{
					text: 'Delete the file and ask AI to rewrite it from scratch',
					points: 5,
					reaction: 'SCORCHED EARTH DEBUGGING. The nuclear option. You are not okay.'
				}
			]
		},
		{
			text: 'How do you start a new project?',
			subtext: 'There are no wrong answers. (There are wrong answers.)',
			answers: [
				{
					text: 'Plan architecture, set up tooling, write the first file myself',
					points: 0,
					reaction: 'You still remember how folders work. Admirable.'
				},
				{
					text: 'Clone a starter template and customize it',
					points: 1,
					reaction: 'Pragmatic. This is fine. You\'re not the problem.'
				},
				{
					text: 'Tell Claude to scaffold the entire thing, then tweak it',
					points: 3,
					reaction: '"Tweak it." Sure. We believe you. Totally.'
				},
				{
					text: '"Claude, build me a startup. Make it profitable."',
					points: 5,
					reaction: 'Ambition. No knowledge. The vibe coder origin story.'
				}
			]
		},
		{
			text: 'A junior dev asks you to explain your code. You:',
			subtext: 'This one separates the humans from the... prompt engineers.',
			answers: [
				{
					text: 'Walk them through the logic clearly, line by line',
					points: 0,
					reaction: 'A mentor. A legend. A person who reads their own code.'
				},
				{
					text: 'Point them to the official docs',
					points: 1,
					reaction: 'Acceptable deflection. Docs are underrated anyway.'
				},
				{
					text: '"It\'s self-documenting." (Visible sweat.)',
					points: 3,
					reaction: '"Self-documenting" is developer for "please don\'t ask follow-up questions."'
				},
				{
					text: 'Admit Claude wrote it and you have no idea either',
					points: 5,
					reaction: 'The honesty is refreshing. The situation is not.'
				}
			]
		},
		{
			text: 'Your PR gets a code review comment: "Why did you use this approach?"',
			answers: [
				{
					text: 'Explain your reasoning with technical justification',
					points: 0,
					reaction: 'You... had reasoning. A novel concept around here.'
				},
				{
					text: '"Seemed like the cleanest option at the time"',
					points: 1,
					reaction: 'Vague but survivable. You\'ve done this before.'
				},
				{
					text: 'Secretly paste the comment into Claude to generate a response',
					points: 3,
					reaction: 'You used AI to respond to a comment about code that AI wrote. It\'s turtles all the way down.'
				},
				{
					text: 'Reply "vibes ✨" and approve your own PR',
					points: 5,
					reaction: 'You should not have merge permissions. We are revoking them spiritually.'
				}
			]
		},
		{
			text: 'How many consecutive lines of code can you write without AI assistance?',
			subtext: 'Think carefully. Really think.',
			answers: [
				{
					text: 'Hundreds, easily. I actually enjoy this.',
					points: 0,
					reaction: 'Wild. An endangered species. Please protect yourself.'
				},
				{
					text: 'Maybe 20–30 before I get tempted',
					points: 1,
					reaction: 'The temptation is normal. Resisting it sometimes is enough.'
				},
				{
					text: 'I can write a console.log() and then I need a break',
					points: 3,
					reaction: 'console.log is the coding equivalent of putting on pants. It\'s a start.'
				},
				{
					text: 'Does the prompt count as code?',
					points: 5,
					reaction: 'We need to have a very serious conversation about what "coding" means.'
				}
			]
		},
		{
			text: "What's your development setup?",
			answers: [
				{
					text: 'Neovim with a custom config I built over several years',
					points: 0,
					reaction: 'We see you. Terminal open, plugins configured, zero AI. A monument.'
				},
				{
					text: 'VS Code with a few extensions I actually use',
					points: 1,
					reaction: 'The people\'s IDE. Normal. Healthy. You\'re okay.'
				},
				{
					text: 'VS Code, but I only use the Copilot autocomplete tab',
					points: 3,
					reaction: 'VS Code is just a frame for the AI at this point. You know this.'
				},
				{
					text: 'A browser tab with Claude open. That IS the IDE.',
					points: 5,
					reaction: 'claude.ai/new is your home directory. Your src folder. Your everything.'
				}
			]
		},
		{
			text: "You're pair programming with a colleague. You:",
			subtext: 'An observer is present. Behavior unchanged?',
			answers: [
				{
					text: 'Take turns driving, discuss tradeoffs out loud',
					points: 0,
					reaction: 'Collaboration. Human collaboration. Remarkable.'
				},
				{
					text: 'Mostly watch and occasionally suggest things',
					points: 1,
					reaction: 'The backseat driver. Still present. Still human.'
				},
				{
					text: "Keep alt-tabbing to AI chat when they look away",
					points: 3,
					reaction: 'You have developed a covert operation for a text editor. Reflect on this.'
				},
				{
					text: 'Ask if Claude can join as a "third engineer"',
					points: 5,
					reaction: 'You invited an LLM to pair programming. This is not a red flag. It\'s a red banner.'
				}
			]
		},
		{
			text: 'Production is down. Alarms are firing. Slack is exploding. You:',
			subtext: 'The clock is ticking. What\'s your first instinct?',
			answers: [
				{
					text: 'Pull up logs, identify root cause, write and deploy a fix',
					points: 0,
					reaction: 'An incident commander. The person everyone calls. Respect.'
				},
				{
					text: 'Immediately revert the last deployment',
					points: 1,
					reaction: 'Git revert as a first principle. Battle-tested. Solid call.'
				},
				{
					text: 'Paste the error logs into Claude and wait for a miracle',
					points: 3,
					reaction: 'You are praying to a language model during a P0. This is your life now.'
				},
				{
					text: 'Ask Claude to write the incident postmortem before fixing the bug',
					points: 5,
					reaction: 'The post-mortem is already written. The service is still down. Priorities.'
				}
			]
		},
		{
			text: 'Someone asks "did you write this code yourself?"',
			answers: [
				{
					text: '"Yes." And it\'s true.',
					points: 0,
					reaction: 'You can say yes and mean it. That\'s rare now. That\'s something.'
				},
				{
					text: '"Mostly. I used AI for a few parts." (Honest.)',
					points: 1,
					reaction: 'The truth, delivered calmly. You\'re one of the good ones.'
				},
				{
					text: '"Yes." Technically Claude\'s fingers aren\'t on your keyboard.',
					points: 3,
					reaction: 'Plausible deniability. A lawyer\'s answer. A vibe coder\'s answer.'
				},
				{
					text: 'Pretend you didn\'t hear the question and change the subject',
					points: 5,
					reaction: 'The fifth amendment applies to developers now. Noted.'
				}
			]
		},
		{
			text: 'Final question. What does "vibe coding" mean to you?',
			subtext: 'This is your moment of truth.',
			answers: [
				{
					text: "I don't know what that is. I was linked here by someone.",
					points: 0,
					reaction: 'An innocent. Uncorrupted. Leave. While you still can.'
				},
				{
					text: "A meme I\'ve seen. Not really how I work.",
					points: 1,
					reaction: 'You can see the culture and remain outside it. Impressive discipline.'
				},
				{
					text: 'My actual development methodology, if I\'m honest.',
					points: 3,
					reaction: 'Honesty. Growth. The first step is acknowledgment.'
				},
				{
					text: "It's not a methodology. It's a lifestyle. It's who I am.",
					points: 5,
					reaction: 'Vibe coding has become your personality. We\'re calling your family.'
				}
			]
		}
	];

	interface ResultTier {
		label: string;
		emoji: string;
		color: string;
		badgeBg: string;
		badgeBorder: string;
		scoreColor: string;
		glowColor: string;
		headline: string;
		description: string;
		subDescription: string;
		prescription: string;
	}

	function getResult(total: number): ResultTier {
		if (total <= 5)
			return {
				label: 'Clean Coder',
				emoji: '🏅',
				color: 'text-[#7ee787]',
				badgeBg: 'bg-[#7ee787]/10',
				badgeBorder: 'border-[#7ee787]/40',
				scoreColor: 'text-[#7ee787]',
				glowColor: 'shadow-[0_0_60px_rgba(126,231,135,0.2)]',
				headline: 'You actually know what your code does.',
				description:
					"Impressive. You can open a blank file and write something that works. You read error messages before asking for help. You might even enjoy debugging.",
				subDescription: "Are you sure you\'re in the right place? This is a support group, not a trophy room.",
				prescription: 'Prescription: Keep going. Mentor someone. You\'re the hope.'
			};
		if (total <= 15)
			return {
				label: 'Casual User',
				emoji: '🤔',
				color: 'text-[#58a6ff]',
				badgeBg: 'bg-[#58a6ff]/10',
				badgeBorder: 'border-[#58a6ff]/40',
				scoreColor: 'text-[#58a6ff]',
				glowColor: 'shadow-[0_0_60px_rgba(88,166,255,0.2)]',
				headline: 'You dabble. A little Claude here, a little Copilot there.',
				description:
					"You use AI tools the way most people use spell-check — as a helper, not a replacement. You still remember how a for-loop works. You could survive without it.",
				subDescription: "You can stop anytime you want. (You probably can't, but it's nice to believe.)",
				prescription: 'Prescription: Attend one meeting per week. Preventative care.'
			};
		if (total <= 30)
			return {
				label: 'Dependent',
				emoji: '😬',
				color: 'text-[#f97316]',
				badgeBg: 'bg-[#f97316]/10',
				badgeBorder: 'border-[#f97316]/40',
				scoreColor: 'text-[#f97316]',
				glowColor: 'shadow-[0_0_60px_rgba(249,115,22,0.2)]',
				headline: 'Your Cmd+Tab muscle memory goes straight to Claude.',
				description:
					"You don't write code anymore — you describe code. You narrate what you want and then review what you get. You've started calling it 'directing' instead of 'coding'.",
				subDescription: "It started innocently. It always does.",
				prescription: 'Prescription: Daily meetings. Bring a friend. They\'re probably worse.'
			};
		if (total <= 40)
			return {
				label: 'Terminal Case',
				emoji: '🚨',
				color: 'text-[#f85149]',
				badgeBg: 'bg-[#f85149]/10',
				badgeBorder: 'border-[#f85149]/40',
				scoreColor: 'text-[#f85149]',
				glowColor: 'shadow-[0_0_60px_rgba(248,81,73,0.2)]',
				headline: 'You don\'t write code. You write prompts.',
				description:
					"Your GitHub contribution graph is technically accurate, but spiritually empty. You've committed code you cannot explain, deployed things you don't understand, and once, in a moment of clarity, tried to read what the AI wrote. You closed the tab.",
				subDescription: "Welcome to Vibe Coders Anonymous. You are home.",
				prescription: 'Prescription: Intensive residential program. We have beds available.'
			};
		return {
			label: 'Beyond Recovery',
			emoji: '☠️',
			color: 'text-[#a855f7]',
			badgeBg: 'bg-[#a855f7]/10',
			badgeBorder: 'border-[#a855f7]/40',
			scoreColor: 'text-[#a855f7]',
			glowColor: 'shadow-[0_0_80px_rgba(168,85,247,0.25)]',
			headline: 'You asked Claude to take this quiz for you, didn\'t you.',
			description:
				"We can't help you. We've tried. The program doesn't reach this far. You've achieved a kind of enlightenment — a perfect, complete surrender of all agency. You are the prompt. There is no you without the AI.",
			subDescription: "Honestly? Respect the commitment. This is pure, undiluted vibe.",
			prescription: 'Prescription: You are the prescription. For others. As a warning.'
		};
	}

	let currentQuestion = $state(0);
	let score = $state(0);
	let quizComplete = $state(false);
	let selectedAnswer = $state(-1);
	let showReaction = $state(false);
	let currentReaction = $state('');
	let copySuccess = $state(false);

	let progressPercent = $derived(((currentQuestion) / questions.length) * 100);
	let result = $derived(getResult(score));

	function selectAnswer(index: number, points: number, reaction: string) {
		if (selectedAnswer !== -1) return;
		selectedAnswer = index;
		score += points;
		currentReaction = reaction;
		showReaction = true;

		setTimeout(() => {
			showReaction = false;
			setTimeout(() => {
				if (currentQuestion + 1 >= questions.length) {
					quizComplete = true;
				} else {
					currentQuestion += 1;
				}
				selectedAnswer = -1;
			}, 200);
		}, 1800);
	}

	function retake() {
		score = 0;
		selectedAnswer = -1;
		quizComplete = false;
		currentQuestion = 0;
		showReaction = false;
		currentReaction = '';
		copySuccess = false;
	}

	function copyDiagnosis() {
		const text = `I just took the Vibe Coder Diagnostic and got: "${result.label}" (${score} pts)\n\n"${result.headline}"\n\nTake the test: vibecodersanonymous.com/quiz`;
		navigator.clipboard.writeText(text).then(() => {
			copySuccess = true;
			setTimeout(() => (copySuccess = false), 2500);
		});
	}
</script>

<div class="min-h-screen bg-[#0d1117] text-[#c9d1d9]">
	<!-- Top nav -->
	<div class="border-b border-[#21262d] px-6 py-4">
		<div class="max-w-3xl mx-auto">
			<a
				href="/"
				class="inline-flex items-center gap-2 text-[#8b949e] hover:text-[#58a6ff] transition-colors duration-200 font-mono text-sm"
			>
				<svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
					<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
				</svg>
				Back to Recovery
			</a>
		</div>
	</div>

	<div class="max-w-3xl mx-auto px-6 py-12 sm:py-16">
		<!-- Header -->
		<div class="text-center mb-10 sm:mb-14">
			<p class="font-mono text-[#f97316] text-xs sm:text-sm tracking-[0.25em] uppercase mb-4">
				// DIAGNOSTIC_TEST.exe
			</p>
			<h1 class="font-display font-bold text-3xl sm:text-4xl md:text-5xl text-[#e6edf3] mb-4 leading-tight">
				The Vibe Coder Diagnostic
			</h1>
			<p class="text-[#8b949e] text-sm sm:text-base font-mono">
				Answer honestly. Your IDE history is already being reviewed.
			</p>
		</div>

		{#if !quizComplete}
			<!-- Progress bar -->
			<div class="mb-8 sm:mb-10">
				<div class="flex items-center justify-between mb-2">
					<span class="font-mono text-xs text-[#8b949e]">
						Question <span class="text-[#c9d1d9]">{currentQuestion + 1}</span> of <span class="text-[#c9d1d9]">{questions.length}</span>
					</span>
					<span class="font-mono text-xs text-[#8b949e]">
						{Math.round(progressPercent)}% complete
					</span>
				</div>
				<div class="h-1.5 bg-[#21262d] rounded-full overflow-hidden">
					<div
						class="h-full bg-gradient-to-r from-[#f97316] to-[#ea580c] rounded-full transition-all duration-500 ease-out"
						style="width: {progressPercent}%"
					></div>
				</div>
			</div>

			<!-- Question card -->
			{#key currentQuestion}
				<div
					in:fly={{ x: 60, duration: 300, easing: cubicOut }}
					out:fly={{ x: -60, duration: 200, easing: cubicOut }}
				>
					<div class="bg-[#161b22] border border-[#30363d] rounded-2xl p-6 sm:p-8 mb-4">
						<div class="flex items-start gap-4">
							<span class="flex-shrink-0 font-mono text-[#f97316]/60 text-xs font-bold mt-1.5">
								{String(currentQuestion + 1).padStart(2, '0')}
							</span>
							<div>
								<h2 class="font-display text-lg sm:text-xl md:text-2xl text-[#e6edf3] leading-snug mb-1">
									{questions[currentQuestion].text}
								</h2>
								{#if questions[currentQuestion].subtext}
									<p class="font-mono text-xs text-[#6e7681] italic">{questions[currentQuestion].subtext}</p>
								{/if}
							</div>
						</div>
					</div>

					<!-- Answer options -->
					<div class="grid gap-2.5">
						{#each questions[currentQuestion].answers as answer, i}
							{@const isSelected = selectedAnswer === i}
							{@const isDisabled = selectedAnswer !== -1}
							{@const isDimmed = isDisabled && !isSelected}
							<button
								onclick={() => selectAnswer(i, answer.points, answer.reaction)}
								disabled={isDisabled}
								class="group w-full text-left p-4 sm:p-5 rounded-xl border transition-all duration-200
									{isSelected
										? 'bg-[#7ee787]/8 border-[#7ee787]/50'
										: isDimmed
											? 'bg-[#161b22] border-[#21262d] opacity-30 cursor-not-allowed'
											: 'bg-[#161b22] border-[#30363d] hover:border-[#f97316]/40 hover:bg-[#1c2128] cursor-pointer'}"
							>
								<div class="flex items-center gap-3.5">
									<span
										class="flex-shrink-0 w-7 h-7 rounded-lg border font-mono text-xs font-bold flex items-center justify-center transition-all duration-200
											{isSelected
												? 'bg-[#7ee787] border-[#7ee787] text-[#0d1117]'
												: isDimmed
													? 'border-[#21262d] text-[#3d444d]'
													: 'border-[#30363d] text-[#6e7681] group-hover:border-[#f97316]/50 group-hover:text-[#f97316]'}"
									>
										{String.fromCharCode(65 + i)}
									</span>
									<span
										class="text-sm sm:text-base transition-colors duration-200 leading-snug
											{isSelected
												? 'text-[#7ee787]'
												: isDimmed
													? 'text-[#3d444d]'
													: 'text-[#c9d1d9] group-hover:text-[#e6edf3]'}"
									>
										{answer.text}
									</span>
									{#if isSelected}
										<span class="ml-auto flex-shrink-0" in:scale={{ duration: 200, easing: backOut }}>
											<svg class="w-4 h-4 text-[#7ee787]" fill="currentColor" viewBox="0 0 20 20">
												<path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
											</svg>
										</span>
									{/if}
								</div>
							</button>
						{/each}
					</div>
				</div>
			{/key}

			<!-- Reaction toast -->
			{#if showReaction}
				<div
					in:fly={{ y: 12, duration: 300, easing: cubicOut }}
					out:fade={{ duration: 200 }}
					class="mt-6 bg-[#161b22] border border-[#f97316]/30 rounded-xl px-5 py-4 flex items-start gap-3"
				>
					<span class="text-[#f97316] font-mono text-sm mt-0.5 flex-shrink-0">▶</span>
					<p class="font-mono text-sm text-[#c9d1d9] leading-relaxed italic">
						{currentReaction}
					</p>
				</div>
			{/if}

		{:else}
			<!-- Results screen -->
			<div in:scale={{ duration: 500, easing: backOut, start: 0.9 }}>
				<!-- Score display -->
				<div class="text-center mb-8">
					<div class="inline-block mb-5">
						<div
							class="relative w-28 h-28 mx-auto rounded-full border-2 {result.badgeBorder} {result.badgeBg} {result.glowColor} flex items-center justify-center"
						>
							<div class="text-center">
								<div class="text-3xl mb-0.5">{result.emoji}</div>
							</div>
						</div>
					</div>

					<div
						class="inline-flex items-center gap-2 px-5 py-2 rounded-full border {result.badgeBorder} {result.badgeBg} mb-5"
					>
						<span class="font-mono text-xs uppercase tracking-widest {result.color} font-bold">
							{result.label}
						</span>
					</div>

					<h2 class="font-display font-bold text-2xl sm:text-3xl text-[#e6edf3] mb-1 px-4">
						{result.headline}
					</h2>
					<p class="font-mono text-xs text-[#6e7681] tracking-wider">
						Score: <span class="{result.scoreColor} font-bold">{score}</span> / 50 pts
					</p>
				</div>

				<!-- Result description -->
				<div class="bg-[#161b22] border {result.badgeBorder} rounded-2xl p-6 sm:p-8 mb-4 relative overflow-hidden">
					<div class="absolute inset-0 bg-[radial-gradient(ellipse_at_top_right,_rgba(249,115,22,0.03)_0%,_transparent_65%)]"></div>
					<div class="relative z-10 space-y-3">
						<p class="text-[#c9d1d9] text-base sm:text-lg leading-relaxed font-display">
							{result.description}
						</p>
						<p class="text-[#8b949e] text-sm leading-relaxed italic font-mono">
							{result.subDescription}
						</p>
					</div>
				</div>

				<!-- Prescription -->
				<div class="bg-[#0d1117] border border-[#30363d] rounded-xl px-5 py-4 mb-8 flex items-center gap-3">
					<span class="font-mono text-[#58a6ff] text-sm flex-shrink-0">Rx</span>
					<p class="font-mono text-sm text-[#8b949e] leading-snug">{result.prescription}</p>
				</div>

				<!-- Score spectrum -->
				<div class="bg-[#161b22] border border-[#30363d] rounded-2xl p-5 sm:p-6 mb-8">
					<p class="font-mono text-xs text-[#6e7681] uppercase tracking-wider mb-4">// Severity Spectrum</p>
					<div class="flex gap-1.5 mb-3">
						{#each [
							{ max: 5, color: 'bg-[#7ee787]', label: 'Clean' },
							{ max: 15, color: 'bg-[#58a6ff]', label: 'Casual' },
							{ max: 30, color: 'bg-[#f97316]', label: 'Dependent' },
							{ max: 40, color: 'bg-[#f85149]', label: 'Terminal' },
							{ max: 50, color: 'bg-[#a855f7]', label: 'Beyond' }
						] as tier, i}
							{@const ranges = [5, 15, 30, 40, 50]}
							{@const mins = [0, 6, 16, 31, 41]}
							{@const isActive = score >= mins[i] && score <= ranges[i]}
							<div class="flex-1 text-center">
								<div
									class="h-2 rounded-full mb-1.5 transition-all duration-500 {isActive ? tier.color : 'bg-[#21262d]'}"
								></div>
								<p class="font-mono text-[9px] text-[#4d5566] leading-tight hidden sm:block">{tier.label}</p>
							</div>
						{/each}
					</div>
				</div>

				<!-- Action buttons -->
				<div class="flex flex-col sm:flex-row gap-3 justify-center">
					<button
						onclick={retake}
						class="px-7 py-3.5 bg-gradient-to-r from-[#f97316] to-[#ea580c] text-white font-bold rounded-xl hover:shadow-[0_0_30px_rgba(249,115,22,0.3)] hover:scale-[1.02] transition-all duration-300 text-sm"
					>
						Retake the Test
					</button>
					<button
						onclick={copyDiagnosis}
						class="px-7 py-3.5 border border-[#30363d] text-[#c9d1d9] font-bold rounded-xl hover:bg-[#161b22] hover:border-[#58a6ff]/40 transition-all duration-300 text-sm flex items-center justify-center gap-2"
					>
						{#if copySuccess}
							<svg class="w-4 h-4 text-[#7ee787]" fill="currentColor" viewBox="0 0 20 20">
								<path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
							</svg>
							<span class="text-[#7ee787]">Copied!</span>
						{:else}
							<svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
								<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z" />
							</svg>
							Share Diagnosis
						{/if}
					</button>
					<a
						href="/"
						class="px-7 py-3.5 border border-[#30363d] text-[#c9d1d9] font-bold rounded-xl hover:bg-[#161b22] hover:border-[#30363d] transition-all duration-300 text-sm text-center"
					>
						Back to Recovery
					</a>
				</div>

				<p class="text-center font-mono text-xs text-[#4d5566] mt-8">
					<span class="text-[#f97316]">*</span> Results are 100% scientifically accurate. We have a PhD in vibes.
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
