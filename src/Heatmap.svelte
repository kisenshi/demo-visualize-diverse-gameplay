<script>
    import { onMount } from 'svelte';

    export let updateData;
    
    let plotlyScript = document.createElement('script');
    plotlyScript.src = "https://cdn.plot.ly/plotly-latest.min.js"
    document.head.append(plotlyScript);

    export let matrix;
    export let xLabels;
    export let yLabels;

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
        title: 'Axis X',
    };

    let yAxisTemplate = {
        showgrid: false,
        zeroline: false,
        title: 'Axis Y',
    };

    let layout = {
        title: 'Each cell represents a member of the team of agents generated',
        annotations: [],
        xaxis: xAxisTemplate,
        yaxis: yAxisTemplate,
    };

    let config = {
        responsive: true,
    }

    onMount(() => {
        plotlyScript.onload = function()  {
            let plotDiv = document.getElementById('plotDiv');				
            let Plot = new Plotly.newPlot(plotDiv, data, layout, config); 

            plotDiv.on('plotly_click', function(data){
                var pts = '';
                for(var i=0; i < data.points.length; i++){
                    pts = 'x = '+data.points[i].x +'\ny = '+ data.points[i].y + '\n\n';
                }
                updateData(pts);
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