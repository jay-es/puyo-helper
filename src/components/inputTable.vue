<template>
  <div @contextmenu.prevent>
    <table>
      <tbody>
        <tr v-for="(row, ri) of tableData">
          <td v-for="(col, ci) of row"
            @mousedown="mouseEventHandler($event, ri, ci)"
            @mouseover="mouseEventHandler($event, ri, ci)"
          >
            <span class="cell" :data-color="col.color" :data-shape="col.shape"><br></span>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// ojama,blankのインデックス
const OJAMA = 5;
const BLANK = 6;

export default {
  props: ['currentColor', 'currentShape', 'tableData', 'isAutoShaping'],
  methods: {
    inputCell(ri, ci, color) {
      this.tableData[ri][ci].color = color;

      if (this.isAutoShaping) {
        this.adjustShape(ri, ci);
        this.walkArround(ri, ci, this.adjustShape);
      } else {
        this.tableData[ri][ci].shape = this.currentShape;
      }
    },

    // 形状を整える
    adjustShape(ri, ci) {
      let shape = '';

      // 5色の場合のみ
      if (this.tableData[ri][ci].color < OJAMA) {
        this.walkArround(ri, ci, (r2, c2, i) => {
          if (this.tableData[r2][c2].color === this.tableData[ri][ci].color) {
            shape += 'udrl'[i];
          }
        });
      }

      this.tableData[ri][ci].shape = shape;
    },

    // セルがあるか
    existsCell(ri, ci) {
      return !!(this.tableData[ri] && this.tableData[ri][ci]);
    },

    // 上下左右で処理を行う
    walkArround(ri, ci, fn) {
      if (typeof fn !== 'function') {
        throw new TypeError('inputTable#walkArround: 第三引数が関数ではない');
      }

      const arr = [[ri - 1, ci], [ri + 1, ci], [ri, ci + 1], [ri, ci - 1]];

      arr.forEach((v, i) => {
        if (this.existsCell(...v)) {
          fn(...v, i);
        }
      });
    },

    // クリック+ドラッグ
    mouseEventHandler($event, ri, ci) {
      const buttons = $event.buttons;

      if (buttons === 1) { // 左ボタン
        this.inputCell(ri, ci, this.currentColor);
      } else if (buttons === 2) { // 右ボタン
        this.inputCell(ri, ci, BLANK);
      }
    },
  },
};
</script>
<style lang="scss" scoped>
$borderColor: #ccc;

table {
  margin: 10px auto;
  border: 1px solid $borderColor;
  border-collapse: collapse;
}

td {
  position: relative;
  border: 1px dotted $borderColor;
  width: 16px;
  height: 16px;
  cursor: pointer;
}
</style>
