<template>
  <div id="app">
    <b-navbar toggleable="md" type="dark" class="navbar" sticky>
      <b-navbar-brand href="#">OverAniMe</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item v-b-modal.modal-1>About</b-nav-item>
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
          <b-form @submit.prevent="onSubmit" class="">
            <label for="input">{{ $t("message") }}</label>
            <b-form-group>
              <b-form-input
                id="input"
                required
                :placeholder="$t('placeholder')"
              ></b-form-input>
            </b-form-group>
            <b-row align-h="end">
              <b-col cols="auto">
                <b-button type="submit" variant="primary">{{
                  $t("search")
                }}</b-button>
              </b-col>
            </b-row>
          </b-form>

          <b-list-group class="reclist">
            <b-list-group-item href="#" v-for="(item, index) in recList" :key="index" @click="onSubmit">{{
              item
            }}</b-list-group-item>
          </b-list-group>
        </b-col>

        <b-col md="9" class="col-margin">
          <div ref="graphContainer" class="graph-container">
            <KnowledgeGraph
              ref="graph"
              :triple-list="tripleList"
            ></KnowledgeGraph>
          </div>
        </b-col>
      </b-row>
    </b-container>

    <b-modal id="modal-1" centered :title="$t('about')">
      <p class="my-4">{{$t('content')}}</p>
    </b-modal>
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
        "New Game！！！",
        "请问你要来点兔子吗？",
        "假面骑士build",
        "芳文社",
        "Daring in the franxx"
      ],
      tripleList: [
        { from: "请问您今天要来点兔子吗？", relation: "主角", to: "香风智乃" },
        { from: "请问您今天要来点兔子吗？", relation: "主角", to: "保登心爱" },
        { from: "请问您今天要来点兔子吗？", relation: "配角", to: "提比" },
        { from: "请问您今天要来点兔子吗？", relation: "出版社", to: "芳文社" },
        { from: "请问您今天要来点兔子吗？", relation: "标签", to: "百合" },
        { from: "请问您今天要来点兔子吗？", relation: "标签", to: "日常" },
        {
          from: "请问您今天要来点兔子吗？",
          relation: "声优",
          to: "水瀬いのり"
        },
        { from: "请问您今天要来点兔子吗？", relation: "声优", to: "佐倉綾音" },
        {
          from: "请问您今天要来点兔子吗？",
          relation: "动画制作",
          to: "WHITE FOX"
        }
      ]
    };
  },
  mounted() {
    var that = this;
    if (document.body.clientWidth < window.innerHeight)
      // 移动端 长 == 宽
      this.$refs.graphContainer.style.height = `${
        this.$refs.graphContainer.clientWidth
      }px`;
    else if (window.innerHeight > 700)
      // PC 等于视窗高度 - navbar高度
      that.$refs.graphContainer.style.height = `${window.innerHeight - 100}px`;
    else that.$refs.graphContainer.style.height = `600px`;

    window.onresize = function resizeGraph() {
      if (document.body.clientWidth < window.innerHeight)
        that.$refs.graphContainer.style.height = `${
          that.$refs.graphContainer.clientWidth
        }px`;
      else if (window.innerHeight > 700)
        // PC 等于视窗高度 - navbar高度
        that.$refs.graphContainer.style.height = `${window.innerHeight -
          100}px`;
      else that.$refs.graphContainer.style.height = `600px`;
    };

    this.$refs.graph.showGraph();
  },
  methods: {
    onSubmit: function() {
      this.$refs.graph.showGraph();
    },
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
  /* background-color: #555555; */
}
</style>
