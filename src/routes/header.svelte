<script>
	import Toggle from './toggle.svelte'
</script>

<nav>
	<Toggle />
</nav>

<style>
	nav  {
		padding-block: var(--size-7); /* new CSS - padding left and right */
	}

	@media (min-width: 768px) {
		nav {
			display: flex;
			justify-content: space-between;
		}

	}
</style>
