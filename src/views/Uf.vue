<template>
  <div>
    <v-row>
      <v-col md="4">
        <v-card>
          <v-card class="text-center">
            <v-card-title>Valor Uf</v-card-title>
            <v-card-text>
              <v-date-picker
                  v-model="fecha"
                  first-day-of-week="1"
                  locale="es_CL"
                  min="1977"
                  :max="today"
                  :disabled="disabled"
                  @change="getUFDay()"
              ></v-date-picker>

            </v-card-text>
            <v-card-text>
              <div class="text-h5"> $ {{ valor }}</div>
            </v-card-text>
          </v-card>
        </v-card>
      </v-col>
      <v-col md="8">
        <v-card>
          <v-card-title>Valor UF 2021</v-card-title>
          <v-card-text>
            <div class="chartufyear" ref="chartufyear"></div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import * as am4core from "@amcharts/amcharts4/core";
import * as am4charts from "@amcharts/amcharts4/charts";
import am4themes_animated from "@amcharts/amcharts4/themes/animated";
import am4lang_es_ES from '@amcharts/amcharts4/lang/es_ES';

am4core.useTheme(am4themes_animated);

import axios from "axios"
import { mapMutations} from 'vuex'
export default {
  name: 'Uf',
  data(){
    return{
      fecha: new Date().toISOString().toLocaleString("es-CL", {timeZone: "America/Santiago"}).substr(0, 10),
      today: new Date().toISOString().toLocaleString("es-CL", {timeZone: "America/Santiago"}).substr(0, 10),
      valor: null,
      datosUF: null,
      disabled : false
    }
  },
  methods:{
    ...mapMutations(['mostraLoading', 'ocultarLoading']),
    async getUFDay(){
      let arrayFec = this.fecha.split("-").reverse().join("-")
      try {
        this.mostraLoading({titulo: 'Trayendo información', color: 'secondary'})
        this.disabled = true
        //console.log(arrayFec)
        await axios.get(`https://mindicador.cl/api/uf/${arrayFec}`)
            .then(
                response => {
                  this.disabled = false
                  this.valor = (response.data.serie.length > 0) ? response.data.serie[0].valor.toLocaleString() : '--'
                }
            )
      }
      catch(e)
      {
        //console.log(e)
      }
      finally{
        this.ocultarLoading()
      }
    },
    async getUFYear(){
      let soloAño=this.fecha.split("-")[0]
      let result = await axios.get(`https://mindicador.cl/api/uf/${soloAño}`)
      let datos = await result.data.serie;
      let chart = am4core.create(this.$refs.chartufyear, am4charts.XYChart);
      chart.dateFormatter.inputDateFormat = "yyyy-MM-dd";
      chart.language.locale = am4lang_es_ES;
      //chart.paddingRight = 20;
      let data = [];
      let visits = 10;
      for(let i=0; i<datos.length; i++){
        data.push({ date: datos[i].fecha.substr(0,10), value: datos[i].valor });
      }
      chart.data = data.reverse();
      chart.dateFormatter.inputDateFormat = "yyyy-MM-dd";
      chart.responsive.enabled = true;


      let dateAxis = chart.xAxes.push(new am4charts.DateAxis());
      /*      dateAxis.renderer.grid.template.location = 0;
            dateAxis.renderer.minGridDistance = 50;*/

      let valueAxis = chart.yAxes.push(new am4charts.ValueAxis());
      valueAxis.tooltip.disabled = true;
      valueAxis.title.text = "Valor $";

      let series = chart.series.push(new am4charts.LineSeries());
      series.dataFields.valueY = "value";
      series.dataFields.dateX = "date";
      series.tooltipText = "$ [bold]{valueY}[/]";
      series.fillOpacity = 0.3;

      chart.cursor = new am4charts.XYCursor();
      chart.cursor.lineY.opacity = 0;
      chart.scrollbarX = new am4charts.XYChartScrollbar();
      chart.scrollbarX.series.push(series);

      this.chart = chart;
    }
  },
  created() {
    this.getUFDay()
    this.getUFYear()
  }
}
</script>
<style scoped>
.chartufyear {
  width: 100%;
  height: 437px;
}
</style>

