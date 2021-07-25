<template>
  <div class="calculator">
    <div class="display">{{ board || 0 }}</div>
    <div v-for="button in buttons" :key="button.key" @click="handle_function_call(button.handler, button.key)" class="btn" :class="button.class">
      {{ button.key }}
    </div>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      board: '',
      current: '',
      result: false,
      buttons: [
        {key: 'AC', handler: 'clear', class: ''},
        {key: '+/-', handler: 'sign', class: ''},
        {key: '%', handler: 'append', class: ''},
        {key: '/', handler: 'append', class: 'operator'},
        {key: '7', handler: 'append', class: ''},
        {key: '8', handler: 'append', class: ''},
        {key: '9', handler: 'append', class: ''},
        {key: '*', handler: 'append', class: 'operator'},
        {key: '4', handler: 'append', class: ''},
        {key: '5', handler: 'append', class: ''},
        {key: '6', handler: 'append', class: ''},
        {key: '-', handler: 'append', class: 'operator'},
        {key: '1', handler: 'append', class: ''},
        {key: '2', handler: 'append', class: ''},
        {key: '3', handler: 'append', class: ''},
        {key: '+', handler: 'append', class: 'operator'},
        {key: '0', handler: 'append', class: 'zero'},
        {key: '.', handler: 'dot', class: ''},
        {key: '=', handler: 'equal', class: 'operator'},
      ],
    }
  },
  methods: {
    clear() {
      this.board = '';
      this.current = '';
      this.result = false;
    },
    sign() {
      let pattern = new RegExp('[-0-9.]+$');
      if (this.current) {
        if (this.board.match(pattern, this.board)[0].charAt(0) === '-') {
          this.board = this.board.replace(pattern, `${this.current}`);
        } else {
          this.board = this.board.replace(pattern, `-${this.current}`);
        }
      }
    },
    append(key) {
      let lastChar = this.board.slice(-2);
      let signs = new RegExp('[-+/*%]');

      if (signs.test(key)) {
        this.result = false;
        if (this.current) {
          if (signs.test(lastChar)) {
            this.board = `${this.board.slice(0, -3)} ${key} `;
          } else if (this.current[this.current.length - 1] == '.') {
            // doing nothing
          } else {
            this.current = '';
            this.board = `${this.board} ${key} `;
          }
        }
      } else {
        if (this.result) {
          this.clear()
        }
        this.board = `${this.board}${key}`;
        this.current = `${this.current}${key}`;
      }
    },
    dot() {
      if (this.current && this.current.indexOf('.') === -1) {
        this.append('.');
      }
    },
    equal() {
      let array = this.board.split(" ");
      while (array.length > 1) {
        const mulDiv = array.findIndex(a => a === "*" || a === "/" || a === "%");
        const index = mulDiv === -1 ? array.findIndex(b => b === "+" || b === "-") : mulDiv;
        const a = Number(array[index - 1]);
        const b = Number(array[index + 1]);
        const evaluate =
          array[index] === "/"
            ? a / b
            : array[index] === "*"
            ? a * b
            : array[index] === "%"
            ? a % b
            : array[index] === "-"
            ? a - b
            : a + b;
        array.splice(index - 1, 3, evaluate);
      }
      this.board = String(array[0]);
      this.result = true;
    },
    handle_function_call(target_function, key) {
      if (target_function == 'append') {
          this[target_function](key);
      } else {
        this[target_function]();
      }
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
.calculator {
  display: grid;
  user-select: none;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(50px, aut);
  width: 400px;
  font-size: 2rem;
  text-align: center;
  margin: 0 auto;
}
.display {
  outline: 1px solid #333;
  grid-column: 1 / 5;
  width: 400px;
  color: #fff;
  text-align: right;
  padding-top: .5rem;
  padding-right: 1rem;
  background-color: #333;
  overflow: hidden;
}
.zero {
  grid-column: 1 / 3;
}
.btn {
  cursor: pointer;
  outline: 1px solid #999;
  background-color: #F2F2F2;
}
.btn:active {
  transform: scale(0.95);
}
.operator {
  color: #fff;
  background-color: #FFB732;
}
</style>
