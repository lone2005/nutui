<template>
  <view :class="classes" :style="getStyle" @click="handleClick">
    <nut-icon class="nut-icon-loading" v-if="loading"></nut-icon>
    <nut-icon :class="icon" v-if="icon && !loading" :name="icon"></nut-icon>
    <view :class="{ text: icon || loading }" v-if="$slots.default"
      ><slot></slot
    ></view>
  </view>
</template>

<script lang="ts">
import { PropType, CSSProperties, toRefs, computed } from 'vue';
import { createComponent } from '@/utils/create';
const { componentName, create } = createComponent('button');

export type ButtonType =
  | 'default'
  | 'primary'
  | 'info'
  | 'success'
  | 'warning'
  | 'danger';
export type ButtonSize = 'large' | 'normal' | 'small';
export type ButtonShape = 'square' | 'round';

export default create({
  props: {
    color: String,
    shape: {
      type: String as PropType<ButtonShape>,
      default: 'round'
    },
    plain: {
      type: Boolean,
      default: false
    },
    loading: {
      type: Boolean,
      default: false
    },
    disabled: {
      type: Boolean,
      default: false
    },
    type: {
      type: String as PropType<ButtonType>,
      default: 'default'
    },
    size: {
      type: String as PropType<ButtonSize>,
      default: 'normal'
    },
    block: {
      type: Boolean,
      default: false
    },
    icon: {
      type: String,
      default: ''
    }
  },
  components: {},
  emits: ['click'],
  setup(props, { emit, slots }) {
    // setup内部只能访问这4个属性，值得注意的是props必须在上面声明才能在这里取到
    console.log('props', props, 'emit', emit, 'slots', slots);
    const {
      type,
      size,
      shape,
      disabled,
      loading,
      color,
      plain,
      block
    } = toRefs(props);
    // console.log("type", type, "size", size);

    const handleClick = (event: MouseEvent) => {
      if (!loading.value && !disabled.value) {
        emit('click', event);
      }
    };

    const classes = computed(() => {
      const prefixCls = componentName;
      return {
        [prefixCls]: true,
        [`${prefixCls}--${type.value}`]: type.value,
        [`${prefixCls}--${size.value}`]: size.value,
        [`${prefixCls}--${shape.value}`]: shape.value,
        [`${prefixCls}--plain`]: plain.value,
        [`${prefixCls}--block`]: block.value,
        [`${prefixCls}--disabled`]: disabled.value
      };
    });

    const getStyle = computed(() => {
      if (color?.value) {
        const style: CSSProperties = {};

        if (plain.value) {
          style.color = color.value;
          style.background = '#fff';
          if (!color.value?.includes('gradient')) {
            style.borderColor = color.value;
          }
        } else {
          style.color = '#fff';
          style.background = color.value;
        }

        return style;
      } else {
        return {};
      }
    });

    return {
      handleClick,
      disabled,
      classes,
      getStyle
    };
  }
});
</script>

<style lang="scss">
@import 'index.scss';
</style>
