<template>
<div>
    <H1>Hello from Estadisticas</H1>

    <table >
        <tr>
            <td><LineChart :chart-data="datacollection" class="chart-container"></LineChart></td>
            <td><PieChart :chart-data='datacollection' class="chart-container"></PieChart></td>
        </tr>
        <tr>
            <td><VerticalBar :chart-data='datacollection' class="chart-container"></VerticalBar></td>
            <td><HorizontalBar :chart-data='datacollection' class="chart-container"></HorizontalBar></td>
        </tr>
    </table>
    
</div>
</template>

<script>
// import axios from 'axios';
import LineChart from './lineChart.js';
import PieChart from './pieChart.js';
import HorizontalBar from './horizontalBar.js';
import VerticalBar from './verticalBar.js';

export default {
    name: 'Estadisticas',
    components: {LineChart, PieChart, HorizontalBar, VerticalBar},
    data(){
        return{
            datacollection: null
        }
    },
    mounted(){
        this.datacollection = {
            labels: ['one', 'two', 'three', 'four', 'five'],
            datasets: [{
                label: 'LineChart',
                data: [10,20,30,40,50]
            }]
        }
    }
}
</script>

<style>
.chart-container{
    height: 200px;
    width: 200px;
}
table{
    height: 100px;
    width: 100px;
    border: 1px solid black;
}
</style>