<script>
    import { onMount } from 'svelte';

    export let getCellInfo;
    
    let plotlyScript = document.createElement('script');
    plotlyScript.src = "https://cdn.plot.ly/plotly-latest.min.js"
    document.head.append(plotlyScript);

    export let matrix;
    export let xLabels;
    export let yLabels;
    export let featureX;
    export let featureY;

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
        title: 'Each cell represents a member of the team of agents generated',
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

            plotDiv.on('plotly_click', function(heatmapData){
                // Return the index of the cell clicked, X and Y axis are set as [Y, X] in the plot
                var pn=heatmapData.points[0].pointNumber;
                
                var cellIdxFeatureX = pn[1];
                var cellIdxFeatureY = pn[0]
                getCellInfo(cellIdxFeatureX, cellIdxFeatureY);

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
            });
        }
    });
            
</script>

<div id="plotly">
    <div id="plotDiv"></div>
</div>

<style>
    #plotly {
        display: block;
        margin: auto;
    }
</style>