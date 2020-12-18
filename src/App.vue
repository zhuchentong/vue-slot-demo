<template>
  <div>
    <data-tree ref="tree" :source="source">
      <!--覆盖内部模版-->
      <template v-slot:node="node">
        <div>-----{{ node.label }}-----</div>
      </template>
    </data-tree>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import DataTree from "./components/data-tree.vue";

// 数据源
const source = [
  {
    key: 1,
    label: "root-1",
    children: [
      {
        key: 11,
        label: "node-1-1",
        children: [
          {
            key: 111,
            label: "node-1-1-1",
          },
          {
            key: 112,
            label: "node-1-1-2",
          },
        ],
      },
      {
        key: 12,
        label: "node-12",
      },
    ],
  },
  {
    key: 2,
    label: "root-2",
  },
  {
    key: 3,
    label: "root-3",
    children: [
      {
        key: 31,
        label: "node-3-1",
      },
      {
        key: 32,
        label: "node-3-2",
      },
    ],
  },
];

export default defineComponent({
  name: "App",
  components: {
    DataTree,
  },
  setup() {
    return {
      source,
    };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
