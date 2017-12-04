<template>
  <div :class="warpCls" v-bind="$attrs">
    <div
        :class="`${prefixCls}-sidebar`"
        :style="sidebarStyle"
        ref="sidebar">
      <slot name="sidebar"></slot>
    </div>
    <div
        :class="`${prefixCls}-overlay`"
        :style="overlayStyle"
        ref="overlay"></div>
    <div
        :class="`${prefixCls}-content`"
        :style="contentStyle"
        ref="content">
      <div
          :class="`${prefixCls}-draghandle`"
          ref="dragHandle"
          @touchStart=""
          @touchMove=""
          @touchEnd=""
          @touchCancel=""
      ></div>
      <slot></slot>
    </div>
  </div>
</template>
<script>
  import Vue from 'vue';
  import VueTouch from 'vue-touch';

  Vue.use(VueTouch);
  const prefixCls = 'vm-drawer';
  const posList = ['up', 'down', 'right', 'left'];
  export default {
    name: 'VDrawer',
    props: {
      open: {
        type: Boolean,
        default: false
      },
      position: {
        type: String,
        default: "left",
        validator: function (value) {
          return posList.indexOf(value) !== -1;
        }
      },
      sidebarStyle: [String, Object],
      overlayStyle: [String, Object],
      contentStyle: [String, Object]
    },
    data() {
      return {
        isOpen: this.open,
        prefixCls: prefixCls
      }
    },
    watch: {
      // 监听父级作用域 open 值的变化
      open: function (val) {
        this.isOpen = val;
      }
    },
    computed: {
      // 元素类集合
      warpCls() {
        return {
          [prefixCls]: true,
          [`${prefixCls}-${this.position}`]: true
        }
      }
    },
    methods: {
      // 遮罩层点击事件
      overlayClick() {
        if (this.isOpen) {
          setTimeout(function () {
            this.$emit('open-change', false, {
              overlayClick: true
            });
          }, 0)
        }
      }
    }
  }
</script>
