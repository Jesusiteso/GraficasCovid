<template>
<div class="divCharts">
    <!-- <h2>Estadisticas</h2> -->

    <!-- <table width="100%">
        <tr><th colspan="3">Datos oficiales en Jalisco</th></tr>
        <tr>
            <td><h4>Confirmados:</h4> {{this.dataJalisco.Confirmados}}</td>
            <td><h4>Negativos:</h4>{{this.dataJalisco.Negativos}}</td>
            <td><h4>Sospechos:</h4>{{this.dataJalisco.Sospechosos}}</td>
        </tr>
        <tr>
            <td><h4>Defunciones:</h4>{{this.dataJalisco.Defunciones}}</td>
            <td><h4>Recuperados:</h4>{{this.dataJalisco.Recuperados}}</td>
            <td><h4>Activos:</h4>{{this.dataJalisco.Activos}}</td>
        </tr>
    </table> -->

    <!-- <hr> -->

    <!-- <div>
        <h2>Test realizados</h2>
        <h4>{{this.doneTestNumber}}</h4>
    </div> -->

    <table id="tableCharts">
        <!-- <h2 align="center">Datos de la web</h2> -->
        <tr width="100%">
            <!-- <div class="card">
            <td class="riskzones-chart" width="50%">
                
                <h2>Infectados por area</h2>
                <VerticalBar :chart-data='dataBarZones' height="300px" width="400px"></VerticalBar>
                
            </td>
            </div> -->
            <td style="display=inline-block;" width="50%" height="100%">
                <div class="card text-center">
                    <div class="card-body blue-card">
                        <h5 class="card-title">Test realizados</h5>
                        <p id="descartados" class="card-text">{{this.doneTestNumber}}</p>
                    </div>
                </div>
            </td>

            <td class="symptoms-chart" width="50%">
                <h2>Síntomas comunes de infectados</h2>
                <VerticalBar :chart-data='dataBarSymptoms' height="300px" width="400px"></VerticalBar>
            </td>

            
            
        </tr>
        <tr>
            <td class="history-chart" colspan="2">
                <h2>Historial de infectados por día</h2>
                <LineChart :chart-data="dataLineHistory" height="300px" width="800px"></LineChart>
            </td>
        </tr>
    </table>

    

    <!-- <hr> -->


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
import Plotly from 'plotly.js/lib/core';
// var Plotly = require('plotly.js/lib/core');
// import 'jquery';
// import 'popper.js';
// import 'bootstrap';
// import '../assets/style.css'

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

            doneTestNumber:     null,
            dataJalisco:        {
                Confirmados:    null,
                Negativos:      null,
                Sospechosos:    null,
                Defunciones:    null,
                Recuperados:    null,
                Activos:        null
            },
        }
    },

    

    mounted(){

        this.dataJalisco.Confirmados = 0;

        //Setting initial values of variables
        this.settingInitialValues();

        //Is filling data in charts
        this.fillSypmtoms();

        // this.test();
        
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
                    label: 'Sintomas de infectados',
                    data: this.symptomDataChart,
                    borderColor: 'aqua', 
                    backgroundColor: 'rgba(52, 132, 243, 0.64)', 
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
                        // console.log("un infectado");
                    }
                    dataHistory.push(historyCounter);
                    firstDate.setDate( firstDate.getDate() + 1 );

                }
                // console.log(labelHistory);
                // console.log(firstDate);
                // console.log(lastDate);
                // console.log(dataHistory);
                // console.log(arrayCounter);
                // console.log(tempDate);
                // console.log("ended");

                this.doneTestNumber = filterData.length;

                this.dataLineHistory = {
                    labels: labelHistory,
                    datasets: [{
                        label: 'Infectados por día',
                        data: dataHistory,
                        borderColor: 'rgba(255, 15, 15, 0.65)', 
                        backgroundColor: 'rgba(255, 15, 15, 0.65)'
                        // pointBackgroundColor: 'blue', 
                    }]
                };

            }).catch( e => {
                console.log(e);
            });

        },

        test(){
            // console.log('aaaaaaaaaaaaaaaaaaaaaaaa');
            // axios.get('https://papvidadigital-test.com/covidV2020/api/actual/gobierno').then( response => {

            //     let data = response.data;
            //     this.dataJalisco;
            //     console.log('data');
            //     console.log(response.data);

            //     for ( let i = 0 ; i < data.length ; i++ ){
            //         if(data[i]["Estado"].localeCompare('Jalisco') == 0){
            //             this.dataJalisco = data[i];
            //             break;
            //         }
            //     }
            //     console.log(this.dataJalisco);



            // }).catch( e => {
            //     console.log(e);
            // });

            Plotly.d3.json('https://docs.mapbox.com/mapbox-gl-js/assets/earthquakes.geojson', (err, raw) => {
                var lon = raw.features.map(f => f.geometry.coordinates[0]);
                var lat = raw.features.map(f => f.geometry.coordinates[1]);
                var z = raw.features.map(f => f.properties.mag);

                var data = [
                  { type: "scattermapbox", lon: lon, lat: lat, z: z, hoverinfo: "y" }
                ];

                var layout = {
                  mapbox: { style: "dark", zoom: 2, center: { lon: -150, lat: 60 } },
                  margin: { t: 0, b: 0 }
                };

                var config = {
                  mapboxAccessToken: "your access token"
                };

                Plotly.newPlot('mapPlotly', data, layout, config);
            });
        },

        settingInitialValues(){

                this.symptomListCode = [
                    'dolorSeveroPecho',
                    'difExtremaRespirar',
                    'desorientado',
                    // 'respEstimulos',
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
                    // 'respEstimulos**',
                    'Falta de Olfato',
                    'Falta de gusto',
                    'Fiebre',
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
h2,h3{
    color: blue;
}
table{
    width: 100%
}
.divCharts{
    /* padding-top:    10%; */
    padding-right:  10%;
    padding-bottom: 10%;
    padding-left:   10%;
}
/* @import "../assets/style.css"; */

</style>