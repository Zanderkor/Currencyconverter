<template>
  <h1>Валютный менеджер</h1>
  <p>Обменять валюту</p>
  <marquee>{{ runLine }}</marquee>
  <p v-bind:value="curFullNameInfoFunc()">{{ currencyInfo }}</p>
  <input type="number" placeholder="На руках" v-model="curInHand">
  <input placeholder="После обмена" readonly v-bind:value="exchangeValue">
  <p v-bind:value="worldCurrenciesRate()">Доллар США $ — {{ rateUsd }} руб.</p>
  <p v-bind:value="worldCurrenciesRate()">Евро € — {{ rateEur }} руб.</p>
  <button @click="valueAfterChange()">Обменять</button>
  <button>Просто кнопка</button>
  <form>
    <label for="currency">Выбирете валюту:</label>
    <select v-model="currencyShortNamePick">
      <option v-for="curName in fullCurrencyArray" v-bind:value="curName" > {{ curName }} </option>
    </select>
  </form>
  <p v-bind:value="justFunction()">{{ currencyIndex }}</p>  
  <div class="container">
    <Bar id="my-chart-id" :options="chartOptions" :data="chartData" />
  </div>

</template>



<script>
import axios, { toFormData } from 'axios'
import { Bar } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)
export default {
  name: 'BarChart',
  components: { Bar },
  data() {
    return {
      runLine: '',
      exchangeValue: null,
      curInHand: null,
      multiplyCof: null,
      jaySon: ' ',
      curArr: [],
      fullCurrencyArray: [],
      currencyList: '',
      currencyInfo:'',
      currencyShortNamePick: '',
      currencyShortName: '',
      currencyFullName: '',
      currencyIndex: null,
      rateUsd: null,
      rateEur: null,
      rateGbp: null,
      rateCny: null,
      datacollection: null,
      chartData: {
        labels: ['January', 'February', 'March'],
        datasets: [{ data: [40, 20, 12] }]
      },
      chartOptions: {
        responsive: true
      }
    }
  },
  async beforeCreate() {    
    axios.get('https://www.cbr-xml-daily.ru/daily_json.js')
      .then(res => (this.curArr = Object.keys(res.data.Valute)))
    await
      axios.get('https://www.cbr-xml-daily.ru/daily_json.js')
        .then(pip => (this.currencyList = pip.data.Valute))
        this.curArr.forEach((element) => {
        this.fullCurrencyArray.push(`${element} - ${this.currencyList[element]["Name"]}`)        
      })
  },
 methods: {
    justFunction(){
    this.currencyIndex = this.fullCurrencyArray.indexOf(this.currencyShortNamePick)
    this.currencyShortName = this.curArr[this.currencyIndex]
    },   
    valueAfterChange() {
      this.exchangeValue = this.curInHand;
      this.multiplyCof = this.currencyList[this.currencyShortName]["Value"];
      this.exchangeValue = this.exchangeValue * this.multiplyCof;
      this.exchangeValue = this.exchangeValue.toFixed(2)
    },
    worldCurrenciesRate() {
      try {
        this.rateUsd = this.currencyList["USD"]["Value"].toFixed(2)
        this.rateEur = this.currencyList["EUR"]["Value"].toFixed(2)
        this.rateCny = this.currencyList["CNY"]["Value"].toFixed(2)
        this.rateGbp = this.currencyList["GBP"]["Value"].toFixed(2)
        this.runLine = `Текущий курс валют $${this.rateUsd}; €${this.rateEur}; £${this.rateGbp}; ¥${this.rateCny}`
      } catch (error) {
        if (this.rateUsd == undefined || this.rateEur == undefined || this.rateCny == undefined || this.rateGbp == undefined) {
          this.runLine = "Текущий курс валют недоступен"
        }
      }
    },
    curFullNameInfoFunc() {
      try {        
        this.currencyFullName = this.currencyList[this.currencyShortName]["Name"]       
        this.currencyInfo = `Вы хотите обменять ${this.currencyList[this.currencyShortName]["Name"]}`
      } catch (error) {
        this.currencyInfo = 'Валюта не выбрана'
      }
    },    
  },
};
</script>

<style scoped>
h1 {
  font-weight: bolder;
  color: rgb(65, 138, 87);
  position: top;


}
</style>
<!--src="https://www.cbr-xml-daily.ru/money.js"-->