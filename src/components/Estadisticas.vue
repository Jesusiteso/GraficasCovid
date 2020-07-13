<template>
<div>
    <H1>Hello from Estadisticas</H1>

    <table>
        <tr>
            <td class="riskzones-chart">
                <VerticalBar :chart-data='dataBarZones' height="300px" width="400px"></VerticalBar>
            </td>
            <td class="symptoms-chart">
                <VerticalBar :chart-data='dataBarSymptoms' height="300px" width="400px"></VerticalBar>
            </td>
        </tr>
        <tr>
            <td class="history-chart" colspan="2">
                <LineChart :chart-data="dataLineHistory" height="300px" width="800px"></LineChart>
            </td>
        </tr>
    </table>

    <hr>

    <!-- <table >
        <tr>
            <td><LineChart :chart-data="dataLineContagios" class="chart-container"></LineChart></td>
            <td><PieChart :chart-data='dataPieDistribucion' class="chart-container"></PieChart></td>
        </tr>
        <tr>
            <td><VerticalBar :chart-data='dataBarZonas' class="chart-container"></VerticalBar></td>
            <td><HorizontalBar :chart-data='dataHorizontalSintomas' class="chart-container"></HorizontalBar></td>
        </tr>
    </table> -->
    
</div>
</template>

<script>
import axios from 'axios';
import LineChart from './lineChart.js';
// import PieChart from './pieChart.js';
// import HorizontalBar from './horizontalBar.js';
import VerticalBar from './verticalBar.js';

export default {
    name: 'Estadisticas',
    components: {LineChart, VerticalBar},
    data(){
        return{
            dataBarZones:       null,
            dataBarSymptoms:    null,
            dataLineHistory:    null,

            symptomListCode:    null,
            symptomListName:    null,
            symptomDataChart:   null,
        }
    },

    

    mounted(){

        this.settingInitialValues();

        this.fillSypmtoms();
        
        // this.settingStyleCharts();


    },


    methods: {

        settingStyleCharts(){
            this.dataBarZones = {
                labels: ['one', 'two', 'three', 'four', 'five'],
                datasets: [{
                    label: 'DefaultZonas',
                    data: [10,20,30,40,50],
                    borderColor: 'aqua', 
                    pointBackgroundColor: 'blue', 
                }]
            };

            this.dataBarSymptoms = {
                labels: this.symptomListName,
                datasets: [{
                    label: 'DefaultSintomas',
                    data: [10,20,30,40,50],
                    borderColor: 'aqua', 
                    pointBackgroundColor: 'blue', 
                }]
            };

            this.dataLineHistory = {
                labels: ['one', 'two', 'three', 'four', 'five'],
                datasets: [{
                    label: 'DefaultHistorial',
                    data: [10,20,30,40,50],
                    borderColor: 'aqua', 
                    pointBackgroundColor: 'blue', 
                }]
            };

            

        },

        fillSypmtoms(){

            axios.get('https://papvidadigital-test.com/covidV2020/api/actual').then( response => {

                let data = response.data;

                //Filtra la información de encuestas repetidas para tener solo las ultimas encuestas
                data = data.reverse();
                let filterData = [];

                let dataIndexUser = [];
                data.forEach( element => {
                    if(!dataIndexUser.includes(element["idUsuario"])){
                        dataIndexUser.push(element["idUsuario"]);
                        filterData.push(element);
                    }
                });

                console.log(filterData);


                
                filterData.forEach( element => {
                    if(element["escrutinio"] >= 3){
                        for ( let i = 0 ; i<this.symptomListCode.length ; i++){
                            if(element[ this.symptomListCode[i] ] != 0){
                                this.symptomDataChart[i]++;
                            }
                        }
                    }
                });

                filterData.sort((a,b) => {
                    let A = new Date(a["fechaCreacion"]);
                    let B = new Date(b["fechaCreacion"]);

                    if( A.getTime() < B.getTime() ){
                        return -1;
                    }
                    if( A.getTime() > B.getTime() ){
                        return 1;
                    }else{
                        return 0;
                    }

                
                });

                console.log(this.symptomDataChart);
                console.log("------------");
                console.log(filterData);

                this.dataBarSymptoms = {
                labels: this.symptomListName,
                datasets: [{
                    label: 'DefaultSintomas',
                    data: this.symptomDataChart,
                    borderColor: 'aqua', 
                    pointBackgroundColor: 'blue', 
                }]
                };

                //Ahora llena la tabla de historial
                let labelHistory = [];
                let dataHistory = [];

                let firstDate = new Date(filterData[0]["fechaCreacion"]);
                let lastDate = new Date(filterData[filterData.length -1]["fechaCreacion"]); 
                let arrayCounter = 0;
                let historyCounter = 0;
                let tempDate;

                while( firstDate.getTime() <= lastDate.getTime() ){
                    labelHistory.push( firstDate.toLocaleDateString() );

                    historyCounter = 0;

                    if( arrayCounter < filterData.length){
                        tempDate = new Date(filterData[arrayCounter]["fechaCreacion"]);
                    }
                    
                    // tempDate.setDate(filterData[arrayCounter]["fechaCreacion"]);
                    

                    while( arrayCounter < filterData.length && firstDate.getTime() == tempDate.getTime() ){

                        arrayCounter++;
                        if( arrayCounter < filterData.length){
                            tempDate = new Date(filterData[arrayCounter]["fechaCreacion"]);
                        }

                        // tempDate = new Date(filterData[arrayCounter]["fechaCreacion"]);
                        
                        historyCounter++;
                        console.log("un infectado");
                    }
                    dataHistory.push(historyCounter);
                    firstDate.setDate( firstDate.getDate() + 1 );

                }
                console.log(labelHistory);
                console.log(firstDate);
                console.log(lastDate);
                console.log(dataHistory);
                console.log(arrayCounter);
                console.log(tempDate);
                console.log("ended");

                this.dataLineHistory = {
                    labels: labelHistory,
                    datasets: [{
                        label: 'DefaultSintomas',
                        data: dataHistory,
                        borderColor: 'aqua', 
                        // pointBackgroundColor: 'blue', 
                    }]
                };

            }).catch( e => {
                console.log(e);
            });

        },

        settingInitialValues(){

                this.symptomListCode = [
                    'dolorSeveroPecho',
                    'difExtremaRespirar',
                    'desorientado',
                    'respEstimulos',
                    'olfato',
                    'gusto',
                    'fiebre',
                    'escalofrios',
                    'respiracion',
                    'diarrea',
                    'vomito',
                    'tos',
                    'dolorMuscular',
                    'dolorCabeza',
                    'irritacionOjos',
                    'sangradoRespiratorio'
                ];
                this.symptomListName = [
                    'Dolor de pecho',
                    'Dificultad respiratoria',
                    'Desorientado',
                    'respEstimulos**',
                    'olfato**',
                    'gusto**',
                    'fiebre**',
                    'Escalofrios',
                    'Respiracion',
                    'Diarrea',
                    'Vomito',
                    'Tos',
                    'Dolor muscular',
                    'Dolor de cabeza',
                    'Irritacion en ojos',
                    'Dangrado Respiratorio'
                ];

                this.symptomDataChart = [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0];

            }


    }
}
</script>

<style>
.chart-container{
    height: 200px;
    width: 200px;
}
.big-chart-container{
    display: block;
    height: 200px;
    width: 400px;
}
</style>