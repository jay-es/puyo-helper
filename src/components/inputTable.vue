<template>
  <div
    @contextmenu.prevent
    @mousemove="doDrag"
  >
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
    <span class="sizer" @mousedown="startDrag">●</span>
  </div>
</template>

<script>
// ojama,blankのインデックス
const OJAMA = 5;
const BLANK = 6;

// ドラッグでサイズ変更する際に、スタート時の状態を保存するためのオブジェクト
const drag = {};

export default {
  props: ['currentColor', 'currentShape', 'tableData', 'isAutoShaping', 'rowNum', 'colNum'],
  methods: {
    inputCell(ri, ci, color) {
      this.tableData[ri][ci].color = color;

      if (this.isAutoShaping) {
        this.adjustShape(ri, ci);
        this.walkArround(ri, ci, this.adjustShape);
      } else {
        this.tableData[ri][ci].shape = this.currentColor < OJAMA ? this.currentShape : '';
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
      // サイズ変更中は無視
      if (drag.isDragging) return;

      const buttons = $event.buttons;

      if (buttons === 1) { // 左ボタン
        this.inputCell(ri, ci, this.currentColor);
      } else if (buttons === 2) { // 右ボタン
        this.inputCell(ri, ci, BLANK);
      }

      // 右ドラッグでマウスジェスチャが反応しないようにする
      $event.stopPropagation();
    },

    startDrag($event) {
      drag.isDragging = true;
      drag.startX = $event.x;
      drag.startY = $event.y;
      drag.rowNum = this.rowNum;
      drag.colNum = this.colNum;
    },
    endDrag() {
      drag.isDragging = false;
    },
    doDrag($event) {
      if (!drag.isDragging || $event.buttons !== 1) return;

      const deltaRow = Math.round(($event.y - drag.startY) / 19);
      const deltaCol = Math.round(($event.x - drag.startX) / 19 * 2);

      this.$emit('update:rowNum', drag.rowNum + deltaRow);
      this.$nextTick(() => {
        this.$emit('update:colNum', drag.colNum + deltaCol);
      });
    },
  },
  created() {
    document.addEventListener('mouseup', this.endDrag);
  },
};
</script>
<style lang="scss" scoped>
$borderColor: #ccc;

div {
  padding-bottom: 3em;
}

.sizer {
  cursor: nw-resize;
}

table {
  display: inline-table;
  vertical-align: text-bottom;
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
