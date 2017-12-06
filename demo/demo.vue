<template>
  <section class="demo-drawer">
    <div class="demo-drawer-main">
      <v-drawer
          :docked="docked"
          :position="posList[position]"
          :open="open"
          :content-style="contentStyle"
          :sidebar-style="sidebarStyle"
          @open-change="onOpenChange">
        <div class="drawer-content-main">
          <h1>Drawer - 抽屉</h1>
          <button @click="onOpenChange">Open: {{open}}</button>
          <button @click="onPositionChange">Position: {{posList[position]}}</button>
          <button @click="onDockedChange">Docked: {{docked}}</button>
          <p>Click upper button to change</p>
        </div>
        <ul slot="sidebar" class="drawer-sidebar-ul">
          <li v-for="n in 10" :key="n"
              @click="clickAlert(`list-item-${n}`)">
            {{`list-item-${n}`}}
          </li>
        </ul>
      </v-drawer>
    </div>
  </section>
</template>
<script>
  import Vue from 'vue';
  import VDrawer from '../index';

  Vue.component(VDrawer.name, VDrawer);

  export default {
    data() {
      return {
        drawerStyle: {
          'position': 'relative',
          'overflow': 'auto',
          '-webkit-overflow-scrolling': 'touch',
          'height': '400px'
        },
        contentStyle: {
          'text-align': 'center',
          'padding-top': '50px'
        },
        sidebarStyle: {
          'background-color': '#fff'
        },
        posList: ['left','top', 'right','bottom'],
        position: 0,
        docked: false,
        open: false
      }
    },
    methods: {
      onOpenChange() {
        this.open = !this.open;
      },
      onPositionChange() {
        this.open = false;
        this.position = ++this.position % 4;
        setTimeout(function (_this) {
          _this.onOpenChange();
        }, 300, this);
      },
      onDockedChange() {
        this.open = false;
        this.docked = !this.docked;
        setTimeout(function (_this) {
          _this.onOpenChange();
        }, 300, this);
      },
      clickAlert(info) {
        alert(info);
      }
    }
  }
</script>
<style lang="less">
  @btnColor: #108ee9;

  .drawer-content-main {
    button {
      padding: 5px 20px;
      background: #fff;
      outline: none;
      border: 1px solid @btnColor;
    }
  }

  .drawer-sidebar-ul {
    margin: 0;
    list-style: circle;
    & > li {
      padding: 8px 20px 8px 0;
      border-bottom: 1px solid #ddd;
    }
  }
</style>