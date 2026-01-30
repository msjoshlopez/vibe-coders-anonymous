<script lang="ts">
	import { onMount } from 'svelte';
	import { fly, fade, scale } from 'svelte/transition';

	interface Step {
		number: number;
		text: string;
	}

	interface Testimonial {
		quote: string;
		author: string;
		days: string;
		role: string;
		featured?: boolean;
	}

	interface WarningSign {
		text: string;
		checked: boolean;
	}

	let daysSober = $state(3);
	let mounted = $state(false);
	let showRelapse = $state(false);

	// Track which sections are visible for animations
	let visibleSections = $state<Set<string>>(new Set());

	let warningSigns = $state<WarningSign[]>([
		{ text: "You've said 'Claude, fix this' more than 5 times in a row", checked: false },
		{ text: "You don't know what language your app is written in", checked: false },
		{ text: "Your git commits say 'made changes' or 'fixed stuff'", checked: false },
		{ text: "You ask AI to explain code you 'wrote' yesterday", checked: false },
		{
			text: "You've never seen a syntax error you didn't immediately paste into chat",
			checked: false
		},
		{ text: "Your debugging process is 'send the entire error message to Claude'", checked: false },
		{ text: "You call yourself a 'prompt engineer' on LinkedIn unironically", checked: false },
		{ text: "You've shipped to production code you've never read", checked: false },
		{ text: 'Your IDE is basically a chat window now', checked: false },
		{ text: "You panic when the AI is 'at capacity'", checked: false }
	]);

	let checkedCount = $derived(warningSigns.filter((s) => s.checked).length);

	onMount(() => {
		mounted = true;
		daysSober = Math.floor(Math.random() * 4);

		// Set up intersection observer for scroll animations
		const observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						visibleSections.add(entry.target.id);
						visibleSections = new Set(visibleSections);
					}
				});
			},
			{ threshold: 0.1, rootMargin: '0px 0px -50px 0px' }
		);

		document.querySelectorAll('section[id]').forEach((section) => {
			observer.observe(section);
		});

		return () => observer.disconnect();
	});

	function handleRelapse() {
		daysSober = 0;
		showRelapse = true;
		setTimeout(() => (showRelapse = false), 3000);
	}

	function getDiagnosisMessage(count: number): { text: string; color: string } {
		if (count === 0) return { text: 'Check any that apply to you...', color: 'text-[#8b949e]' };
		if (count <= 2) return { text: 'You might be okay. Maybe.', color: 'text-[#7ee787]' };
		if (count <= 4)
			return { text: 'Concerning. Consider attending a meeting.', color: 'text-[#f0c674]' };
		if (count <= 6)
			return { text: "This is serious. We're here for you.", color: 'text-[#f97316]' };
		if (count <= 8) return { text: 'Please, sit down. We need to talk.', color: 'text-[#f85149]' };
		return { text: "Oh no. OH NO. We're sending help immediately.", color: 'text-[#ff7b72]' };
	}

	const steps: Step[] = [
		{
			number: 1,
			text: 'We admitted we were powerless over Claude—that our codebases had become unmanageable.'
		},
		{ number: 2, text: 'Came to believe that reading documentation could restore us to sanity.' },
		{
			number: 3,
			text: 'Made a decision to turn our terminal over to the care of actual understanding.'
		},
		{ number: 4, text: 'Made a searching and fearless inventory of our node_modules folder.' },
		{
			number: 5,
			text: 'Admitted to ourselves, and to another human being, the exact nature of our copy-paste.'
		},
		{ number: 6, text: 'Were entirely ready to have our IDE remove all these console.logs.' },
		{ number: 7, text: 'Humbly asked Stack Overflow to remove our shortcomings.' },
		{
			number: 8,
			text: 'Made a list of all the codebases we had harmed, and became willing to refactor them all.'
		},
		{
			number: 9,
			text: 'Made direct amends to such codebases wherever possible, except when to do so would break production.'
		},
		{
			number: 10,
			text: 'Continued to take personal inventory of our git history and when we were wrong promptly admitted it.'
		},
		{
			number: 11,
			text: 'Sought through tutorials and documentation to improve our conscious contact with the language specification.'
		},
		{
			number: 12,
			text: 'Having had a technical awakening as the result of these Steps, we tried to carry this message to other vibe coders.'
		}
	];

	const stepPhases = [
		{
			name: 'Acceptance',
			range: 'Steps 1-4',
			description: 'Recognizing the problem',
			steps: steps.slice(0, 4)
		},
		{ name: 'Action', range: 'Steps 5-8', description: 'Making changes', steps: steps.slice(4, 8) },
		{
			name: 'Growth',
			range: 'Steps 9-12',
			description: 'Maintaining recovery',
			steps: steps.slice(8, 12)
		}
	];

	const testimonials: Testimonial[] = [
		{
			quote:
				"I used to mass-produce code like a factory. Ship fast, break things, let Claude figure it out. Then one day, my entire production database got wiped because of code I never read. I lost everything. That was 47 days ago. Now I read every line before it ships. It's slower, but I sleep at night.",
			author: 'Anonymous',
			days: 'Day 47',
			role: "Former 'Full Stack Developer'",
			featured: true
		},
		{
			quote:
				'My first line of code I actually wrote myself was a console.log. I cried. It was beautiful.',
			author: 'Sarah M.',
			days: '3 months clean',
			role: 'Recovering Prompt Engineer'
		},
		{
			quote:
				"I thought 'npm install' was actual programming. I was a 0x developer with AI doing 10x.",
			author: 'Jake R.',
			days: '6 months in recovery',
			role: 'Ex-Vibe Architect'
		},
		{
			quote:
				"I asked Claude to explain code I 'wrote' the day before. It couldn't. Neither could I.",
			author: 'DevBro2024',
			days: 'Day 12',
			role: 'LinkedIn Thought Leader'
		}
	];
</script>

<div class="min-h-screen bg-[#0d1117] text-[#c9d1d9] overflow-x-hidden">
	<!-- Hero Section -->
	<header class="relative min-h-screen flex items-center justify-center overflow-hidden">
		<!-- Animated background gradient -->
		<div
			class="absolute inset-0 bg-[radial-gradient(ellipse_at_center,_rgba(88,166,255,0.08)_0%,_transparent_50%)]"
		></div>
		<div
			class="absolute inset-0 bg-[radial-gradient(ellipse_at_top_right,_rgba(249,115,22,0.05)_0%,_transparent_40%)]"
		></div>

		<!-- Subtle grid pattern -->
		<div
			class="absolute inset-0 opacity-[0.02]"
			style="background-image: linear-gradient(#fff 1px, transparent 1px), linear-gradient(90deg, #fff 1px, transparent 1px); background-size: 60px 60px;"
		></div>

		<!-- Floating code snippets -->
		<div class="absolute inset-0 overflow-hidden pointer-events-none select-none">
			<div
				class="absolute top-[15%] left-[8%] font-mono text-sm text-[#58a6ff]/[0.07] rotate-[-12deg] animate-float-slow"
			>
				Claude.fix(everything)
			</div>
			<div
				class="absolute top-[25%] right-[12%] font-mono text-sm text-[#f97316]/[0.07] rotate-[8deg] animate-float-slower"
			>
				// TODO: understand this later
			</div>
			<div
				class="absolute top-[55%] left-[5%] font-mono text-sm text-[#7ee787]/[0.07] rotate-[3deg] animate-float"
			>
				git commit -m "stuff"
			</div>
			<div
				class="absolute top-[65%] right-[8%] font-mono text-sm text-[#58a6ff]/[0.07] rotate-[-6deg] animate-float-slow"
			>
				npm install hope --save-dev
			</div>
			<div
				class="absolute top-[35%] left-[75%] font-mono text-sm text-[#f97316]/[0.07] rotate-[15deg] animate-float-slower"
			>
				console.log("why tho")
			</div>
			<div
				class="absolute top-[80%] left-[20%] font-mono text-sm text-[#7ee787]/[0.07] rotate-[-4deg] animate-float"
			>
				// it works, don't touch it
			</div>
			<div
				class="absolute top-[45%] left-[3%] font-mono text-sm text-[#58a6ff]/[0.07] rotate-[10deg] animate-float-slower"
			>
				await claude.pray()
			</div>
			<div
				class="absolute top-[10%] right-[25%] font-mono text-sm text-[#f97316]/[0.07] rotate-[-8deg] animate-float"
			>
				try {'{'} vibeCode() {'}'}
			</div>
		</div>

		<div class="relative z-10 max-w-4xl mx-auto px-6 text-center">
			{#if mounted}
				<!-- Sobriety chip medallion -->
				<div class="mb-8 sm:mb-10" in:scale={{ duration: 600, delay: 200 }}>
					<button
						onclick={handleRelapse}
						class="group relative cursor-pointer"
						title="Click to relapse"
					>
						<div class="relative">
							<!-- Outer glow -->
							<div
								class="absolute -inset-4 bg-amber-500/20 rounded-full blur-2xl group-hover:bg-amber-500/30 transition-all duration-500 animate-pulse-slow"
							></div>

							<!-- Medallion -->
							<div
								class="relative w-28 h-28 sm:w-32 sm:h-32 mx-auto rounded-full bg-gradient-to-br from-amber-500 via-amber-400 to-amber-600 p-1 shadow-[0_0_60px_rgba(245,158,11,0.3)] group-hover:shadow-[0_0_80px_rgba(245,158,11,0.5)] transition-all duration-500 group-hover:scale-105"
							>
								<div
									class="w-full h-full rounded-full bg-gradient-to-br from-amber-600 via-amber-500 to-amber-700 flex items-center justify-center border-4 border-amber-400/60 shadow-inner"
								>
									<div class="text-center">
										<div class="text-4xl sm:text-5xl font-bold text-amber-100 font-display drop-shadow-lg">
											{daysSober}
										</div>
										<div class="text-[10px] uppercase tracking-wider text-amber-200/90 font-medium">
											days without<br />prompting
										</div>
									</div>
								</div>
							</div>

							<!-- VCA badge -->
							<div
								class="absolute -bottom-1 left-1/2 -translate-x-1/2 bg-gradient-to-r from-amber-600 to-amber-500 text-amber-100 text-[10px] px-4 py-1.5 rounded-full uppercase tracking-widest font-bold shadow-lg"
							>
								VCA
							</div>
						</div>
					</button>
				</div>

				<!-- Relapse message -->
				{#if showRelapse}
					<div
						class="fixed top-8 left-1/2 -translate-x-1/2 z-50 bg-[#f85149] text-white px-6 py-3 rounded-lg shadow-2xl font-mono text-sm"
						in:fly={{ y: -20, duration: 300 }}
						out:fade={{ duration: 200 }}
					>
						Counter reset. We don't judge. Start again.
					</div>
				{/if}

				<p
					class="text-sm font-mono text-[#58a6ff] mb-6 tracking-[0.3em] uppercase"
					in:fade={{ duration: 400, delay: 400 }}
				>
					A Safe Space for Developers
				</p>

				<h1
					class="text-5xl sm:text-6xl md:text-7xl font-display font-bold mb-5 sm:mb-6 leading-[0.95] tracking-tight"
					in:fly={{ y: 30, duration: 600, delay: 500 }}
				>
					<span
						class="bg-gradient-to-b from-white via-[#e6edf3] to-[#8b949e] bg-clip-text text-transparent"
						>Vibe Coders</span
					>
					<br />
					<span class="bg-gradient-to-b from-[#c9d1d9] to-[#6e7681] bg-clip-text text-transparent"
						>Anonymous</span
					>
				</h1>

				<div class="max-w-2xl mx-auto mb-6 sm:mb-8" in:fade={{ duration: 400, delay: 700 }}>
					<p
						class="text-xl sm:text-2xl md:text-3xl text-[#8b949e] italic font-display leading-snug"
					>
						"Hi, I'm <span class="text-[#58a6ff] font-semibold">[name]</span>.<br />
						And I'm a <span class="text-[#f97316] font-semibold">vibe coder</span>."
					</p>
				</div>

				<p
					class="text-sm sm:text-base text-[#8b949e] mb-6 mx-auto whitespace-nowrap"
					in:fade={{ duration: 400, delay: 800 }}
				>
					A support community for developers recovering from AI-dependent coding.
					<span class="text-[#c9d1d9] font-semibold block mt-3"
						>You're not alone. Recovery is possible.</span
					>
				</p>

				<div
					class="flex flex-col sm:flex-row gap-4 justify-center"
					in:fly={{ y: 20, duration: 400, delay: 900 }}
				>
					<a
						href="#twelve-steps"
						class="group relative px-10 py-4 bg-gradient-to-r from-[#f97316] to-[#ea580c] text-white font-bold rounded-xl overflow-hidden transition-all duration-300 hover:shadow-[0_0_40px_rgba(249,115,22,0.4)] hover:scale-105"
					>
						<span class="relative z-10">Begin Recovery</span>
					</a>
					<a
						href="#warning-signs"
						class="px-10 py-4 border-2 border-[#30363d] text-[#c9d1d9] font-bold rounded-xl hover:bg-[#21262d] hover:border-[#58a6ff]/50 transition-all duration-300 hover:scale-105"
					>
						Am I a Vibe Coder?
					</a>
				</div>
			{/if}
		</div>

	</header>

	<!-- The Problem - Terminal style -->
	<section id="confession" class="py-32 px-6 bg-gradient-to-b from-[#0d1117] to-[#161b22]">
		<div
			class="max-w-3xl mx-auto {visibleSections.has('confession')
				? 'animate-fade-in-up'
				: 'opacity-0'}"
		>
			<div
				class="bg-[#0d1117] rounded-2xl border border-[#30363d] overflow-hidden shadow-2xl shadow-black/50"
			>
				<!-- Terminal header -->
				<div class="flex items-center gap-2 px-4 py-3 bg-[#161b22] border-b border-[#30363d]">
					<div class="w-3 h-3 rounded-full bg-[#ff5f56] shadow-[0_0_8px_rgba(255,95,86,0.5)]"></div>
					<div
						class="w-3 h-3 rounded-full bg-[#ffbd2e] shadow-[0_0_8px_rgba(255,189,46,0.5)]"
					></div>
					<div class="w-3 h-3 rounded-full bg-[#27c93f] shadow-[0_0_8px_rgba(39,201,63,0.5)]"></div>
					<span class="ml-3 text-xs text-[#8b949e] font-mono">~/recovery/confession.sh</span>
				</div>
				<!-- Terminal content -->
				<div class="p-8 font-mono text-sm sm:text-base leading-loose">
					<p class="text-[#7ee787]">$ cat my_story.txt</p>
					<div class="mt-6 space-y-4">
						<p class="text-[#e6edf3]">
							It started innocently. A quick prompt here. A <span class="text-[#ffa657]"
								>"fix this for me"</span
							> there.
						</p>
						<p class="text-[#8b949e]">
							Before you knew it, you were shipping entire applications without understanding a
							single line of code.
						</p>
						<p class="text-[#8b949e]">
							You told yourself you were being <span class="text-[#a5d6ff]">"efficient"</span>.
						</p>
						<p class="text-[#8b949e]">
							You called it <span class="text-[#a5d6ff]">"leveraging AI"</span>.
						</p>
						<p class="text-[#8b949e]">
							Your LinkedIn said <span class="text-[#a5d6ff]">"10x developer"</span>.
						</p>
						<p class="text-[#8b949e] mt-6">But deep down, you knew the truth:</p>
						<p class="text-[#f97316] text-xl sm:text-2xl font-bold mt-2">You were just vibing.</p>
					</div>
					<p class="text-[#7ee787] mt-8">$ <span class="animate-pulse">▋</span></p>
				</div>
			</div>
		</div>
	</section>

	<!-- Warning Signs - Interactive checklist -->
	<section id="warning-signs" class="py-32 px-6 scroll-mt-8">
		<div
			class="max-w-4xl mx-auto {visibleSections.has('warning-signs')
				? 'animate-fade-in-up'
				: 'opacity-0'}"
		>
			<div class="text-center mb-16">
				<p class="text-[#f97316] font-mono text-sm mb-4 tracking-wider">// SELF_DIAGNOSIS.exe</p>
				<h2 class="text-4xl sm:text-5xl md:text-6xl font-display font-bold text-white mb-6">
					Warning Signs
				</h2>
				<p class="text-[#8b949e] text-lg max-w-xl mx-auto">
					Identify your symptoms. Check all that apply. Be honest—your recovery depends on it.
				</p>
			</div>

			<!-- Diagnosis meter -->
			<div class="mb-10 p-6 rounded-2xl bg-[#161b22] border border-[#30363d]">
				<div class="flex items-center justify-between mb-4">
					<span class="text-sm font-mono text-[#8b949e]">DIAGNOSIS STATUS</span>
					<span class="text-sm font-mono text-[#58a6ff]">{checkedCount}/10 symptoms</span>
				</div>
				<div class="h-3 bg-[#21262d] rounded-full overflow-hidden mb-4">
					<div
						class="h-full rounded-full transition-all duration-500 ease-out {checkedCount <= 2
							? 'bg-[#7ee787]'
							: checkedCount <= 5
								? 'bg-[#f0c674]'
								: checkedCount <= 7
									? 'bg-[#f97316]'
									: 'bg-[#f85149]'}"
						style="width: {checkedCount * 10}%"
					></div>
				</div>
				<p
					class="text-center font-mono {getDiagnosisMessage(checkedCount)
						.color} transition-colors duration-300"
				>
					{getDiagnosisMessage(checkedCount).text}
				</p>
			</div>

			<div class="grid gap-4 sm:grid-cols-2">
				{#each warningSigns as sign, i (i)}
					<button
						onclick={() => (sign.checked = !sign.checked)}
						class="group flex items-start gap-4 p-5 rounded-xl border-2 text-left transition-all duration-300 {sign.checked
							? 'bg-[#f97316]/10 border-[#f97316]/50'
							: 'bg-[#161b22] border-[#30363d] hover:border-[#58a6ff]/30 hover:bg-[#21262d]'}"
					>
						<div class="relative flex-shrink-0 mt-0.5">
							<div
								class="w-6 h-6 rounded-md border-2 transition-all duration-300 flex items-center justify-center {sign.checked
									? 'bg-[#f97316] border-[#f97316]'
									: 'border-[#30363d] group-hover:border-[#58a6ff]/50'}"
							>
								{#if sign.checked}
									<svg
										class="w-4 h-4 text-white"
										fill="none"
										stroke="currentColor"
										viewBox="0 0 24 24"
										in:scale={{ duration: 200 }}
									>
										<path
											stroke-linecap="round"
											stroke-linejoin="round"
											stroke-width="3"
											d="M5 13l4 4L19 7"
										/>
									</svg>
								{/if}
							</div>
						</div>
						<span
							class="transition-colors duration-300 {sign.checked
								? 'text-[#e6edf3]'
								: 'text-[#c9d1d9] group-hover:text-white'}">{sign.text}</span
						>
					</button>
				{/each}
			</div>

			<p class="text-center text-[#6e7681] text-sm mt-10 font-mono">
				<span class="text-[#f97316]">*</span> Your shame is encrypted end-to-end. We promise.
			</p>
		</div>
	</section>

	<!-- The 12 Steps - Phased approach -->
	<section id="twelve-steps" class="py-32 px-6 bg-[#161b22] scroll-mt-8">
		<div
			class="max-w-5xl mx-auto {visibleSections.has('twelve-steps')
				? 'animate-fade-in-up'
				: 'opacity-0'}"
		>
			<div class="text-center mb-20">
				<p class="text-[#58a6ff] font-mono text-sm mb-4 tracking-wider">// THE_PROGRAM</p>
				<h2 class="text-4xl sm:text-5xl md:text-6xl font-display font-bold text-white mb-6">
					The Twelve Steps
				</h2>
				<p class="text-[#8b949e] text-lg max-w-2xl mx-auto">
					Our program of recovery, adapted for the modern developer. Take it one step at a time.
				</p>
			</div>

			<div class="space-y-16">
				{#each stepPhases as phase, phaseIndex (phase.name)}
					<div>
						<!-- Phase header -->
						<div class="flex items-center gap-4 mb-8">
							<div
								class="flex-shrink-0 w-14 h-14 rounded-2xl bg-gradient-to-br {phaseIndex === 0
									? 'from-[#58a6ff] to-[#1f6feb]'
									: phaseIndex === 1
										? 'from-[#f97316] to-[#ea580c]'
										: 'from-[#7ee787] to-[#3fb950]'} flex items-center justify-center shadow-lg"
							>
								<span class="text-2xl font-bold text-white">{phaseIndex + 1}</span>
							</div>
							<div>
								<h3 class="text-2xl font-display font-bold text-white">{phase.name}</h3>
								<p class="text-sm text-[#8b949e]">{phase.range} · {phase.description}</p>
							</div>
						</div>

						<!-- Steps in this phase -->
						<div class="ml-7 pl-10 border-l-2 border-[#30363d] space-y-6">
							{#each phase.steps as step (step.number)}
								<div class="relative group">
									<!-- Connector dot -->
									<div
										class="absolute -left-[calc(2.5rem+5px)] top-3 w-3 h-3 rounded-full bg-[#30363d] group-hover:bg-[#58a6ff] transition-colors duration-300"
									></div>

									<div
										class="flex gap-4 p-5 rounded-xl bg-[#0d1117]/50 border border-[#21262d] hover:border-[#30363d] hover:bg-[#0d1117] transition-all duration-300"
									>
										<div
											class="flex-shrink-0 w-10 h-10 rounded-full bg-[#21262d] flex items-center justify-center font-display font-bold text-[#8b949e] group-hover:text-[#58a6ff] transition-colors"
										>
											{step.number}
										</div>
										<p
											class="pt-2 text-[#c9d1d9] leading-relaxed group-hover:text-white transition-colors"
										>
											{step.text}
										</p>
									</div>
								</div>
							{/each}
						</div>
					</div>
				{/each}
			</div>

			<!-- Closing quote -->
			<div class="mt-20 text-center">
				<div class="inline-block px-8 py-6 rounded-2xl bg-[#0d1117] border border-[#30363d]">
					<p class="font-display text-xl sm:text-2xl italic text-[#8b949e]">
						"Keep coming back. It works if you <span class="text-[#58a6ff]">work</span> it."
					</p>
				</div>
			</div>
		</div>
	</section>

	<!-- Testimonials - Featured layout -->
	<section id="testimonials" class="py-32 px-6">
		<div
			class="max-w-5xl mx-auto {visibleSections.has('testimonials')
				? 'animate-fade-in-up'
				: 'opacity-0'}"
		>
			<div class="text-center mb-16">
				<p class="text-[#7ee787] font-mono text-sm mb-4 tracking-wider">// SUCCESS_STORIES</p>
				<h2 class="text-4xl sm:text-5xl md:text-6xl font-display font-bold text-white mb-6">
					Stories of Recovery
				</h2>
				<p class="text-[#8b949e] text-lg">
					Real testimonials from developers on their journey back to understanding
				</p>
			</div>

			<!-- Featured testimonial -->
			{#each testimonials.filter((t) => t.featured) as testimonial (testimonial.author)}
				<div class="mb-10 relative">
					<div
						class="absolute -top-8 left-8 text-8xl text-[#58a6ff]/10 font-serif leading-none select-none"
					>
						"
					</div>
					<div
						class="bg-gradient-to-br from-[#161b22] to-[#0d1117] rounded-3xl p-8 sm:p-12 border border-[#30363d] relative overflow-hidden"
					>
						<div
							class="absolute top-0 right-0 w-64 h-64 bg-[#58a6ff]/5 rounded-full blur-3xl"
						></div>
						<p class="text-xl sm:text-2xl text-[#e6edf3] leading-relaxed mb-8 relative z-10">
							{testimonial.quote}
						</p>
						<div
							class="flex items-center justify-between flex-wrap gap-4 pt-6 border-t border-[#21262d]"
						>
							<div>
								<p class="text-lg font-semibold text-white">{testimonial.author}</p>
								<p class="text-sm text-[#8b949e]">{testimonial.role}</p>
							</div>
							<div class="px-5 py-2 rounded-full bg-[#7ee787]/10 border border-[#7ee787]/30">
								<span class="font-mono font-semibold text-[#7ee787]">{testimonial.days}</span>
							</div>
						</div>
					</div>
				</div>
			{/each}

			<!-- Other testimonials -->
			<div class="grid gap-6 md:grid-cols-3">
				{#each testimonials.filter((t) => !t.featured) as testimonial (testimonial.author)}
					<div
						class="group relative bg-[#161b22] rounded-2xl p-6 border border-[#30363d] hover:border-[#58a6ff]/30 transition-all duration-300 hover:-translate-y-1"
					>
						<div
							class="absolute -top-3 left-6 text-5xl text-[#58a6ff]/10 font-serif leading-none select-none"
						>
							"
						</div>
						<p class="text-[#c9d1d9] leading-relaxed mb-6 relative z-10">
							{testimonial.quote}
						</p>
						<div class="flex items-center justify-between pt-4 border-t border-[#21262d]">
							<div>
								<p class="font-semibold text-white text-sm">{testimonial.author}</p>
								<p class="text-xs text-[#8b949e]">{testimonial.role}</p>
							</div>
							<div class="px-3 py-1 rounded-full bg-[#7ee787]/10 border border-[#7ee787]/20">
								<span class="text-xs font-mono text-[#7ee787]">{testimonial.days}</span>
							</div>
						</div>
					</div>
				{/each}
			</div>
		</div>
	</section>

	<!-- Serenity Prayer - Dramatic -->
	<section id="prayer" class="py-40 px-6 bg-[#161b22] relative overflow-hidden">
		<!-- Background effects -->
		<div
			class="absolute inset-0 bg-[radial-gradient(ellipse_at_center,_rgba(249,115,22,0.08)_0%,_transparent_50%)]"
		></div>
		<div
			class="absolute inset-0 opacity-[0.015]"
			style="background-image: url('data:image/svg+xml,%3Csvg viewBox=%220 0 200 200%22 xmlns=%22http://www.w3.org/2000/svg%22%3E%3Cfilter id=%22noise%22%3E%3CfeTurbulence type=%22fractalNoise%22 baseFrequency=%220.65%22 numOctaves=%223%22 stitchTiles=%22stitch%22/%3E%3C/filter%3E%3Crect width=%22100%25%22 height=%22100%25%22 filter=%22url(%23noise)%22/%3E%3C/svg%3E');"
		></div>

		<div
			class="max-w-3xl mx-auto text-center relative z-10 {visibleSections.has('prayer')
				? 'animate-fade-in-up'
				: 'opacity-0'}"
		>
			<!-- Icon -->
			<div class="mb-10">
				<div
					class="inline-flex items-center justify-center w-20 h-20 rounded-full bg-gradient-to-br from-[#f97316] to-[#ea580c] shadow-[0_0_60px_rgba(249,115,22,0.3)]"
				>
					<svg class="w-10 h-10 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="1.5"
							d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"
						/>
					</svg>
				</div>
			</div>

			<h3 class="text-3xl sm:text-4xl font-display font-bold text-[#f97316] mb-10">
				The Vibe Coder's Serenity Prayer
			</h3>

			<p
				class="text-2xl sm:text-3xl md:text-4xl font-display italic leading-relaxed text-[#c9d1d9]"
			>
				"Grant me the <span class="text-white font-semibold">serenity</span><br
					class="hidden sm:inline"
				/>
				to accept the code I cannot understand,
			</p>
			<p
				class="text-2xl sm:text-3xl md:text-4xl font-display italic leading-relaxed text-[#c9d1d9] mt-4"
			>
				the <span class="text-white font-semibold">courage</span><br class="hidden sm:inline" />
				to read the documentation,
			</p>
			<p
				class="text-2xl sm:text-3xl md:text-4xl font-display italic leading-relaxed text-[#c9d1d9] mt-4"
			>
				and the <span class="text-white font-semibold">wisdom</span><br class="hidden sm:inline" />
				to know when to ask Claude anyway."
			</p>
		</div>
	</section>

	<!-- Resources -->
	<section id="resources" class="py-32 px-6">
		<div
			class="max-w-4xl mx-auto {visibleSections.has('resources')
				? 'animate-fade-in-up'
				: 'opacity-0'}"
		>
			<div class="text-center mb-16">
				<h2 class="text-3xl sm:text-4xl font-display font-bold text-white mb-4">
					Recovery Resources
				</h2>
				<p class="text-[#8b949e]">Tools to help you on your journey</p>
			</div>

			<div class="grid gap-6 sm:grid-cols-3">
				<div
					class="group p-8 rounded-2xl bg-[#161b22] border border-[#30363d] hover:border-[#58a6ff]/50 transition-all duration-300 hover:-translate-y-1"
				>
					<div
						class="w-14 h-14 rounded-2xl bg-[#58a6ff]/10 flex items-center justify-center mb-6 group-hover:bg-[#58a6ff]/20 group-hover:scale-110 transition-all duration-300"
					>
						<svg
							class="w-7 h-7 text-[#58a6ff]"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"
							/>
						</svg>
					</div>
					<h3 class="font-bold text-white text-lg mb-3">Beginner's Guide</h3>
					<p class="text-[#8b949e] leading-relaxed">
						Learn what a "for loop" actually does. Warning: May cause existential crisis.
					</p>
				</div>

				<div
					class="group p-8 rounded-2xl bg-[#161b22] border border-[#30363d] hover:border-[#7ee787]/50 transition-all duration-300 hover:-translate-y-1"
				>
					<div
						class="w-14 h-14 rounded-2xl bg-[#7ee787]/10 flex items-center justify-center mb-6 group-hover:bg-[#7ee787]/20 group-hover:scale-110 transition-all duration-300"
					>
						<svg
							class="w-7 h-7 text-[#7ee787]"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"
							/>
						</svg>
					</div>
					<h3 class="font-bold text-white text-lg mb-3">Sponsor Program</h3>
					<p class="text-[#8b949e] leading-relaxed">
						Get paired with a developer who actually reads their own code.
					</p>
				</div>

				<div
					class="group p-8 rounded-2xl bg-[#161b22] border border-[#30363d] hover:border-[#f97316]/50 transition-all duration-300 hover:-translate-y-1"
				>
					<div
						class="w-14 h-14 rounded-2xl bg-[#f97316]/10 flex items-center justify-center mb-6 group-hover:bg-[#f97316]/20 group-hover:scale-110 transition-all duration-300"
					>
						<svg
							class="w-7 h-7 text-[#f97316]"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"
							/>
						</svg>
					</div>
					<h3 class="font-bold text-white text-lg mb-3">24/7 Hotline</h3>
					<p class="text-[#8b949e] leading-relaxed">
						Call before you paste that entire stack trace into Claude.
					</p>
				</div>
			</div>
		</div>
	</section>

	<!-- CTA -->
	<section id="cta" class="py-24 px-6">
		<div
			class="max-w-4xl mx-auto {visibleSections.has('cta') ? 'animate-fade-in-up' : 'opacity-0'}"
		>
			<div
				class="relative rounded-3xl bg-gradient-to-br from-[#f97316] via-[#ea580c] to-[#c2410c] p-12 sm:p-16 text-center overflow-hidden"
			>
				<!-- Pattern overlay -->
				<div
					class="absolute inset-0 opacity-10"
					style="background-image: radial-gradient(circle at 2px 2px, white 1px, transparent 0); background-size: 24px 24px;"
				></div>

				<div class="relative z-10">
					<h2
						class="text-3xl sm:text-4xl md:text-5xl font-display font-bold text-white mb-6 leading-tight"
					>
						The First Step is Admitting<br class="hidden sm:inline" /> You Don't Know What Your Code Does
					</h2>
					<p class="text-white/90 mb-10 text-lg sm:text-xl max-w-2xl mx-auto">
						Join thousands of developers who are learning to code again—this time, with
						understanding.
					</p>
					<button
						class="px-10 py-5 bg-[#0d1117] text-white font-bold text-lg rounded-xl hover:bg-[#161b22] transition-all duration-300 shadow-2xl hover:shadow-[0_20px_40px_rgba(0,0,0,0.4)] hover:scale-105"
					>
						Join a Meeting
					</button>
				</div>
			</div>
		</div>
	</section>

	<!-- Footer -->
	<footer class="py-16 px-6 border-t border-[#21262d]">
		<div class="max-w-4xl mx-auto text-center">
			<p class="font-display text-3xl font-bold text-white mb-3">Vibe Coders Anonymous</p>
			<p class="text-[#8b949e] font-mono mb-8">"One line at a time."</p>

			<!-- Easter egg relapse button -->
			<div class="mb-8">
				<a
					href="https://claude.ai"
					target="_blank"
					rel="noopener noreferrer"
					class="inline-flex items-center gap-2 px-4 py-2 rounded-lg bg-[#21262d] border border-[#30363d] text-[#8b949e] text-sm font-mono hover:border-[#f85149] hover:text-[#f85149] transition-all duration-300 group"
				>
					<span class="w-2 h-2 rounded-full bg-[#f85149] animate-pulse"></span>
					<span>Emergency Relapse Button</span>
					<span class="opacity-0 group-hover:opacity-100 transition-opacity">→</span>
				</a>
			</div>

			<p class="text-xs text-[#6e7681] max-w-md mx-auto">
				This is a satirical website. AI coding tools are genuinely useful—just maybe understand what
				they write sometimes.
			</p>
		</div>
	</footer>
</div>

<style>
	@keyframes float {
		0%,
		100% {
			transform: translateY(0) rotate(var(--rotation, 0deg));
		}
		50% {
			transform: translateY(-10px) rotate(var(--rotation, 0deg));
		}
	}

	@keyframes float-slow {
		0%,
		100% {
			transform: translateY(0) rotate(var(--rotation, 0deg));
		}
		50% {
			transform: translateY(-15px) rotate(var(--rotation, 0deg));
		}
	}

	@keyframes float-slower {
		0%,
		100% {
			transform: translateY(0) rotate(var(--rotation, 0deg));
		}
		50% {
			transform: translateY(-8px) rotate(var(--rotation, 0deg));
		}
	}

	@keyframes pulse-slow {
		0%,
		100% {
			opacity: 0.4;
		}
		50% {
			opacity: 0.7;
		}
	}

	@keyframes fade-in-up {
		from {
			opacity: 0;
			transform: translateY(30px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}

	:global(.animate-float) {
		animation: float 6s ease-in-out infinite;
	}

	:global(.animate-float-slow) {
		animation: float-slow 8s ease-in-out infinite;
	}

	:global(.animate-float-slower) {
		animation: float-slower 10s ease-in-out infinite;
	}

	:global(.animate-pulse-slow) {
		animation: pulse-slow 3s ease-in-out infinite;
	}

	:global(.animate-fade-in-up) {
		animation: fade-in-up 0.8s ease-out forwards;
	}
</style>
