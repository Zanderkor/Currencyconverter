<template> 
  <h1>Валютный менеджер</h1>
  <p>Обменять валюту</p>
  <marquee>Текущий курс валют</marquee>
  <input name="Валюта на руках" placeholder="На руках" id="handedCurrency" >
  <input name="Валюта для обмена" placeholder="После обмена" id="changeCurrency" >
  <div id="USD">Доллар США $ — 00,0000 руб.</div>
  <div id="EUR">Евро € — 00,0000 руб.</div>
  <form>
  <label for="currency">Выбирете валюту:</label>
  <select id="currencyHave" name="currency">
    <option value="apple">Яблоко</option>
    <option value="banana">Банан</option>
    <option value="orange">Апельсин</option>
  </select>
  <select id="currencyGet" name="currency">
    <option value="apple">Яблоко</option>
    <option value="banana">Банан</option>
    <option value="orange">Апельсин</option>
  </select>
  <button>Обменять</button>
</form>

</template>



<script >
function handleFruitChange() {
    const selectElement = document.getElementById('currency');
    const selectedFruit = selectElement.value;
    alert('Вы выбрали: ' + selectedFruit);
  }
function CBR_XML_Daily_Ru(rates) {
     
  var USDrate = rates.Valute.USD.Value.toFixed(4).replace('.', ',');
  var USD = document.getElementById('USD');
  USD.innerHTML = USD.innerHTML.replace('00,0000', USDrate);
  USD.innerHTML += trend(rates.Valute.USD.Value, rates.Valute.USD.Previous);

  var EURrate = rates.Valute.EUR.Value.toFixed(4).replace('.', ',');
  var EUR = document.getElementById('EUR');
  EUR.innerHTML = EUR.innerHTML.replace('00,0000', EURrate);
  EUR.innerHTML += trend(rates.Valute.EUR.Value, rates.Valute.EUR.Previous);
}
</script>

<style scoped>
h1 {
  font-weight: bolder;
  color: rgb(65, 138, 87);
  position: top;


}
</style>
<!--src="https://www.cbr-xml-daily.ru/money.js"-->