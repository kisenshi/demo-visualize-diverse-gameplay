<svelte:head>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
</svelte:head>

<script>
	import { onMount } from 'svelte';
	import { fade, slide } from 'svelte/transition';

	import * as animateScroll from "svelte-scrollto";
	import { Spinner } from 'sveltestrap';
	import { Button } from 'sveltestrap';
	import { FormGroup, CustomInput } from 'sveltestrap';

	import { Tabs, TabList, TabPanel, Tab } from './TabLogic/tabs.js';

	import Heatmap from './Heatmap.svelte';
	import CellData from './CellData.svelte';
	import GameFeaturesInfo from './GameFeaturesInfo.svelte';
	import DemoInfo from './DemoInfo.svelte';

	let configInfo; 

	let jsonFileName = '';

	let dataInfo = {};

	let featureX = 'Non selected';
	let featureY = 'Non selected';

	let mapElites;

	let matrix;
	let xLabels;
	let yLabels;

	let agentData;

	let welcomeTab;
	let chooseDataTab;
	let heatmapTab;

	let loadingPlot;

	onMount(async () => {
		const res = await fetch("/json/demo_config.json");
		configInfo = await res.json();

		console.log(configInfo);
	});

	function loadMatrixData() {
		if (!jsonFileName) {
			console.log('not chosen')
			return;
		}

		let fetchFile = '/json/' + jsonFileName + '.json';
		console.log(fetchFile);

		fetch(fetchFile)
			.then((response) => response.json())
			.then((jsonData) => {
				dataInfo['experimentId'] = jsonData.config.experimentId;
				dataInfo['gameName'] = jsonData.config.frameworkConfig.game;
				dataInfo['level'] = jsonData.config.frameworkConfig.level;
				dataInfo['agentName'] = jsonData.config.frameworkConfig.agent;
				dataInfo['behaviours'] = jsonData.teamInfo.enabledBehaviours;
				dataInfo['featureX'] = jsonData.config.mapElitesConfig.featureX;
				dataInfo['featureY'] = jsonData.config.mapElitesConfig.featureY;
				dataInfo['gamePoster'] = './img/games/'+configInfo["games"][dataInfo['gameName']]["name"]+'.png';

				featureX = dataInfo['featureX'];
				featureY = dataInfo['featureY'];

				xLabels = jsonData.featuresDetails[featureX].bucketsRangeInfo;
				yLabels = jsonData.featuresDetails[featureY].bucketsRangeInfo;

				console.log(xLabels);
				console.log(yLabels);

				matrix = new Array(yLabels.length).fill(null).map(() => new Array(xLabels.length).fill(null));
				mapElites = jsonData.result.mapElites;
				var occupiedCells = jsonData.result.occupiedCellsIdx;
				
				var max = null;
				var min = null;
				occupiedCells.forEach(occupiedCellInfo => {
					var x = occupiedCellInfo['x']
					var y = occupiedCellInfo['y']
					var performanceCell = mapElites[x][y].performance * (-1);
					matrix[y][x] = performanceCell

					if ((max == null) || (max < performanceCell)){
						max = performanceCell;
					}
					if ((min == null) || (min > performanceCell)){
						min = performanceCell;
					}
				});
				dataInfo["performanceMax"] = max;
				dataInfo["performanceMin"] = min;
				
				loadingPlot = true;
				heatmapTab.triggerTab();
				agentData = null;
			});
  	}

	function setGameplayAvailability(path) {
        fetch(path).then(response => {
            if(response.status == 404) {
                agentData['gameplayAvailable'] = false;
            } else {
				agentData['gameplayAvailable'] = true;
			}
        });
    }

	const getCellInfo = (cellIdxFeatureX, cellIdxFeatureY) => {
		agentData = mapElites[cellIdxFeatureX][cellIdxFeatureY];
		agentData["cellX"] = cellIdxFeatureX;
		agentData["cellY"] = cellIdxFeatureY;

		agentData["videoUrl"] = "video/"+dataInfo["gameName"]+"_"+dataInfo["experimentId"]+"_"+cellIdxFeatureX+"_"+cellIdxFeatureY+".webm";
		
		setGameplayAvailability(agentData["videoUrl"]);

		animateScroll.scrollTo({element: '#agentData'});

		// DEBUG
		console.log("Feature X id: " + cellIdxFeatureX + " Feature Y id: " + cellIdxFeatureY);
		console.log(agentData);	
	}
</script>

<main id="my-style">
	<Tabs>
		<TabList>
			<Tab bind:this={welcomeTab}>Welcome!</Tab>
			<Tab bind:this={chooseDataTab}>Choose data</Tab>
			<Tab bind:this={heatmapTab}>
				{#if matrix}
					Team info
				{/if}
			</Tab>
		</TabList>
	
		<TabPanel>
			<div class="container info" in:fade out:slide>
				<DemoInfo/>
			</div>
		</TabPanel>
	
		<TabPanel>
			<div class="container" in:fade>
				<h1>Select game and features</h1>
			
				<FormGroup>
					<CustomInput type="select" bind:value={jsonFileName}>
						<option />
						{#each configInfo.files["BUTTERFLIES"]["B2"] as fileData}
							<option value="B2/{fileData.file}">butterflies (2 behaviours) - {fileData.featureX} x {fileData.featureY}</option>
						{/each}
						{#each configInfo.files["BUTTERFLIES"]["B3"] as fileData}
							<option value="B3/{fileData.file}">butterflies (3 behaviours) - {fileData.featureX} x {fileData.featureY}</option>
						{/each}
						{#each configInfo.files["ZELDA"]["Z5"] as fileData}
							<option value="Z5/{fileData.file}">zelda (5 behaviours) - {fileData.featureX} x {fileData.featureY}</option>
						{/each}
						{#each configInfo.files["DIGDUG"]["D5"] as fileData}
							<option value="D5/{fileData.file}">digdug (5 behaviours) - {fileData.featureX} x {fileData.featureY}</option>
						{/each}
					</CustomInput>
				</FormGroup>

				<Button color="secondary" on:click={loadMatrixData}>
					Load data
				</Button>
			</div>
		</TabPanel>
	
		{#if matrix}
			<TabPanel>
				<div in:fade out:slide>
					{#if loadingPlot}
						<div class="container">
							<Spinner color="info" type="border" />
						</div>
					{:else}
						<div class="container info">
							<GameFeaturesInfo {configInfo} {dataInfo}/>
						</div>
					{/if}
					<div class="container">
						<Heatmap {matrix} {featureX} {xLabels} {featureY} {yLabels} {getCellInfo} bind:loadingPlot/>
					</div>
					
					<div class="container" id="agentData">
						{#if agentData}
							<CellData {configInfo} {agentData} {dataInfo}/>
						{/if}
					</div>
				</div>
			</TabPanel>
		{/if}
	</Tabs>	
</main>

<style>
	:global(body) {
    	background-color: #65a7ff !important;
	}

	:global(main) {
		text-align: center;
    	color: #333;
    	background-color: #65a7ff;
	}

	:global(#my-style .container) {
		padding: 10px;
		max-width: 1000px;
		width: 100%;
		margin: 0 auto;
		background-color: #fff;
	}

	:global(#my-style .container.info) {
		text-align: left;
		padding: 25px;
	}

	:global(#my-style .container.info h1) {
		text-align: center;
	}

	:global(#my-style h1) {	
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
		color: #65a7ff;
	}

	:global(#my-style h2) {
		font-size: 1.5em;
		color: #65a7ff;
		font-weight: 400;
	}

	:global(#my-style h3) {
		font-size: 1em;
		color: #a8ceff;
		font-weight: 400;
	}

	:global(#my-style hr) {
		border-top: 1px solid #65a7ff;
	}
</style>