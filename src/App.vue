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
      <option v-for="curName in fullCurrencyArray" v-bind:value="curName"> {{ curName }} </option>
    </select>
  </form>
  <p v-bind:value="justFunction()">{{ currencyIndex }}</p>
  <div class="container">
    <Bar id="my-chart-id" :options="chartOptions" :data="chartData" />
  </div>
  <p>Числа дат {{ today }} {{ dayDates }} </p>

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
      today: new Date(Date.now()),
      exchangeValue: null,
      curInHand: null,
      multiplyCof: null,
      jaySon: ' ',
      curArr: [],
      fullCurrencyArray: [],
      currencyList: '',
      currencyInfo: '',
      currencyShortNamePick: '',
      currencyShortName: '',
      currencyFullName: '',
      currencyIndex: null,
      rateUsd: null,
      rateEur: null,
      rateGbp: null,
      rateCny: null,
      datacollection: null,
      dayDates: [],
      chartData: {
        labels: [],
        datasets: [{ data: [] }]
      },
      chartOptions: {
        responsive: true
      }
    }
  },
  //https://www.cbr-xml-daily.ru/archive/2024/(месяц)/(день)/daily_json.js
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
  mounted(){    
      try {
        let i = 0;
        let pasteDate = 0;        
        do {
          i = i + 1
          pasteDate = this.today.setDate(this.today.getDate() - i)         
          this.chartData.labels.unshift(new Date(pasteDate).toLocaleString("ru-RU", { month: 'numeric', day: 'numeric' }));
          this.today.setDate(this.today.getDate() + i)
        } while (i < 30);
      } catch (error) {
        this.dayDates = "ошибка"
      }
      return this.chartData.labels    
  },
  /*computed: {
    dateNumbers() {
      try {
        let i = 0;
        let pasteDate = 0;        
        do {
          i = i + 1
          pasteDate = this.today.setDate(this.today.getDate() - i)         
          this.chartData.labels.unshift(new Date(pasteDate).toLocaleString("ru-RU", { month: 'numeric', day: 'numeric' }));
          this.today.setDate(this.today.getDate() + i)
        } while (i < 30);
      } catch (error) {
        this.dayDates = "хуйня"
      }         
    }
  },*/
  methods: {
    justFunction() {
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
    verticalChartParameter() {
      const a = 0;
      let indexOfNumber = 0;
      let pasteDate = 0;
      let pasteDay = 0;
      let pasteMonth = 0;
      const todayDate = new Date(Date.now()).toLocaleString("ru-RU", { month: 'numeric', day: 'numeric' })
      /*do {
        pasteDate=todayDate.setDate(todayDate.getDate()-a)

      pasteDay = formatDate.substring(0, indexOfNumber)
      pasteMonth = formatDate.substring(indexOfNumber + 1)
      //axios.get(`https://www.cbr-xml-daily.ru/archive/2024/${pasteMonth}/${pasteDay}/daily_json.js`)
        //.then(res => (this.))

    }*/
  },
}
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