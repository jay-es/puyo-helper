<template>
  <div id="app">
    <fieldset>
      <legend>Color</legend>
      <label v-for="(colorName, ci) of colors">
        <input type="radio" :value="ci" v-model="currentColor">{{ colorName }}
      </label>
    </fieldset>

    <fieldset>
      <legend>Shape</legend>

      <label>
        <input type="radio" :value="true" v-model="isAutoShaping">Auto
      </label>

      <label>
        <input type="radio" :value="false" v-model="isAutoShaping">Custom
      </label>
    </fieldset>

    <shape-option
      :class="{ hidden: isAutoShaping }"
      :current-color="currentColor"
      :current-shape.sync="currentShape"
    ></shape-option>

    <input-table
      :table-data="tableData"
      :current-color="currentColor"
      :current-shape="currentShape"
      :is-auto-shaping="isAutoShaping"
    ></input-table>

    <textarea :value="resultText"></textarea>
  </div>
</template>

<script>
import inputTable from './components/inputTable.vue';
import shapeOption from './components/shapeOption.vue';

export default {
  components: {
    inputTable,
    shapeOption,
  },
  name: 'app',
  data() {
    return {
      colors: ['blank', 'blue', 'green', 'purple', 'red', 'yellow'],
      shapes: [''],
      rowNum: 7,
      colNum: 5,
      tableData: [],
      currentColor: 0,
      currentShape: '',
      isAutoShaping: true,
    };
  },
  computed: {
    resultText() {
      return this.tableData.map((row) => (
        row.map((col) => {
          if (col.color === 0) {
            return ':puyo_blk:';
          }

          const color = this.colors[col.color].substr(0, 1);

          return `:puyo_${color}${col.shape}:`;
        }).join('')
      )).join('\n');
    },
  },
  methods: {
    makeNewCell(color = 0, shape = '') {
      return {
        color,
        shape,
      };
    },
  },
  created() {
    for (let ri = 0; ri < this.rowNum; ri++) {
      this.tableData.push([]);

      for (let ci = 0; ci < this.colNum; ci++) {
        this.tableData[ri].push(this.makeNewCell());
      }
    }
  },
};
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  user-select: none;
}

fieldset {
  display: inline-block;
  border: 1px solid #ccc;
}
input {
  cursor: pointer;
}

.hidden {
  visibility: hidden;
}

.cell {
  $cellPadding: 2px;
  position: absolute;
  top: $cellPadding;
  right: $cellPadding;
  bottom: $cellPadding;
  left: $cellPadding;
  border-radius: 50%;

  &[data-color="1"] { background-color: #25c; }
  &[data-color="2"] { background-color: #0a2; }
  &[data-color="3"] { background-color: #80d; }
  &[data-color="4"] { background-color: #c12; }
  &[data-color="5"] { background-color: #fb1; }

  &::before,
  &::after {
    content: '';
    position: absolute;
    top: $cellPadding;
    right: $cellPadding;
    bottom: $cellPadding;
    left: $cellPadding;
    background-color: inherit;
  }

  &[data-shape*="u"]::before { top: -$cellPadding; }
  &[data-shape*="d"]::before { bottom: -$cellPadding; }
  &[data-shape*="r"]::after { right: -$cellPadding; }
  &[data-shape*="l"]::after { left: -$cellPadding; }
}

textarea {
  width: 50em;
  height: 10em;
}
</style>
