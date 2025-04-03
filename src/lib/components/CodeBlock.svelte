<script lang="ts">
	import { onMount } from "svelte";
	import CopyToClipBoardBtn from "./CopyToClipBoardBtn.svelte";
	import DOMPurify from "isomorphic-dompurify";

	interface Props {
		code?: string;
		rawCode?: string;
		lang?: string;
	}

	let { code = "", rawCode = "", lang = "" }: Props = $props();

	let isExpanded = $state(true);
	let isExpandable = $state(false);
	let lineCount = $state(0);
	const lineLimit = 3;

	onMount(() => {
		lineCount = code.split("\n").length;
		isExpandable = lineCount > lineLimit;
	});

	function toggleExpand() {
		isExpanded = !isExpanded;
	}
</script>

<div class="group relative my-4 rounded-lg">
	<div
		class="absolute left-0 top-0 flex h-10 w-full items-center rounded-t-md bg-gray-600/60 px-4 dark:bg-gray-700/40"
	>
		<div class="text-white dark:text-gray-300">{lang}</div>
	</div>
	<pre
		class="scrollbar-custom overflow-auto px-5 pt-14 font-mono scrollbar-thumb-gray-500 hover:scrollbar-thumb-gray-400 dark:border dark:border-gray-700 dark:scrollbar-thumb-white/10 dark:hover:scrollbar-thumb-white/20"
		class:max-h-40={!isExpanded && isExpandable}
		class:overflow-hidden={!isExpanded && isExpandable}><code
			><!-- eslint-disable svelte/no-at-html-tags -->{@html DOMPurify.sanitize(code)}</code
		></pre>
	{#if !isExpanded && isExpandable}
		<div
			class="absolute bottom-0 left-0 h-8 w-full rounded-b-md bg-black/80 dark:bg-gray-700/80"
		></div>
		<div class="absolute bottom-1.5 left-2 text-sm italic text-gray-300 dark:text-gray-400">
			<!-- 1 offset -->
			+{lineCount - lineLimit - 1} lines
		</div>
	{/if}
	<div
		class="invisible absolute right-2 top-[6px] flex items-center space-x-2 opacity-0 group-hover:visible group-hover:opacity-100"
	>
		{#if isExpandable}
			<button
				class="rounded border border-gray-200 px-2 py-1 text-xs text-gray-200 focus:outline-none focus:ring-1 focus:ring-gray-300 dark:border-gray-700 dark:bg-gray-800 dark:text-gray-500 dark:hover:border-gray-500 dark:hover:bg-gray-700"
				onclick={toggleExpand}
				aria-expanded={isExpanded}
			>
				{isExpanded ? "Collapse" : "Expand"}
			</button>
		{/if}

		<CopyToClipBoardBtn
			classNames="btn rounded-lg border border-gray-200 p-1.5 text-xs shadow-sm transition-all hover:border-gray-300 active:shadow-inner dark:border-gray-700 dark:hover:border-gray-500 invisible opacity-0 group-hover:visible group-hover:opacity-100 dark:text-gray-700 text-gray-200"
			value={rawCode}
		/>
	</div>
</div>
