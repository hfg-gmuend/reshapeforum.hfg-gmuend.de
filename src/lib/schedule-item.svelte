<script>
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();
	export let timeslot = 'tbd';
	export let title = '';
	export let subtitle = '';
	export let description = '';
	export let color = 'red';
	$: expandable = !!description;
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<p
	class:cursor-pointer={expandable}
	on:click={() => {
		if (!expandable) {
			return;
		}
		dispatch('click');
		return;
	}}
>
	<span class="text-{color}" class:opacity-50={title === 'Break'}>
		<span class="block reshape-lead-medium" class:expandable>{timeslot}</span>
		<span class="block">{title}</span>
		<span class="block">{subtitle}</span></span
	>
</p>

<style>
	.expandable::after {
		content: '';
		display: inline-block;
		width: 11px;
		height: 11px;
		background-image: url('/icon-expand.svg');
		background-repeat: no-repeat;
		margin-left: 3px;
		color: 'red';
	}

	.text-reshape-gruen > .expandable::after {
		background-image: url('/icon-link-green.svg');
	}

	.text-reshape-blau > .expandable::after {
		background-image: url('/icon-link-blue.svg');
	}

	.text-reshape-lila > .expandable::after {
		background-image: url('/icon-link-lila.svg');
	}
</style>
