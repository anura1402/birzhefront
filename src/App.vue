<template>
  <div id="app2">
    <h1 style="margin-bottom: 20px;">{{msg}}</h1>
      <h2 style="margin-bottom: 40px;" for="">Функция: {{getCorrectFunction()}}</h2>
      <!-- <div>
        <b-table striped hover :items="this.arguments" :fields="this.fields">
          <template slot="status" slot-scope="data"> -->
            <!-- <b-form-group> -->
                <!-- <input type="checkbox" @change="indexiesCalculation" v-model="item.status" /> -->
            <!-- </b-form-group> -->
            <!-- <b-form-checkbox></b-form-checkbox>
        
         </template>
        </b-table>
    </div> -->
      <div>
      <table style="margin-bottom: 40px;" class="table">
        <thead>
          <tr>
            <th>Название</th>
            <th>Аргумент</th>
            <th>Статус</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="argument in this.arguments">
            <td>{{argument.name}}</td>
            <td>{{argument.arg}}</td>
            <td>
              <input type="checkbox" v-model="argument.status"  @change="indexiesCalculation">
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <GChart 
    type="LineChart"
    :data="chartData"
    :options="chartOptions"
  />

    <router-view class="view"></router-view>
  </div>
</template>

<script>
  import { GChart } from 'vue-google-charts/legacy'

export default {
  data () {
    return {
      indexies: new Map,
      msg: 'Расчет значений биржевого индекса',
      functionString: '',
      fields: ['name', 'arg', 'status'],
      arguments: [
        {
          digit: 0.25, arg: 'x', name:'Лукойл',status: true, isNegative: false, capital: 10,
          history: new Map([
            [2000, 825.3],
            [2001, 722.3],
            [2002, 675.3],
            [2003, 565.3],
            [2004, 350.3],
            [2005, 5.3],
            [2006, 5.3],
            [2007, 5.3],
            [2008, 525.3],
            [2009, 635.3],
            [2010, 755.3],
      ]),
      },
        {
          digit: 0.25, arg: 'y', name:'Тинькофф',status: true, isNegative: false ,capital: 20, 
          history: new Map([
            [2000, 125.3],
            [2001, 122.3],
            [2002, 175.3],
            [2003, 165.3],
            [2004, 150.3],
            [2005, 135.3],
            [2006, 225.3],
            [2007, 225.3],
            [2008, 225.3],
            [2009, 235.3],
            [2010, 255.3],
      ]),
        },
        {
          digit: 0.25, arg: 'z', name:'АльфаБанк',status: true, isNegative: false ,capital: 30, 
          history: new Map([
            [2000, 125.3],
            [2001, 122.3],
            [2002, 175.3],
            [2003, 165.3],
            [2004, 150.3],
            [2005, 135.3],
            [2006, 225.3],
            [2007, 225.3],
            [2008, 225.3],
            [2009, 235.3],
            [2010, 255.3],
      ]),
        },
        {
          digit: 0.25, arg: 'w', name:'Сбер', status: true, isNegative: false,capital: 40, 
          history: new Map([
            [2000, 125.3],
            [2001, 122.3],
            [2002, 175.3],
            [2003, 165.3],
            [2004, 150.3],
            [2005, 135.3],
            [2006, 225.3],
            [2007, 225.3],
            [2008, 225.3],
            [2009, 235.3],
            [2010, 255.3],
      ]), 
        }
      ],
      chartData: [
        ['Год', 'Значение'],
      ],
      chartOptions: {
        title: 'Значения индекса',
        curveType: 'function',
        legend: { position: 'bottom' },
        width: "",
        height: 500,
      },
    }
  },
  mounted(){
    this.indexiesCalculation()
  },
  methods:{
    indexiesCalculation(){
      this.chartData = [
        ['Год', 'Значение'],
      ]
      var indexies = new Map() 
      var cr = 0

      // заполнение мапы годами
      this.arguments.forEach(function(item, index, array) {
        if (cr == 0){
          item.history.forEach(function(item, index) {
            indexies.set(index,0)
          })
        }
        cr++
      })
      this.indexies = indexies


      // проходим по годам в индексе и считаем значение и базисную сумму и записываем результат для вывода в графике
      for (let year of this.indexies.keys()) {
        var yearValSum = 0
        var yearBasisSum = 0
        this.arguments.forEach(function(item, i, array) {
          if (item.status){
            yearValSum += item.history.get(year) * item.digit
            yearBasisSum += item.history.get(year) + item.capital
          }
        })

        var val = yearValSum / yearBasisSum * 100

        this.chartData.push([year, val])
        this.indexies.set(year,val)
      }

      console.log('result',this.indexies)
    },
    // функция для составления функции
    getCorrectFunction(){
      var f = "F = "
      var cr = -1

      this.arguments.forEach(function(item, index, array) {
        // проверка что аргумент включен в функцию
        if (item.status){
          // определение знака перед аргументом в функции
          var znak = ''
          if (item.isNegative){
            znak = '-'
          } else{
            znak = '+'
            // проверка что аргумент первый
            if (cr == -1){
              znak = ''
            }
          }

          // отслеживание 1 перед аргументом при выводе в функцию
          var digit = ''
          if ( item.digit != 1){
            digit =  item.digit
          }

          f+= znak + digit + item.arg
          cr++ 
        }
      })
      return f
    }
  },
  components: {GChart}
}


</script>

<style type="text/css">
body {
  font-family: Helvetica, sans-serif;}
  .chart-container {
    float: left;
    margin-right: 20px; /* Добавьте отступ справа, если нужно */
  }
  .table{
    border: 1px solid #eee;
    table-layout: fixed;
    width: 100%;
    margin-bottom: 20px;
  }
  .table th {
    font-weight: bold;
    padding: 5px;
    background: #efefef; 
    text-align: center; 
    border: 1px solid #dddddd;
  }
  .table td{
    padding: 5px 10px;
    border: 1px solid #eee;
    text-align: center; 
    vertical-align: middle
  }
  .table tbody tr:nth-child(odd){
    background: #fff;
  }
  .table tbody tr:nth-child(even){
    background: #F7F7F7;
  }

</style>
