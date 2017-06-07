<template>
  <table>
    <tbody>
      <tr>
        <td><br></td>
        <td><input type="checkbox" v-model="up" @click="change" /></td>
        <td><br></td>
      </tr>
      <tr>
        <td><input type="checkbox" v-model="left" @click="change" /></td>
        <td>
          <span class="cell" :data-color="currentColor" :data-shape="currentShape"><br></span>
        </td>
        <td><input type="checkbox" v-model="right" @click="change" /></td>
      </tr>
      <tr>
        <td><br></td>
        <td><input type="checkbox" v-model="down" @click="change" /></td>
        <td><br></td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  props: ['currentColor', 'currentShape'],
  data() {
    return {
      up: false,
      down: false,
      right: false,
      left: false,
    };
  },
  methods: {
    change() {
      let shape = '';

      if (this.up) shape += 'u';
      if (this.down) shape += 'd';
      if (this.right) shape += 'r';
      if (this.left) shape += 'l';

      this.$emit('update:currentShape', shape);
    },
  },
};
</script>

<style lang="scss" scoped>
table {
  display: inline-table;
  vertical-align: middle;
  border-collapse: collapse;
  line-height: 1;
}
td {
  position: relative;
  width: 16px;
  height: 16px;
}
input {
  margin: 0;
}
</style>