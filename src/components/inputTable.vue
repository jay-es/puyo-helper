<template>
  <table>
    <tbody>
      <tr v-for="(row, ri) of tableData">
        <td v-for="(col, ci) of row"
          @click="inputCell(ri, ci)" @contextmenu.prevent="inputCell(ri, ci, true)"
        >
          <span class="cell" :data-color="col.color" :data-shape="col.shape"><br></span>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  props: ['currentColor', 'currentShape', 'tableData', 'isAutoShaping'],
  methods: {
    inputCell(ri, ci, isBlank) {
      this.tableData[ri][ci].color = isBlank ? 0 : this.currentColor;

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
        throw new TypeError('inputTable#walkArround: 第三引数が関数ではない');
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
}

td {
  position: relative;
  border: 1px dotted $borderColor;
  width: 16px;
  height: 16px;
  cursor: pointer;
}
</style>
