<!-- @migration-task Error while migrating Svelte code: This migration would change the name of a slot making the component unusable -->
<script lang="ts">
	import { twMerge } from 'tailwind-merge';
	import { Tooltip } from 'flowbite-svelte';
	import { AngleDownOutline, AngleUpOutline } from 'flowbite-svelte-icons';
	export let divClass = 'w-full mx-auto bg-gradient-to-r bg-white dark:bg-gray-900 p-6';
	// the source of the example, if you want it
	export let src: any = undefined;

	// all meta tags of the code block
	export let meta: any = undefined;

	let browserSupport: boolean = false;
	let code: HTMLElement;

	// suppress vite-plugin-svelte warning about unused props
	$: src, meta;

	let showExpandButton: boolean = false;
	let expand: boolean = false;
	let dark: boolean = false;

	function init(node: HTMLElement) {
		browserSupport = !!window?.navigator?.clipboard;

		dark = node.ownerDocument.documentElement.classList.contains('dark');
	}

	let node: HTMLElement;

	const copyToClipboard = async (e: MouseEvent) => {
		const REG_HEX = /&#x([a-fA-F0-9]+);/g;
		const decodedText = code.innerText.replace(REG_HEX, function (_match, group1) {
			const num = parseInt(group1, 16);
			return String.fromCharCode(num);
		});

		await window.navigator.clipboard.writeText(decodedText);

		const button: HTMLButtonElement | null = e?.target as HTMLButtonElement;
		button?.blur();

		const lastChild = button?.lastChild;
		if (lastChild) {
			lastChild.textContent = 'Copied';
			setTimeout(() => (lastChild.textContent = 'Copy'), 3000);
		}
	};

	function checkOverflow(el: HTMLElement) {
		const isOverflowingY = el.clientHeight < el.scrollHeight;
		showExpandButton = isOverflowingY;
	}
</script>

<div class="mt-8 code-example" bind:this={node} use:init>
	{#if !meta.hideOutput}
		<div class="code-preview-wrapper">
			<div
				class="flex p-0 bg-white border-gray-200 bg-gradient-to-r code-preview dark:bg-gray-900 border-x border-t dark:border-gray-600"
				class:dark
			>
				<div class="w-full code-responsive-wrapper">
					<div class={twMerge(divClass, meta.class)}>
						<slot name="example" />
					</div>
				</div>
			</div>
		</div>
	{/if}
	<div class="code-syntax-wrapper">
		<div class="relative border-gray-200 border-y border-x code-syntax dark:border-gray-600">
			<div
				class="grid w-full grid-cols-2 border-b border-gray-200 bg-gray-50 rounded-t-md dark:bg-gray-700 dark:border-gray-600"
			>
				<ul class="flex text-sm font-medium text-center text-gray-500 dark:text-gray-400">
					<li>
						<span
							class="inline-block w-full p-2 px-3 text-gray-800 bg-gray-100 border-e border-gray-200 dark:text-white dark:bg-gray-800 dark:border-gray-600"
						>
							Svelte
						</span>
					</li>
				</ul>
				<div class="flex justify-end">
					{#if browserSupport}
						<button
							on:click={(e) => copyToClipboard(e)}
							type="button"
							class="flex items-center px-3 py-2 text-xs font-medium text-gray-600 bg-gray-100 border-s border-gray-200 dark:border-gray-600 dark:text-gray-400 dark:bg-gray-800 hover:text-primary-700 dark:hover:text-white copy-to-clipboard-button"
						>
							<svg
								class="w-4 h-4 me-2"
								fill="none"
								stroke="currentColor"
								viewBox="0 0 24 24"
								xmlns="http://www.w3.org/2000/svg"
							>
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									stroke-width="2"
									d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"
								/>
							</svg>
							Copy
						</button>
						<Tooltip placement="bottom-end">Copy to clipboard.</Tooltip>
					{/if}
				</div>
			</div>
			<div class="relative">
				<div class="overflow-hidden" class:max-h-72={!expand} tabindex="-1" use:checkOverflow>
					<div class="highlight">
						<pre bind:this={code} class="language-svelte !-mt-2 !rounded-none"><slot
								name="code"
							/></pre>
					</div>
				</div>
				{#if showExpandButton}
					<button
						on:click={() => (expand = !expand)}
						data-expand-code=""
						type="button"
						class="flex items-center justify-center absolute bottom-0 start-0 py-2.5 px-5 w-full text-sm font-medium text-gray-900 bg-gray-100 border-t border-gray-200 hover:bg-gray-100 hover:text-primary-700 dark:bg-gray-700 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700"
					>
						{#if !expand}
							Expand code <AngleDownOutline />
						{:else}
							Collapse code<AngleUpOutline />
						{/if}
					</button>
				{/if}
			</div>
		</div>
	</div>
</div>
