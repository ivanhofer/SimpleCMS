<script lang="ts">
	// typesafe-i18n
	import LL from '$i18n/i18n-svelte';

	// show/hide Left Sidebar
	import AnimatedHamburger from '$src/components/AnimatedHamburger.svelte';

	import { toggleLeftSidebar } from '$src/stores/store';
	export let switchSideBar = false;

	// Icons from https://icon-sets.iconify.design/
	import Icon from '@iconify/svelte';

	//skelton
	import { Avatar } from '@skeletonlabs/skeleton';

	// import { load } from './generate-thumbnails';
	// let thumbnails;
	// export async function preload(page) {
	// 	const result = await load({ fetch: null });
	// 	thumbnails = result.thumbnails;
	// }

	let view = 'grid';
	let size: 'small' | 'medium' | 'large' = 'small';
	$: {
		//TODO : removing console.log  will break refresh
		console.log('size changed:', size);
		// update the table when the size variable changes
		refreshData();
	}

	import { onMount } from 'svelte';

	let images: any = [];

	onMount(async () => {
		const res = await fetch('/gallery.json');
		images = await res.json();
	});

	// tanstack table

	import { writable } from 'svelte/store';
	import {
		createSvelteTable,
		flexRender,
		getCoreRowModel,
		getSortedRowModel
	} from '@tanstack/svelte-table';

	import type { ColumnDef, TableOptions, SortDirection, FilterFn } from '@tanstack/svelte-table';

	type Images = {
		image: string;
		name: string;
		path: string;
	};

	// columns definition
	const defaultColumns: ColumnDef<Images>[] = [
		{
			accessorKey: 'image',
			header: () => 'Image',
			footer: (info) => info.column.id,
			cell: (info) =>
				flexRender(Avatar, {
					src: info.row.original.image,
					width: `${size === 'small' ? 'w-6' : size === 'medium' ? 'w-10' : 'w-14'}`
				})
		},
		{
			accessorKey: 'name',
			header: () => 'Name',
			cell: (info) => info.getValue(),
			footer: (info) => info.column.id
		},
		{
			accessorKey: 'path',
			header: () => 'Path',
			cell: (info) => info.getValue(),
			footer: (info) => info.column.id
		}
	];

	const defaultData: Images[] = [
		{
			image: '/Default_User.svg',
			name: 'Default_User',
			path: '/static/Default_User'
		},
		{
			image: '/SimpleCMS_Logo_Round.png',
			name: 'SimpleCMS_Logo_Round',
			path: '/static/SimpleCMS_Logo_Round'
		},
		{
			image: '/SimpleCMS_Logo.svg',
			name: 'SimpleCMS_Logo',
			path: '/static/SimpleCMS_Logo'
		}
	];

	let sorting: any = [];

	const setSorting = (updater: (arg0: any) => any) => {
		if (updater instanceof Function) {
			sorting = updater(sorting);
		} else {
			sorting = updater;
		}
		options.update((old) => ({
			...old,
			state: {
				...old.state,
				sorting
			}
		}));
	};

	const options = writable<TableOptions<Images>>({
		data: defaultData,
		columns: defaultColumns,
		state: {
			sorting
		},
		onSortingChange: setSorting,
		getCoreRowModel: getCoreRowModel(),
		getSortedRowModel: getSortedRowModel()
	});

	const refreshData = () => {
		//console.info('refresh');
		options.update((prev) => ({
			...prev,
			data: defaultData
		}));
	};

	const rerender = () => {
		options.update((options) => ({
			...options,
			data: defaultData
		}));
	};

	const table = createSvelteTable(options);
</script>

<div class="flex mr-1 align-centre mb-2">
	{#if !switchSideBar && $toggleLeftSidebar}
		<AnimatedHamburger />
	{/if}

	<h1 class={!$toggleLeftSidebar ? 'ml-2' : ''}>Media Gallery</h1>
</div>

<div class="flex items-center justify-center mt-2 gap-10 border-b border-surface-500">
	<!-- Display Grid / Table -->
	<div class="mr-2 flex flex-col p-2 text-center text-xs">
		<div class="flex p-2 divide-x divide-gray-500">
			<div
				class="px-2"
				on:click={() => {
					view = 'grid';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter' || e.key === ' ') {
						view = 'grid';
					}
				}}
			>
				<Icon
					icon="material-symbols:grid-view-rounded"
					height="40"
					style={`color: ${view === 'grid' ? 'white' : 'grey'}`}
				/>Grid
			</div>
			<div
				class="px-2"
				on:click={() => {
					view = 'table';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter' || e.key === ' ') {
						view = 'table';
					}
				}}
			>
				<Icon
					icon="material-symbols:list-alt-outline"
					height="40"
					style={`color: ${view === 'table' ? 'white' : 'grey'}`}
				/>Table
			</div>
		</div>
	</div>

	<!-- switch between small, medium, and large images -->
	<div class="mr-2 flex flex-col p-2 text-center text-xs">
		<div class=" flex p-2 divide-x divide-gray-500">
			<div
				class="px-2"
				on:click={() => {
					size = 'small';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter' || e.key === ' ') {
						size = 'small';
					}
				}}
			>
				<Icon
					icon="material-symbols:background-grid-small-sharp"
					height="40"
					style={`color: ${size === 'small' ? 'white' : 'grey'}`}
				/>Small
			</div>
			<div
				class="px-2"
				on:click={() => {
					size = 'medium';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter' || e.key === ' ') {
						size = 'medium';
					}
				}}
			>
				<Icon
					icon="material-symbols:grid-on-sharp"
					height="40"
					style={`color: ${size === 'medium' ? 'white' : 'grey'}`}
				/>Medium
			</div>
			<div
				class="px-2"
				on:click={() => {
					size = 'large';
				}}
				on:keydown={(e) => {
					if (e.key === 'Enter' || e.key === ' ') {
						size = 'large';
					}
				}}
			>
				<Icon
					icon="material-symbols:grid-view"
					height="40"
					style={`color: ${size === 'large' ? 'white' : 'grey'}`}
				/>Large
			</div>
		</div>
	</div>
</div>

{#if view === 'grid'}
	<div
		class={`grid grid-cols-${
			size === 'small' ? '3' : size === 'medium' ? '2' : '4'
		} mt-2 px-1 gap-4`}
	>
		<!-- {#each images as image} -->
		<div class="card rounded-sm shadow-2xl">
			<section class="p-4">
				<img
					class={`w-full h-full object-cover ${
						size === 'small' ? 'h-32' : size === 'medium' ? 'h-48' : 'h-64'
					}`}
					src="/SimpleCMS_Logo_Round.png"
					alt="alt"
				/>
			</section>

			<footer class="card-footer bg-surface-500 font-bold rounded-sm text-center text-white">
				SimpleCMS
			</footer>
		</div>
		<!-- {/each} -->
	</div>
{:else}
	<div class="p-2">
		<div class="justify-center flex gap-3 mb-4">
			<div>{$table.getRowModel().rows.length} Rows</div>
			<button on:click={() => rerender()}>Force Rerender</button>
			<button on:click={() => refreshData()}>Refresh Data</button>
		</div>
		<div class="table-container">
			<table class="table table-hover">
				<thead class="bg-surface-500">
					{#each $table.getHeaderGroups() as headerGroup}
						<tr class="divide-x">
							{#each headerGroup.headers as header}
								<th colSpan={header.colSpan} class="text-center">
									{#if !header.isPlaceholder}
										<div
											class:cursor-pointer={header.column.getCanSort()}
											class:select-none={header.column.getCanSort()}
											on:click={header.column.getToggleSortingHandler()}
										>
											<svelte:component
												this={flexRender(header.column.columnDef.header, header.getContext())}
											/>
											{{
												asc: ' 🔼',
												desc: ' 🔽'
											}[header.column.getIsSorted().toString()] ?? ''}
										</div>
									{/if}
								</th>
							{/each}
						</tr>
					{/each}
				</thead>
				<tbody>
					{#each $table.getRowModel().rows.slice(0, 20) as row}
						<tr>
							{#each row.getVisibleCells() as cell}
								<td>
									<svelte:component
										this={flexRender(cell.column.columnDef.cell, cell.getContext())}
									/>
								</td>
							{/each}
						</tr>
					{/each}
				</tbody>
			</table>
		</div>
	</div>
{/if}
