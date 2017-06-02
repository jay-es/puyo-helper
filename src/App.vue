<template>
  <div id="app">
    <h1></h1>
    <h2>Essential Links</h2>

    <label v-for="(colorName, ci) of colors">
      <input type="radio" :value="ci" v-model="currentColor">{{ colorName }}
    </label>

    <input-table
      :table-data="tableData"
      :current-color="currentColor"
      :current-shape="currentShape"
    ></input-table>

    <textarea :value="resultText"></textarea>
  </div>
</template>

<script>
import inputTable from './components/inputTable.vue';

export default {
  components: {
    inputTable,
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
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

textarea {
  width: 50em;
  height: 10em;
}
</style>
