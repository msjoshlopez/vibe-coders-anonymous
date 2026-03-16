<script lang="ts">
	import { page } from '$app/state';

	interface NavLink {
		href: string;
		label: string;
		icon: string;
	}

	const links: NavLink[] = [
		{ href: '/', label: 'Recovery Home', icon: '🏠' },
		{ href: '/quiz', label: 'Diagnose Yourself', icon: '🩺' },
		{ href: '/game', label: '60 Seconds Sober', icon: '⏱️' },
		{ href: '/confessions', label: 'Confession Booth', icon: '🕯️' },
	];

	let menuOpen = $state(false);

	function toggleMenu() {
		menuOpen = !menuOpen;
	}

	function closeMenu() {
		menuOpen = false;
	}
</script>

<nav class="fixed top-0 left-0 right-0 z-50 border-b border-[#30363d] bg-[#0d1117]/90 backdrop-blur-md">
	<div class="mx-auto max-w-6xl px-4">
		<div class="flex h-14 items-center justify-between">
			<!-- Logo -->
			<a href="/" class="flex items-center gap-2 group" onclick={closeMenu}>
				<span class="font-mono text-xs text-[#8b949e] group-hover:text-[#58a6ff] transition-colors">~/</span>
				<span class="font-['Bitter'] font-bold text-[#c9d1d9] text-sm tracking-tight group-hover:text-white transition-colors">
					Vibe Coders Anonymous
				</span>
			</a>

			<!-- Desktop links -->
			<div class="hidden md:flex items-center gap-1">
				{#each links as link (link.href)}
					{@const isActive = page.url.pathname === link.href}
					<a
						href={link.href}
						class="flex items-center gap-1.5 rounded-md px-3 py-1.5 text-sm transition-all duration-150
							{isActive
								? 'bg-[#161b22] text-[#f97316] border border-[#30363d]'
								: 'text-[#8b949e] hover:text-[#c9d1d9] hover:bg-[#161b22]'}"
					>
						<span class="text-xs">{link.icon}</span>
						{link.label}
					</a>
				{/each}
			</div>

			<!-- Mobile hamburger -->
			<button
				class="md:hidden flex flex-col gap-1 p-2 text-[#8b949e] hover:text-[#c9d1d9] transition-colors"
				onclick={toggleMenu}
				aria-label="Toggle menu"
			>
				<span class="block w-5 h-px bg-current transition-all duration-200 {menuOpen ? 'translate-y-[5px] rotate-45' : ''}"></span>
				<span class="block w-5 h-px bg-current transition-all duration-200 {menuOpen ? 'opacity-0' : ''}"></span>
				<span class="block w-5 h-px bg-current transition-all duration-200 {menuOpen ? '-translate-y-[5px] -rotate-45' : ''}"></span>
			</button>
		</div>
	</div>

	<!-- Mobile menu -->
	{#if menuOpen}
		<div class="md:hidden border-t border-[#30363d] bg-[#0d1117]">
			{#each links as link (link.href)}
				{@const isActive = page.url.pathname === link.href}
				<a
					href={link.href}
					onclick={closeMenu}
					class="flex items-center gap-3 px-4 py-3 text-sm border-b border-[#30363d]/50 transition-colors
						{isActive
							? 'text-[#f97316] bg-[#161b22]'
							: 'text-[#8b949e] hover:text-[#c9d1d9] hover:bg-[#161b22]'}"
				>
					<span>{link.icon}</span>
					{link.label}
				</a>
			{/each}
		</div>
	{/if}
</nav>

<!-- Spacer so page content doesn't hide under the nav -->
<div class="h-14"></div>
