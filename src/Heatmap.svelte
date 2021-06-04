<script>
    import { onMount } from 'svelte';
    import { Alert } from 'sveltestrap';
    import * as animateScroll from "svelte-scrollto";

    import CellData from './CellData.svelte';
    
    let plotlyScript = document.createElement('script');
    plotlyScript.src = "https://cdn.plot.ly/plotly-latest.min.js"
    document.head.append(plotlyScript);

    // heatmap data
    export let matrix;
    export let xLabels;
    export let yLabels;
    export let featureX;
    export let featureY;
    export let loadingPlot;

    // necessary to get agent info of each cell
    export let configInfo;
    export let dataInfo;
    export let mapElites;
    let agentData = null;

    // video data
    let loadingVideo = false;
    let videoAvailable = false;
    let videoSrc;

    // Heatmap plot config
    let GnYlRd = [
        [0, 'green'], 
        [0.5, 'yellow'],
        [1, 'red']
    ];

    let data = [{
        z: matrix,
        x: xLabels,
        y: yLabels,
        type: 'heatmap',
        hoverongaps: false,
        colorscale: GnYlRd,
    }];

    let xAxisTemplate = {
        showgrid: false,
        zeroline: false,
        title: featureX,
        automargin: true,
        autotick: false,
    };

    let yAxisTemplate = {
        showgrid: false,
        zeroline: false,
        title: featureY,
        automargin: true,
        autotick: false,
    };

    let layout = {
        title: '',
        annotations: [],
        xaxis: xAxisTemplate,
        yaxis: yAxisTemplate,     
    };

    let config = {
        responsive: true,
        displayModeBar: false
    }

    onMount(() => {
        plotlyScript.onload = function()  {
            let plotDiv = document.getElementById('plotDiv');				
            let Plot = new Plotly.newPlot(plotDiv, data, layout, config);
            
            plotDiv.on('plotly_afterplot', function(){
                loadingPlot = false;
            });

            plotDiv.on('plotly_click', function(heatmapData){
                // Return the index of the cell clicked, X and Y axis are set as [Y, X] in the plot
                var pn=heatmapData.points[0].pointNumber;
                
                var cellIdxFeatureX = pn[1];
                var cellIdxFeatureY = pn[0]

                // highlight cell
                if (plotDiv.data.length > 1) {
                    Plotly.deleteTraces(plotDiv, 1);
                } 
                
                let highlightMatrix = new Array(yLabels.length).fill(null).map(() => new Array(xLabels.length).fill(null));
                highlightMatrix[pn[0]][pn[1]] = 0;

                let highlightData = [{
                    z: highlightMatrix,
                    type: 'heatmap',
                    showscale: false,
                    hoverinfo: 'skip',
                    opacity: 0.65,
                }];
                
                Plotly.plot(plotDiv, highlightData);

                updateAgentData(cellIdxFeatureX, cellIdxFeatureY);
            });
        }
    });

    function updateAgentData(cellIdxFeatureX, cellIdxFeatureY) {
        agentData = mapElites[cellIdxFeatureX][cellIdxFeatureY];
		agentData["cellX"] = cellIdxFeatureX;
		agentData["cellY"] = cellIdxFeatureY;

		agentData["videoUrl"] = "video/"+dataInfo["gameName"]+"_"+dataInfo["experimentId"]+"_"+cellIdxFeatureX+"_"+cellIdxFeatureY+".webm";
		
		animateScroll.scrollTo({element: '#agentData'});

		// DEBUG
		console.log("Feature X id: " + cellIdxFeatureX + " Feature Y id: " + cellIdxFeatureY);
		console.log(agentData);	

        // Reload video
        updateVideo();
    }

    async function updateVideo() {
        loadingVideo = true;
        console.log("Loading "+agentData["videoUrl"]);
        const res = await fetch(agentData["videoUrl"]);
        console.log(res);
        if(res.status == 200) {
            videoSrc = agentData["videoUrl"];
            videoAvailable = true;
        } else {
            videoAvailable = false;
        }
        loadingVideo = false;
        console.log("Video loaded");
    }
            
</script>

<div class="container">
    {#if loadingPlot==false}
        <Alert color="primary">
            Each cell represents a member of the team of agents generated for the pair of selected features.<br/>
            Click on the cells to see detailed information about the gameplay and resulting stats of each agent.
        </Alert>
    {/if}

    <div id="plotly">
        <div id="plotDiv"></div>
    </div>
</div>

<div class="container" id="agentData">
    {#if agentData}
        <CellData {agentData} {configInfo} {dataInfo} {loadingVideo} {videoAvailable} {videoSrc}></CellData>
    {/if}
</div>


<style>
    #plotly {
        display: block;
        margin: auto;
    }
</style>