<template>
  <table>
    <tbody>
      <tr v-for="(row, ri) of tableData">
        <td v-for="(col, ci) of row" @click="inputCell(ri, ci)">
          <span class="cell" :data-color="col.color" :data-shape="col.shape"><br></span>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  props: ['currentColor', 'currentShape', 'tableData'],
  methods: {
    inputCell(ri, ci) {
      this.tableData[ri][ci].color = this.currentColor;
      this.adjustShape(ri, ci);
      this.walkArround(ri, ci, this.adjustShape);
    },

    // 形状を整える
    adjustShape(ri, ci) {
      let shape = '';

      this.walkArround(ri, ci, (r2, c2, i) => {
        if (this.tableData[r2][c2].color === this.tableData[ri][ci].color) {
          shape += 'udrl'[i];
        }
      });

      this.tableData[ri][ci].shape = shape;
    },

    // セルがあるか
    existsCell(ri, ci) {
      return !!(this.tableData[ri] && this.tableData[ri][ci]);
    },

    // 上下左右で処理を行う
    walkArround(ri, ci, fn) {
      if (typeof fn !== 'function') {
        throw new TypeError('inputTable#walkArround: 第三引数が関数ではない')
      }

      const arr = [[ri - 1, ci], [ri + 1, ci], [ri, ci + 1], [ri, ci - 1]];

      arr.forEach((v, i) => {
        if (this.existsCell(...v)) {
          fn(...v, i);
        }
      });
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
  user-select: none;
}

td {
  position: relative;
  border: 1px solid $borderColor;
  width: 16px;
  height: 16px;
  cursor: pointer;
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
</style>
