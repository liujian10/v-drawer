<template>
  <div :class="warpCls" v-bind="$attrs">
    <div
        :class="`${prefixCls}-sidebar`"
        :style="sbStyle"
        ref="sidebar">
      <slot name="sidebar"></slot>
    </div>
    <div
        :class="`${prefixCls}-overlay`"
        :style="olStyle"
        @click="overlayClick"
        ref="overlay"></div>
    <div
        :class="`${prefixCls}-content`"
        :style="ctStyle"
        ref="content">
      <slot></slot>
    </div>
  </div>
</template>
<script>
  const prefixCls = 'vm-drawer';
  // 侧边栏位置列表
  const posList = ['top', 'bottom', 'right', 'left'];
  export default {
    name: 'VDrawer',
    props: {
      // 侧边栏初始展开状态
      open: {
        type: Boolean,
        default: false
      },
      // 是否嵌入到正常文档流里
      docked: Boolean,
      // 是否禁止背景点击
      forbidClick: {
        type: Boolean,
        default: false
      },
      // 侧边栏展示位置
      position: {
        type: String,
        default: "left",
        validator: function (value) {
          return posList.indexOf(value) !== -1;
        }
      },
      // 侧边栏自定义样式
      sidebarStyle: {
        type: Object,
        default: function () {
          return {}
        }
      },
      // 遮罩层自定义样式
      overlayStyle: {
        type: Object,
        default: function () {
          return {}
        }
      },
      // 侧边栏内容自定义样式
      contentStyle: {
        type: Object,
        default: function () {
          return {}
        }
      }
    },
    data() {
      return {
        prefixCls: prefixCls, // 类名前缀
        sidebarWidth: 0, // 侧边栏宽度
        sidebarHeight: 0 // 侧边栏高度
      }
    },
    mounted() {
      this.$nextTick(function () {
        this.sidebarWidth = this.$refs.sidebar.clientWidth;
        this.sidebarHeight = this.$refs.sidebar.clientHeight;
      })
    },
    computed: {
      isPositionY() {
        return this.position === posList[0] || this.position === posList[1];
      },
      // 元素类集合
      warpCls() {
        return {
          [prefixCls]: true,
          [`${prefixCls}-${this.position}`]: true
        }
      },
      // 侧边栏样式
      sbStyle() {
        const resStyle = this.sidebarStyle;
        let transform = "";
        if (this.open) {
          if (this.isPositionY) {
            transform = 'translateY(0%)';
          } else {
            transform = 'translateX(0%)';
          }
        }
        resStyle.transform = transform;
        resStyle.visibility = this.open ? 'visible' : 'hidden';
        return resStyle;
      },
      // 遮罩层样式
      olStyle() {
        const resStyle = this.overlayStyle;
        resStyle.opacity = this.open ? 1 : 0;
        resStyle.visibility = this.open ? 'visible' : 'hidden';
        return resStyle;
      },
      // 侧边栏内容样式
      ctStyle() {
        const resStyle = this.contentStyle;
        let transform = "";
        if (this.open && this.docked) {
          switch (this.position) {
            case posList[0]: // up
              transform = `translateY(${this.sidebarHeight}px)`;
              break;
            case posList[1]: // down
              transform = `translateY(-${this.sidebarHeight}px)`;
              break;
            case posList[2]: // right
              transform = `translateX(-${this.sidebarWidth}px)`;
              break;
            case posList[3]: // left
            default:
              transform = `translateX(${this.sidebarWidth}px)`;
          }
        } else {
          if (this.isPositionY) {
            transform = 'translateY(0)';
          } else {
            transform = 'translateX(0)';
          }
        }
        resStyle.transform = transform;
        return resStyle;
      }
    },
    methods: {
      // 遮罩层点击事件
      overlayClick() {
        if (this.open && !this.forbidClick) {
          this.$emit('open-change', false, {
            overlayClick: true
          })
        }
      }
    }
  }
</script>
<style lang="less">
  @drawerPrefixCls: vm-drawer;
  .@{drawerPrefixCls} {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    overflow: hidden;

    &-sidebar {
      z-index: 2;
      position: absolute;
      transition: transform ease-in-out 0.3s, visibility 0.3s;
      will-change: transform;
      overflow-y: auto;
    }
    &-draghandle {
      z-index: 1;
      position: absolute;
      background-color: rgba(50, 50, 50, 0.1);
    }
    &-overlay {
      z-index: 1;
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      opacity: 0;
      visibility: hidden;
      transition: opacity 0.3s ease-out;
      background-color: rgba(0, 0, 0, 0.3);
    }
    &-content {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      overflow: auto;
      transition: transform ease-in-out 0.3s, visibility 0.3s;
    }

    &&-left,
    &&-right {
      .@{drawerPrefixCls}-sidebar,
      .@{drawerPrefixCls}-draghandle {
        top: 0;
        bottom: 0;
      }
      .@{drawerPrefixCls}-draghandle {
        width: 20px;
        height: 100%;
      }
    }
    &&-top,
    &&-bottom {
      .@{drawerPrefixCls}-sidebar,
      .@{drawerPrefixCls}-draghandle {
        left: 0;
        right: 0;
      }
      .@{drawerPrefixCls}-draghandle {
        width: 100%;
        height: 20px;
      }
    }
    &&-left {
      .@{drawerPrefixCls}-sidebar {
        left: 0;
        transform: translateX(-100%);
        .@{drawerPrefixCls}-open& {
          box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.15);
        }
      }
      .@{drawerPrefixCls}-draghandle {
        left: 0;
      }
    }

    &&-right {
      .@{drawerPrefixCls}-sidebar {
        right: 0;
        transform: translateX(100%);
        .@{drawerPrefixCls}-open& {
          box-shadow: -2px 2px 4px rgba(0, 0, 0, 0.15);
        }
      }
      .@{drawerPrefixCls}-draghandle {
        right: 0;
      }
    }

    &&-top {
      .@{drawerPrefixCls}-sidebar {
        top: 0;
        transform: translateY(-100%);
        .@{drawerPrefixCls}-open& {
          box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.15);
        }
      }
      .@{drawerPrefixCls}-draghandle {
        top: 0;
      }
    }

    &&-bottom {
      .@{drawerPrefixCls}-sidebar {
        bottom: 0;
        transform: translateY(100%);
        .@{drawerPrefixCls}-open& {
          box-shadow: 2px -2px 4px rgba(0, 0, 0, 0.15);
        }
      }
      .@{drawerPrefixCls}-draghandle {
        bottom: 0;
      }
    }
  }

</style>
