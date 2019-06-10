<template>
  <div id="app">
    <b-navbar toggleable="md" type="dark" class="navbar">
      <b-navbar-brand href="#">OverAniMe</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item href="#">About</b-nav-item>
        </b-navbar-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item-dropdown text="Lang" right>
            <b-dropdown-item href="#">EN</b-dropdown-item>
            <b-dropdown-item href="#">JA</b-dropdown-item>
            <b-dropdown-item href="#">ZH</b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>

    <b-container fluid class="">
      <b-row class="main-row">
        <b-col md="4" class="col-margin">
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

        <b-col md="8" class="col-margin">
          <div ref="graph" class="graph"></div>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
export default {
  name: "app",
  components: {},
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
    var that = this
    if(document.body.clientWidth < 690) // 移动端 长 == 宽
      this.$refs.graph.style.height = `${this.$refs.graph.clientWidth}px`
    else  // PC 等于视窗高度 - navbar高度
      this.$refs.graph.style.height = `${document.body.clientHeight - 100}px`

    window.onresize = function resizeGraph() {
      if(document.body.clientWidth < 690)
        that.$refs.graph.style.height = `${that.$refs.graph.clientWidth}px`
      else  // PC 等于视窗高度 - navbar高度
        that.$refs.graph.style.height = `${document.body.clientHeight - 100}px`
    };
  },
  methods: {
    onSubmit: function() {}
  }
};
</script>

<style>
#app {
  position: relative;
  height: 100vh;
  background-color: #dddddd;
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

.graph {
  display: inline-block;
  width: 100%;
  background-color: #555555;
}
</style>
