<script lang="ts">
	import { getContext } from 'svelte';
	import ModelItem from './ModelItem.svelte';
	import ChevronRight from '$lib/components/icons/ChevronRight.svelte';
	import type { Writable } from 'svelte/store';
	import type { i18n as i18nType } from 'i18next';

	const i18n: Writable<i18nType> = getContext('i18n');

	export let groupName: string;
	export let items: any[] = [];
	export let allFilteredItems: any[] = [];
	export let value: string = '';
	export let selectedModelIdx: number = -1;
	export let pinModelHandler: (modelId: string) => void = () => {};
	export let unloadModelHandler: (modelValue: string) => void = () => {};
	export let onModelSelect: (item: any, index: number) => void = () => {};
	export let forceExpanded: boolean = false;

	// Calculate the global index for each item in this group
	const getGlobalIndex = (item: any): number => {
		return allFilteredItems.findIndex((i) => i.value === item.value);
	};

	let manuallyExpanded = false;
	let groupButton;

	// Show expanded if either forced or manually expanded
	$: isExpanded = forceExpanded || manuallyExpanded;

	// Check if this group contains the currently selected model
	$: containsSelectedModel = items.some((item) => item.value === value);
</script>

<div class="relative" role="group" aria-label={groupName}>
	<button
		bind:this={groupButton}
		class="flex group/item w-full text-left font-medium select-none items-center rounded-button py-2 pl-3 pr-1.5 text-sm outline-hidden transition-all duration-75 rounded-xl cursor-pointer {containsSelectedModel
			? 'bg-gray-100 dark:bg-gray-800 text-gray-900 dark:text-gray-50'
			: 'text-gray-700 dark:text-gray-100 hover:bg-gray-100 dark:hover:bg-gray-800'}"
		on:click={() => {
			if (!forceExpanded) {
				manuallyExpanded = !manuallyExpanded;
			}
		}}
		type="button"
		aria-expanded={isExpanded}
	>
		<div class="flex items-center gap-2 flex-1">
			<div class="transition-transform duration-200" class:rotate-90={isExpanded}>
				<ChevronRight className="size-3" />
			</div>
			<div class="flex items-center gap-2">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					viewBox="0 0 32 32"
					fill="currentColor"
					class="size-4"
				>
					<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24"
						><!-- Icon from Material Symbols by Google - https://github.com/google/material-design-icons/blob/master/LICENSE --><path
							fill="currentColor"
							d="M12 14L1 8l11-6l11 6zm0 4L1.575 12.325l2.1-1.15L12 15.725l8.325-4.55l2.1 1.15zm0 4L1.575 16.325l2.1-1.15L12 19.725l8.325-4.55l2.1 1.15zm0-10.275L18.825 8L12 4.275L5.175 8zM12 8"
						/></svg
					>
				</svg>
				<span class="font-semibold">{groupName}</span>
			</div>
		</div>
		<div class="text-xs text-gray-500 dark:text-gray-400">
			{items.length}
			{items.length === 1 ? $i18n.t('Model') : $i18n.t('Models')}
		</div>
	</button>

	{#if isExpanded}
		<div class="ml-4 mt-1">
			{#each items as item, localIndex}
				<ModelItem
					{selectedModelIdx}
					{item}
					index={getGlobalIndex(item)}
					{value}
					{pinModelHandler}
					{unloadModelHandler}
					onClick={() => {
						onModelSelect(item, localIndex);
						manuallyExpanded = false;
					}}
				/>
			{/each}
		</div>
	{/if}
</div>
