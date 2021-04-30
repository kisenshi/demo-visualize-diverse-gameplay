<script>
	import { getContext } from 'svelte';
	import { TABS } from './Tabs.svelte';

	const tab = {};
	const { registerTab, selectTab, selectedTab } = getContext(TABS);

	registerTab(tab);
	
	export const triggerTab = () => selectTab(tab);
    
</script>

<style>
	button {
		text-transform: uppercase;
		background: none;
		border: none;
		border-radius: 0;
		margin: -10px 0px 0px 0px;
		color: #dbeafd;
		border-bottom: 2px solid #65a7ff; 
	}

	button:hover:not(.selected) {
  		background-color: #1178ff;
	}

	.selected {
		border-bottom: 2px solid #dbeafd;
		background-color: #fff;
		color: #65a7ff;
		text-transform: uppercase;
		border-radius: 0;
	}
</style>

<button class:selected="{$selectedTab === tab}" on:click="{() => selectTab(tab)}">
	<slot></slot>
</button>