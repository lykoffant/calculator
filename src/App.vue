<script>
export default {
  data() {
    return {
      raw: "",
      hasError: false,
      buttons: {
        numbers: [
          { name: "zero", value: "0" },
          { name: "one", value: "1" },
          { name: "two", value: "2" },
          { name: "three", value: "3" },
          { name: "four", value: "4" },
          { name: "five", value: "5" },
          { name: "six", value: "6" },
          { name: "seven", value: "7" },
          { name: "eight", value: "8" },
          { name: "nine", value: "9" },
          { name: "decimal-point", value: "." },
        ],
        operators: [
          { name: "plus", value: "+" },
          { name: "minus", value: "-" },
          { name: "multiply", value: "*" },
          { name: "divide", value: "/" },
          { name: "closing-bracket", value: ")" },
          { name: "opening-bracket", value: "(" },
        ],
        actions: [
          { name: "clear", value: "C", handler: "clear" },
          { name: "calc", value: "=", handler: "calc" },
        ],
      },
    };
  },

  watch: {
    raw(newRaw) {
      this.hasError = !newRaw
        .split("")
        .every((char) => this.validCharacters.includes(char));
    },
  },

  computed: {
    validCharacters() {
      const { numbers, operators } = this.buttons;

      return [...numbers, ...operators]
        .map((button) => button.value)
        .concat(" ");
    },
  },

  methods: {
    runMethod(method) {
      this[method]();
    },

    input(e) {
      if (this.isResult && /\d/.test(e.target.textContent)) {
        this.raw = e.target.textContent;
      } else {
        this.raw += e.target.textContent;
      }
    },

    clear() {
      this.raw = "";
    },

    calc() {
      try {
        this.raw = String(eval(this.raw));
      } catch (err) {
        alert(err);
      }
    },
  },
};
</script>

<template>
  <h1 class="title">Calculator</h1>

  <div class="container">
    <input
      class="display"
      :class="{ display_error: hasError }"
      type="text"
      placeholder="0"
      v-model="raw"
      @keydown.enter="calc"
    />

    <button
      v-for="button in buttons.numbers"
      :key="button.name"
      class="btn"
      :style="{ gridArea: button.name }"
      @click="input"
    >
      {{ button.value }}
    </button>

    <button
      v-for="button in buttons.operators"
      :key="button.name"
      class="btn btn_operator"
      :style="{ gridArea: button.name }"
      @click="input"
    >
      {{ button.value }}
    </button>

    <button
      v-for="button in buttons.actions"
      :key="button.name"
      :class="`btn_${button.name}`"
      class="btn"
      :style="{ gridArea: button.name }"
      @click="runMethod(button.handler)"
    >
      {{ button.value }}
    </button>
  </div>
</template>

<style scoped>
.title {
  margin-bottom: 24px;
  font-size: 32px;
  font-weight: bold;
  text-align: center;
}

.container {
  overflow: hidden;
  display: grid;
  grid-template-areas:
    "display display         display         display"
    "clear   opening-bracket closing-bracket divide"
    "seven   eight           nine            multiply"
    "four    five            six             minus"
    "one     two             three           plus"
    "zero    zero            decimal-point   calc";
  grid-template-columns: repeat(4, 1fr);
  gap: 2px;

  max-width: 512px;
  margin: 0 auto;

  background-color: black;
  border: 2px solid black;
  border-radius: 16px;
  box-shadow: 0 25px 50px -12px rgb(0 0 0 / 50%);
}

.display {
  grid-area: display;

  min-width: 0;
  padding: 16px;

  font-size: 32px;
  text-align: right;

  border: none;
  outline: none;
}

.display_error {
  color: red;
}

.btn {
  padding: 16px;

  font-size: 24px;
  font-weight: bold;

  background-color: rgb(148 163 184);
  border: none;
  border-radius: 0;
}

.btn_operator {
  background-color: rgb(251 191 36);
}

.btn_calc {
  background-color: rgb(74 222 128);
}

.btn_clear {
  background-color: rgb(248 113 113);
}
</style>
