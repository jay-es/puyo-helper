<template>
  <table>
    <tbody>
      <tr v-for="(row, ri) of tableData">
        <td v-for="(col, ci) of row" @click="inputCell(ri, ci)">
          <span class="cell" :data-color="col.color" :data-shape="col.shape">
            {{ col.color }}
          </span>
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

  &[data-color="1"] { background-color: blue; }
  &[data-color="2"] { background-color: green; }
  &[data-color="3"] { background-color: purple; }
  &[data-color="4"] { background-color: red; }
  &[data-color="5"] { background-color: yellow; }
}


</style>
