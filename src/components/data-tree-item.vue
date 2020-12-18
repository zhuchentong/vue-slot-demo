<template>
  <div class="data-tree-item">
    <!-- #region 节点元素 -->
    <div class="node-container">
      <div class="expend-icon" @click="onExpend">
        <div v-if="node.children && node.children.length">
          {{ expend ? "v" : ">" }}
        </div>
      </div>

      <div class="label" @click="onSelect">
        <template-container
          v-if="nodeTemplate"
          :template="nodeTemplate"
          :data="node"
        ></template-container>
        <template v-else> {{ node.label }} </template>

        <div class="selection">
          <div v-if="selection">
            √
          </div>
        </div>
      </div>
    </div>
    <!-- endregion -->

    <!-- #region 子元素 -->
    <div class="children-container" v-show="expend">
      <data-tree-item
        v-for="item in node.children"
        :key="item.key"
        :node="item"
      ></data-tree-item>
    </div>
    <!-- endregion -->
  </div>
</template>

<script lang="ts">
import {
  ComponentInternalInstance,
  defineComponent,
  getCurrentInstance,
  inject,
  ref,
  watch,
} from "vue";
import { h } from "vue";
/**
 * 展开逻辑处理
 */
function expendSetup() {
  const expend = ref(false);
  const onExpend = () => {
    expend.value = !expend.value;
  };

  return {
    expend,
    onExpend,
  };
}

/**
 * 选中逻辑处理
 */
function selectionSetup(parentProps, node) {
  const updateSelection = inject("updateSelection") as any;
  const parentSelection = parentProps.selection;
  const canSelect = parentProps.canSelect || [];
  const selection = ref(!!parentSelection.find((x) => x.key === node.key));
  const selections = inject("selections") as any;
  const onSelect = () => {
    if (!canSelect(node)) {
      return;
    }

    selection.value = !selection.value;
    updateSelection(node, selection.value);
  };

  // 判断是否存在重复KEY
  watch(
    selections,
    () => {
      const target = !!selections.value.find((x) => x.key === node.key);

      if (target !== selection.value) {
        selection.value = target;
      }
    },
    {
      deep: true,
    }
  );

  return {
    selection,
    onSelect,
  };
}

/**
 * 获取父组件
 */
const getDataFormComponent = (
  component: ComponentInternalInstance | null
): ComponentInternalInstance | undefined => {
  if (component && component.type.name === "data-tree") {
    return component;
  }

  if (component && component.parent) {
    const parent = component.parent;
    return getDataFormComponent(parent);
  }
};

export default defineComponent({
  name: "data-tree-item",
  components: {
    TemplateContainer: {
      functional: true,
      props: {
        template: {
          type: Function,
        },
        data: {
          type: Object,
        },
      },
      render: (props, ctx) => h("div", [props.template(props.data)]),
    },
  },
  props: {
    node: {
      type: Object,
    },
  },
  setup(props) {
    // 获取根节点
    const dataTree = getDataFormComponent(getCurrentInstance());
    const parentProps = dataTree?.props;
    const parentSlots = dataTree?.slots;
    const nodeTemplate = parentSlots?.node as any;
    return {
      ...expendSetup(),
      ...selectionSetup(parentProps, props.node),
      parentSlots,
      nodeTemplate,
    };
  },
});
</script>

<style lang="less" scoped>
.node-container {
  position: relative;
  margin-left: 25px;
  // 节点周边距离设置
  padding: 3px;
  display: flex;
  flex-direction: row;
  justify-content: center;

  .expend-icon {
    position: absolute;
    left: -20px;
  }

  .label {
    width: 100%;
    padding-right: 10px;
    display: flex;
    flex-direction: row;
    align-items: center;

    .selection {
      padding: 0 10px;
    }
  }
}

.children-container {
  // 子元素列表距离左侧值
  margin-left: 20px;
}
</style>
