<template>
  <div id="app"  style="margin: 0 auto 20px; width: 800px;">
    <div style="margin: 20px 0;">
      <el-button
        size="small"
        @click="addTab()"
      >+</el-button>
    </div>
    <el-tabs type="card" closable @tab-remove="removeTab()">
      <el-tab-pane
        v-for="(item) in editableTabs"
        :key="item.name"
        :label="item.title"
      ><h2>{{item.title}}</h2>
        <Options />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import Options from './components/Options.vue'

export default {
  name: 'app',
  components: {
    Options
  },
  data() {
    return {
        editableTabs: [{
          title: 'Mortgage 1',
          i: 1
        }]
    }
  },
  computed: {
    tabCount() {
      return this.editableTabs.length;
    }
  },
  methods: {
    addTab() {
      this.editableTabs.push({
        title: `Mortgage ${this.tabCount + 1}`,
        i: this.tabCount + 1
      })
    },
    removeTab() {
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

h2 {
  text-align: center;
  margin: 10px auto;
}
</style>
