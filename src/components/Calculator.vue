<template>
  <div class="calculator-container">
    <h1>Калькулятор на Vue</h1>
    <div class="calculator">
      <div class="display previous">{{previous}}</div>
      <div class="display operator-sign">{{operatorSign}}</div>
      <div class="display">{{current || '0'}}</div>
      <div @click="clear" class="btn">C</div>
      <div @click="sign" class="btn">+/-</div>
      <div @click="percent" class="btn">%</div>
      <div @click="divide" class="btn operator">÷</div>
      <div @click="append(7)" class="btn">7</div>
      <div @click="append(8)" class="btn">8</div>
      <div @click="append(9)" class="btn">9</div>
      <div @click="multiply" class="btn operator">x</div>
      <div @click="append(4)" class="btn">4</div>
      <div @click="append(5)" class="btn">5</div>
      <div @click="append(6)" class="btn">6</div>
      <div @click="minus" class="btn operator">-</div>
      <div @click="append(1)" class="btn">1</div>
      <div @click="append(2)" class="btn">2</div>
      <div @click="append(3)" class="btn">3</div>
      <div @click="plus" class="btn operator">+</div>
      <div @click="append(0)" class="btn zero">0</div>
      <div @click="dot" class="btn">.</div>
      <div @click="equal" class="btn">=</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      current: '0',
      operator: null,
      previous: null,
      operatorClicked: false,
      operatorSign: null,
    }
  },
  methods: {
    clear() {
      this.current = '0';
      this.previous = null;
      this.operatorSign = null;
    },
    sign() {
      if (this.current == 0) {
        return null;
      }
      this.current = this.current.charAt(0) === '-' ?
        this.current.slice(1) : `-${this.current}`;
    },
    percent() {
      this.current = `${parseFloat(this.current) / 100}`
    },
    append(number) {
      if (this.current == '0' && number == '0') {
        return null;
      }
      if (this.current == '0' && number != 0) {
        this.current = ''
      }
      this.current = `${this.current}${number}`;
    },
    dot() {
      if (this.current == 0) {
        return null;
      }
      if (this.current.indexOf('.') === -1) {
        this.append('.')
      }
    },
    setPrevious() {
      this.operatorClicked = true;
      this.previous = this.current;
    },
    divide() {
      if (this.operatorClicked) {
        return null;
      }
      this.operatorSign = '÷'
      console.log('divide')
      this.setPrevious();
      this.operator = (a, b) => a / b;
      this.current = '0';
    },
    multiply() {
      if (this.operatorClicked) {
        return null;
      }
      this.operatorSign = '*'
      console.log('multiply')
      this.setPrevious();
      this.operator = (a, b) => a * b;
      this.current = '0';
    },
    minus() {
      if (this.operatorClicked) {
        return null;
      }
      this.operatorSign = '-'
      console.log('minus')
      this.setPrevious();
      this.operator = (a, b) => a - b;
      this.current = '0';
    },
    plus() {
      if (this.operatorClicked) {
        return null;
      }
      this.operatorSign = '+'
      console.log('plus')
      this.setPrevious();
      this.operator = (a, b) => a + b;
      this.current = '0';
    },
    equal() {
      if (this.previous) {
        this.current = `${this.operator(
          parseFloat(this.previous), 
          parseFloat(this.current)
        )}`;
          this.previous = null;
          this.operatorClicked = false;
          this.operatorSign = null;
      }
    },
  }
}
</script>


<style scoped>
  .calculator-container {
    transition: .4s ease;
    color: #292126;
    text-align: center;
  }
  h1 {
    font-size: 3em;
    margin: 1em 0;
  }
  .calculator {
    display: grid;
    max-width: 600px;
    margin: 0 auto;
    grid-template-columns: repeat(4, 1fr);
    grid-auto-rows: minmax(50px, auto);
    font-size: 40px;
  }
  .zero {
    grid-column: 1/3;
  }
  .btn {
    border: 1px solid #999;
    background-color: #f2f2f2;
    cursor: pointer;
    transition: .4s ease all;
  }
  .btn:active {
    transform: scale(.9)
  }
  .btn:hover {
    background-color: #f8ccf9
  }
  .display {
    grid-column: 1 / 5;
    background-color: #333;
    color: white;
    text-align: right;
    padding: 5px 25px 0px;
  }
  .operator {
    color: #fff;
    background-color: orange;
  }
  .previous {
    font-size: 25px;
    font-style: italic;
  }
</style>
