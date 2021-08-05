<template>
  <div class="title">
    <h1>{{ msg }}</h1>
    <div class="calc">
      <input
        type="number"
        id="operand1"
        placeholder="Operand1"
        v-model.number="operand1"
      />
      <input
        type="number"
        id="operand2"
        placeholder="Operand2"
        v-model.number="operand2"
      />
      = {{ result }}
      <br />
      <div v-if="!!error">Ошибка! {{ error }}</div>
      <br />
      <div class="keyboard">
        <button
          v-for="operand in operands"
          v-bind:key="operand"
          v-bind:title="operand"
          v-bind:disabled="operand1 === '' || operand2 === ''"
          @click="calculate(operand)"
        >
          {{ operand }}
        </button>
        <!-- <button v-on:click="calculate('+')">+</button>
        <button v-on:click="calculate('-')">-</button>
        <button v-on:click="calculate('*')">*</button>
        <button v-on:click="calculate('/')">/</button>
        <button v-on:click="calculate('^')">^</button>
        <button v-on:click="calculate('//')">/</button> -->
      </div>
      <br />
      <label>
        <input type="checkbox" v-model="virtual_keys" />
        Показать/Скрыть экранную клавиатуру
      </label>

      <div class="virtual_keys" v-if="virtual_keys">
        <button v-for="key in keys" v-bind:key="key" v-on:click="inputNum(key)">
          {{ key }}
        </button>
        <button v-on:click="deleteNum">Delete</button>
        <br />
        <label
          ><input
            type="radio"
            value="operand1"
            v-model="operand"
            v-on:change="focusInput($event)"
          />Операнд 1</label
        >
        <label
          ><input
            type="radio"
            value="operand2"
            v-model="operand"
            v-on:change="focusInput($event)"
          />Операнд 2</label
        >
      </div>

      <div class="logs">
        <div v-for="(log, id) in logs" v-bind:key="id">{{ log }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Calculator",
  data: () => ({
    operand: "",
    operand1: "",
    operand2: "",
    operands: ["+", "-", "*", "/", "^", "//"],
    keys: [1, 2, 3, 4, 5, 6, 7, 8, 9, 0],
    virtual_keys: false,
    result: 0,
    error: "",
    logs: {},
  }),
  props: {
    msg: String,
  },
  methods: {
    add() {
      this.result = this.operand1 + this.operand2;
    },
    sub() {
      this.result = this.operand1 - this.operand2;
    },
    multi() {
      this.result = this.operand1 * this.operand2;
    },
    div() {
      const { operand1, operand2 } = this;
      if (operand2 === 0) {
        this.error = "Делить на ноль нельзя";
      } else {
        this.result = operand1 / operand2;
      }
    },
    pow() {
      this.result = this.operand1 ** this.operand2;
    },
    intdiv() {
      const { operand1, operand2 } = this;
      if (operand2 === 0) {
        this.error = "Делить на ноль нельзя";
      } else {
        this.result = Math.floor(operand1 / operand2);
      }
    },
    focusInput(event) {
      const input = this.$el.querySelector(`#${event.target.value}`);
      input.focus();
      // console.log(event, this.$el, input);
    },
    deleteNum() {
      this[this.operand] = +String(this[this.operand]).slice(0, -1);
    },
    inputNum(key) {
      this[this.operand] = +(this[this.operand] += String(key));
    },
    calculate(operation = "+") {
      this.error = "";
      switch (operation) {
        case "+":
          this.add();
          break;
        case "-":
          this.sub();
          break;
        case "*":
          this.multi();
          break;
        case "/":
          this.div();
          break;
        case "^":
          this.pow();
          break;
        case "//":
          this.intdiv();
          break;
      }
      const key = Date.now();
      const value = `${this.operand1}${operation}${this.operand2}=${this.result}`;
      this.$set(this.logs, key, value);

      // this.logs[
      //   Date.now()
      // ] = `${this.operand1}${operation}${this.operand2}=${this.result}`;
      // Vue.set(this.logs, key, value);
    },
  },
};
</script>