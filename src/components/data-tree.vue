<template>
  <div>
    <data-tree-item
      v-for="node in source"
      :key="node.key"
      :node="node"
    ></data-tree-item>
  </div>
</template>

<script lang="ts">
import { defineComponent, provide, ref } from "vue";
import DataTreeItem from "./data-tree-item.vue";

export default defineComponent({
  name: "data-tree",
  components: {
    DataTreeItem,
  },
  props: {
    source: {
      type: Array,
    },
    selection: {
      type: Array,
      default: () => [],
    },
    canSelect: {
      type: Function,
      default: () => true,
    },
    mode: {
      type: String,
      default: () => "multiple", // 'single' - 单选 | 'multiple' - 多选
    },
  },
  setup(props) {
    // 选择项
    const selections = ref<any[]>([...props.selection]);

    // 选择操作
    const selectionActions = {
      // 单选
      single: (node, selection) => {
        selections.value = selection ? [node] : [];
      },
      // 多选
      multiple: (node, selection) => {
        if (selection) {
          selections.value.push(node);
        } else {
          selections.value.splice(
            selections.value.findIndex((x) => x === node),
            1
          );
        }
      },
    };

    provide("selections", selections);

    // 更新选择项
    provide("updateSelection", (node, selection) => {
      const action = selectionActions[props.mode];
      action(node, selection);
    });

    const getSelections = () => selections.value;
    const getSelectionsKey = () => selections.value.map((x) => x.key);

    return {
      getSelections,
      getSelectionsKey,
    };
  },
});
</script>
