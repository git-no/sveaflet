<script lang="ts">
	import { onMount, onDestroy, getContext } from 'svelte';
	import { Map, control } from 'leaflet';
	import type { Control } from 'leaflet';
	import type { LeafletContextInterface } from './types';
	import { Compare } from './utils/index';

	// props
	type Props = {
		options?: Control.AttributionOptions;
		instance?: Control.Attribution;
	} & { [key: string]: unknown };

	let { options = {}, instance = $bindable(), ...restProps }: Props = $props();

	let latestProps = $derived.by(() => ({
		options,
		...restProps
	}));

	// context
	let parentContext = getContext<LeafletContextInterface>(Map);
	const { getMap } = parentContext;

	// data
	let attribution: Control.Attribution | undefined = $state();
	let compare: Compare | undefined;

	let map: Map | undefined = $derived(getMap?.());

	$effect(() => {
		instance = attribution;
	});

	onMount(() => {
		attribution = control.attribution(options);
		compare = new Compare(attribution, latestProps);
	});

	$effect(() => {
		if (map) {
			if (attribution) {
				compare?.updateProps(latestProps);
				map.addControl(attribution);
				compare?.storeProps(latestProps);
			}
		}
	});

	function reset() {
		attribution?.remove();
		attribution = undefined;
	}

	onDestroy(() => {
		reset();
	});
</script>
