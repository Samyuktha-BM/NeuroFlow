<script>
    import {onMount} from "svelte";
    import * as echarts from "echarts";

    let chartContainer;
    let chartInstance;
    export let data ;
    export let lineColor;
    export let lineName;
    onMount(() => {
        chartInstance = echarts.init(chartContainer);
    });
    function updateChart() {
        chartInstance.setOption({
            xAxis: {
                type: 'category',
                data: data.x
            },
            yAxis: {
                type: 'value',
                axisLabel: {
                    formatter: '{value}'
                }
            },
            series: [{
                data: data.main,
                type: 'line',
                name: lineName,
                itemStyle: { color: lineColor },
            }],
            tooltip: {
                trigger: 'axis'
            },
            legend: {
                data: [lineName]
            }
        }
    )}
    $: if (chartInstance) {
        updateChart(data);
    }
</script>
<div bind:this={chartContainer} class="chart-container"></div>
<style>
    .chart-container {
        width: 600px;
        height: 400px;
    }
</style>
