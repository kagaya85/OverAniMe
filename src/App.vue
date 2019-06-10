<template>
  <div id="app">
    <b-navbar toggleable="md" type="dark" class="navbar" sticky>
      <b-navbar-brand href="#">OverAniMe</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item href="#">About</b-nav-item>
        </b-navbar-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item-dropdown text="Lang" right>
            <b-dropdown-item-button @click="changeLocale($event)" value="en"
              >EN</b-dropdown-item-button
            >
            <b-dropdown-item-button @click="changeLocale($event)" value="ja"
              >JA</b-dropdown-item-button
            >
            <b-dropdown-item-button @click="changeLocale($event)" value="zh_cn"
              >ZH</b-dropdown-item-button
            >
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>

    <b-container fluid class="">
      <b-row class="main-row">
        <b-col md="3" class="col-margin">
          <b-form @submit="onSubmit" class="">
            <label for="input">{{ $t("message") }}</label>
            <b-form-group :description="$t('message')">
              <b-form-input
                id="input"
                required
                :placehloder="$t('message')"
              ></b-form-input>
            </b-form-group>
            <b-button type="submit" variant="primary">{{
              $t("message")
            }}</b-button>
          </b-form>

          <b-list-group class="reclist">
            <b-list-group-item v-for="(item, index) in recList" :key="index">{{
              item
            }}</b-list-group-item>
          </b-list-group>
        </b-col>

        <b-col md="9" class="col-margin">
          <div ref="graphContainer" class="graph-container">
            <KnowledgeGraph ref="graph"></KnowledgeGraph>
          </div>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import KnowledgeGraph from "./components/KnowledgeGraph.vue";

export default {
  name: "app",
  components: {
    KnowledgeGraph
  },
  data() {
    return {
      recList: [
        "Cras justo odio",
        "Dapibus ac facilisis in",
        "Morbi leo risus",
        "Porta ac consectetur ac",
        "Vestibulum at eros"
      ]
    };
  },
  mounted() {
    var that = this;
    if (document.body.clientWidth < window.innerHeight)
      // 移动端 长 == 宽
      this.$refs.graphContainer.style.height = `${this.$refs.graphContainer.clientWidth}px`;
    else if (window.innerHeight > 700)
      // PC 等于视窗高度 - navbar高度
      that.$refs.graphContainer.style.height = `${window.innerHeight - 100}px`;
    else that.$refs.graphContainer.style.height = `600px`;

    window.onresize = function resizeGraph() {
      if (document.body.clientWidth < window.innerHeight)
        that.$refs.graphContainer.style.height = `${that.$refs.graphContainer.clientWidth}px`;
      else if (window.innerHeight > 700)
        // PC 等于视窗高度 - navbar高度
        that.$refs.graphContainer.style.height = `${window.innerHeight - 100}px`;
      else that.$refs.graphContainer.style.height = `600px`;
    };

    this.$refs.graph.showGraph();
  },
  methods: {
    onSubmit: function() {},
    changeLocale: function(event) {
      this.$i18n.locale = event.currentTarget.value;
    }
  }
};
</script>

<style>
#app {
  background-color: #dddddd;
  min-height: 100vh;
}

.navbar {
  background-color: #333333;
}

.col-margin {
  margin-top: 20px;
}

.reclist {
  margin-top: 20px;
}

.graph-container {
  /* display: inline-block; */
  width: 100%;
  background-color: #555555;
}
</style>
