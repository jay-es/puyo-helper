<template>
  <div id="app">
    <fieldset>
      <legend>Size</legend>
      <label>
        Rows
        <input type="number" min="1" v-model.number="rowNum">
      </label>

      <label>
        Cols
        <input type="number" min="1" v-model.number="colNum">
      </label>
    </fieldset>

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
    trimmed() {
      // tableDataを複製
      const arr = this.tableData.map((row) => row.concat());

      // 末尾の空の行を削除
      for (let ri = this.rowNum; ri--;) {
        const isEmptyRow = arr[ri].every(col => col.color === 0);

        if (!isEmptyRow) break;

        arr.splice(-1, 1);
      }

      // 末尾の空の列を削除
      for (let ci = this.colNum; ci--;) {
        const isEmptyCol = arr.every(row => row[ci].color === 0);

        if (!isEmptyCol) break;

        arr.forEach(row => row.splice(-1, 1));
      }

      return arr;
    },
    resultText() {
      return this.trimmed.map((row) => (
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
    /**
     * 行を追加
     * @param {number} rows 追加する行数
     */
    addRows(rows) {
      const orgLen = this.tableData.length;

      for (let ri = 0; ri < rows; ri++) {
        this.tableData.push([]);
        this.addCols(this.tableData[orgLen + ri], this.colNum);
      }
    },
    /**
     * 列を追加
     * @param {array} row 追加する行
     * @param {number} cols 追加する列数
     */
    addCols(row, cols) {
      for (let ci = 0; ci < cols; ci++) {
        row.push(this.makeNewCell(0));
      }
    },
  },
  watch: {
    rowNum(newVal, oldVal) {
      if (newVal > oldVal) {
        this.addRows(newVal - oldVal);
      } else {
        this.tableData.splice(newVal - oldVal, oldVal);
      }
    },
    colNum(newVal, oldVal) {
      this.tableData.forEach((row) => {
        if (newVal > oldVal) {
          this.addCols(row, newVal - oldVal);
        } else {
          row.splice(newVal - oldVal, oldVal);
        }
      });
    },
  },
  created() {
    this.addRows(this.rowNum);
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
input[type="radio"],
input[type="checkbox"] {
  cursor: pointer;
}

input[type="number"] {
  margin-bottom: -2px;
  width: 40px;
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
