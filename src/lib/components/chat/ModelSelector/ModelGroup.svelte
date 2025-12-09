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
</script>

<div
	class="relative"
	role="group"
	aria-label={groupName}
>
	<button
		bind:this={groupButton}
		class="flex group/item w-full text-left font-medium select-none items-center rounded-button py-2 pl-3 pr-1.5 text-sm text-gray-700 dark:text-gray-100 outline-hidden transition-all duration-75 hover:bg-gray-100 dark:hover:bg-gray-800 rounded-xl cursor-pointer"
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
					viewBox="0 0 16 16"
					fill="currentColor"
					class="size-4"
				>
					<path
						fill-rule="evenodd"
						d="M2 4.75A.75.75 0 0 1 2.75 4h10.5a.75.75 0 0 1 0 1.5H2.75A.75.75 0 0 1 2 4.75ZM2 8a.75.75 0 0 1 .75-.75h10.5a.75.75 0 0 1 0 1.5H2.75A.75.75 0 0 1 2 8Zm0 3.25a.75.75 0 0 1 .75-.75h10.5a.75.75 0 0 1 0 1.5H2.75a.75.75 0 0 1-.75-.75Z"
						clip-rule="evenodd"
					/>
				</svg>
				<span class="font-semibold">{groupName}</span>
			</div>
		</div>
		<div class="text-xs text-gray-500 dark:text-gray-400">
			{items.length} {items.length === 1 ? $i18n.t('Model') : $i18n.t('Models')}
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

