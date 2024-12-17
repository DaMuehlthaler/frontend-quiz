<script>
	import { onMount } from 'svelte';
	import '../app.css';
	import 'iconify-icon';

	// Default theme is dark
	let theme = 'dark';

	// Initialize theme on component mount
	onMount(() => {
		const savedTheme = localStorage.getItem('theme');
		if (savedTheme) {
			theme = savedTheme;
		}

		// Sync toggle with the theme
		const toggle = document.querySelector('.toggle');
		if (toggle) {
			toggle.checked = theme === 'dark';
		}
	});

	// Switch theme and save it in localStorage
	function switchTheme() {
		theme = theme === 'dark' ? 'light' : 'dark';
		localStorage.setItem('theme', theme);
	}
</script>

<div data-theme={theme} class="min-h-screen p-1">
	<div class="flex justify-end">
		<div class="gap-1 flex items-center">
			<iconify-icon icon="lucide-sun" class="text-2xl"></iconify-icon>
			<input
				type="checkbox"
				class="toggle border-none bg-white [--tglbg:#a729f5] hover:bg-white"
				checked={theme === 'dark'}
				on:click={switchTheme}
			/>
			<iconify-icon icon="lucide-moon" class="text-2xl"></iconify-icon>
		</div>
	</div>
	<slot />
</div>
