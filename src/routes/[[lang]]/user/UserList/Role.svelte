<script lang="ts">
	import { PUBLIC_TRANSLATIONS } from '$env/static/public';

	export let value: string;

	// Icons from https://icon-sets.iconify.design/
	import Icon from '@iconify/svelte';

	// TODO| Add i18n PUBLIC_TRANSLATIONS
	// Grab Users from one Location only

	let classes = `${
		value === 'Admin'
			? 'badge gradient-primary '
			: value === 'Developer'
			? 'badge gradient-pink'
			: value === 'Editor'
			? 'badge gradient-tertiary'
			: value === 'User'
			? 'badge gradient-secondary'
			: 'text-white'
	} rounded-full text-white`;
</script>

<div class={classes}>
	{#if value == 'Admin'}
		<Icon icon="material-symbols:verified-outline" width="20" class="mr-2" /> {value}
	{:else if value == 'Developer'}
		<Icon icon="material-symbols:supervised-user-circle" width="20" class="mr-2" /> {value}
	{:else if value == 'Editor'}
		<Icon icon="mdi:user-edit" width="20" class="mr-2" /> {value}
	{:else if value == 'User'}
		<Icon icon="material-symbols:supervised-user-circle" width="20" class="mr-2" /> {value}
	{/if}
</div>
