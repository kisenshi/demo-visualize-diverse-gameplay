<svelte:head>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
</svelte:head>

<script>
	import { onMount } from 'svelte';
	import { each } from 'svelte/internal';
	import { fade, slide, fly } from 'svelte/transition';

	import { Tabs, TabList, TabPanel, Tab } from './TabLogic/tabs.js';

	import * as animateScroll from "svelte-scrollto";
	import { Spinner } from 'sveltestrap';
	import { Col, Container, Row } from 'sveltestrap';

	import Heatmap from './Heatmap.svelte';
	import CellData from './CellData.svelte';

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
	let videoUrl;
	let gamePoster;

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
		/*
		return fetch('/json/test.json')
		.then(response => response.json());*/
		if (!jsonFileName) {
			console.log('not chosen')
			return;
		}

		let fetchFile = '/json/' + jsonFileName + '.json';
		console.log(fetchFile);

		fetch(fetchFile)
			.then((response) => response.json())
			.then((jsonData) => {
				dataInfo['gameName'] = jsonData.config.frameworkConfig.game;
				dataInfo['agentName'] = jsonData.config.frameworkConfig.agent;
				dataInfo['behaviours'] = jsonData.teamInfo.enabledBehaviours;

				featureX = jsonData.config.mapElitesConfig.featureX;
				featureY = jsonData.config.mapElitesConfig.featureY;

				xLabels = jsonData.featuresDetails[featureX].bucketsRangeInfo;
				yLabels = jsonData.featuresDetails[featureY].bucketsRangeInfo;

				console.log(xLabels);
				console.log(yLabels);

				matrix = new Array(yLabels.length).fill(null).map(() => new Array(xLabels.length).fill(null));
				mapElites = jsonData.result.mapElites;
				var occupiedCells = jsonData.result.occupiedCellsIdx;
				
				occupiedCells.forEach(occupiedCellInfo => {
					var x = occupiedCellInfo['x']
					var y = occupiedCellInfo['y']
					matrix[y][x] = mapElites[x][y].performance * (-1);
				});

				gamePoster = './img/games/'+jsonData.config.frameworkConfig.game.toLowerCase()+'.png';
				loadingPlot = true;
				heatmapTab.triggerTab();
				agentData = null;
			});
  	}

	const getCellInfo = (cellIdxFeatureX, cellIdxFeatureY) => {
		agentData = mapElites[cellIdxFeatureX][cellIdxFeatureY];
		agentData["cellX"] = cellIdxFeatureX;
		agentData["cellY"] = cellIdxFeatureY;

		console.log("Feature X id: " + cellIdxFeatureX + " Feature Y id: " + cellIdxFeatureY);
		console.log(agentData);
		
		videoUrl = './video/demo.mp4';	

		animateScroll.scrollTo({element: '#agentData'});	
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
				<h1>Demo</h1>
				<h2>Visualize Diverse Gameplays Based on Agent Behaviour</h2>
				<h3>Cristina Guerrero-Romero, Jeremy Gow (Demo supervisor)</h3>
				<hr/>
				<p>
					Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum
				</p>
				<p>
					Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit, sed quia non numquam eius modi tempora incidunt ut labore et dolore magnam aliquam quaerat voluptatem. Ut enim ad minima veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea commodi consequatur? Quis autem vel eum iure reprehenderit qui in ea voluptate velit esse quam nihil molestiae consequatur, vel illum qui dolorem eum fugiat quo voluptas nulla pariatur?
				</p>
				<p>
					At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident, similique sunt in culpa qui officia deserunt mollitia animi, id est laborum et dolorum fuga. Et harum quidem rerum facilis est et expedita distinctio. Nam libero tempore, cum soluta nobis est eligendi optio cumque nihil impedit quo minus id quod maxime placeat facere possimus, omnis voluptas assumenda est, omnis dolor repellendus. Temporibus autem quibusdam et aut officiis debitis aut rerum necessitatibus saepe eveniet ut et voluptates repudiandae sint et molestiae non recusandae. Itaque earum rerum hic tenetur a sapiente delectus, ut aut reiciendis voluptatibus maiores alias consequatur aut perferendis doloribus asperiores repellat.
				</p>
			</div>
		</TabPanel>
	
		<TabPanel>
			<div class="container" in:fade out:slide>
				<h1>Games</h1>
			
				<select bind:value={jsonFileName}>
					<option value="">----</option>
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
				</select>

				<button on:click={loadMatrixData}>
					Load data
				</button>
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
							<h1>{dataInfo['gameName']}</h1>
							<Container>
								<Row>
									<Col lg="7">
										<h2>Features:</h2>
										<h2>{featureX} x {featureY}</h2>
										<h3>Behaviours enabled for agents:</h3>
										<h3>
											{#each dataInfo['behaviours'] as behaviour }
												{behaviour}{" "}
											{/each}
										</h3>
									</Col>
									<Col lg="5">
										<img src={gamePoster} alt="Game screenshot"/>
									</Col>
								</Row>
							</Container>
							<div>
								adsfdsfdsf
								fdsfdsf
							</div>
						</div>
					{/if}
					<div class="container">
						<Heatmap {matrix} {featureX} {xLabels} {featureY} {yLabels} {getCellInfo} bind:loadingPlot/>
					</div>
					
					<div class="container" id="agentData">
						<CellData {agentData} {videoUrl} {gamePoster}/>
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

	img {
		width: 100%;
	}
</style>