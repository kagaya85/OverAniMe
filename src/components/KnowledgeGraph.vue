<template>
  <div class="graph">
    <svg>
    </svg>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "KnowledgeGraph",
  props: {
    tripleList: Array
  },
  data() {
    return {
      nodes: [
        // 节点
        { name: "请问您今天要来点兔子吗？" },
        { name: "香风智乃" },
        { name: "保登心爱" },
        { name: "提比" },
        { name: "芳文社" },
        { name: "百合" },
        { name: "日常" },
        { name: "水瀬いのり" },
        { name: "佐倉綾音" },
        { name: "WHITE FOX" }
      ],
      edges: [
        // 边集
        { source: 0, target: 1, relation: "主角" },
        { source: 0, target: 2, relation: "主角" },
        { source: 0, target: 3, relation: "配角" },
        { source: 0, target: 4, relation: "出版社" },
        { source: 0, target: 5, relation: "标签" },
        { source: 0, target: 6, relation: "标签" },
        { source: 0, target: 7, relation: "声优" },
        { source: 0, target: 8, relation: "声优" },
        { source: 0, target: 9, relation: "动画制作" }
      ],
      forceSimulation: null
    };
  },
  mounted() {
      // 新建力导向图
      this.forceSimulation = d3
        .forceSimulation()
        .force("link", d3.forceLink())
        .force("charge", d3.forceManyBody())
        .force("center", d3.forceCenter());
  },
  methods: {
    /**
     * 对外提供重绘知识图谱的接口
     */

    showGraph: function() {
      // if (this.tripleList == null) return;
      var marge = {top:60,bottom:60,left:60,right:60}
      var svg = d3.select("svg");
      var g = d3.select("g");
      var width = svg.attr("width");
      var height = svg.attr("height");
    	var g = svg.append("g")
    		.attr("transform","translate("+marge.top+","+marge.left+")");
      //设置一个color的颜色比例尺，为了让不同的扇形呈现不同的颜色
      var colorScale = d3
        .scaleOrdinal()
        .domain(d3.range(this.nodes.length))
        .range(d3.schemeCategory10);


      //生成节点数据
      this.forceSimulation.nodes(this.nodes).on("tick", this.ticked);

      //生成边数据
      this.forceSimulation
        .force("link")
        .links(this.edges)
        .distance(function(d) {
          //每一边的长度
          return 500;
        });

      //设置图形的中心位置
      this.forceSimulation
        .force("center")
        .x(width / 2)
        .y(height / 2);

      //绘制边
      var links = g
        .append("g")
        .selectAll("line")
        .data(this.edges)
        .enter()
        .append("line")
        .attr("stroke", function(d, i) {
          return colorScale(i);
        })
        .attr("stroke-width", 1);

      // 边的文字
      var linksText = g
        .append("g")
        .selectAll("text")
        .data(this.edges)
        .enter()
        .append("text")
        .text(function(d) {
          return d.relation;
        });

      var gs = g
        .selectAll(".circleText")
        .data(this.nodes)
        .enter()
        .append("g")
        .attr("transform", function(d, i) {
          var cirX = d.x;
          var cirY = d.y;
          return "translate(" + cirX + "," + cirY + ")";
        })
        .call(
          d3
            .drag()
            .on("start", this.started)
            .on("drag", this.dragged)
            .on("end", this.ended)
        );
      //绘制节点
      gs.append("circle")
        .attr("r", 10)
        .attr("fill", function(d, i) {
          return colorScale(i);
        });
      //文字
      gs.append("text")
        .attr("x", -10)
        .attr("y", -20)
        .attr("dy", 10)
        .text(function(d) {
          return d.name;
        });

      function ticked() {
        links
          .attr("x1", function(d) {
            return d.source.x;
          })
          .attr("y1", function(d) {
            return d.source.y;
          })
          .attr("x2", function(d) {
            return d.target.x;
          })
          .attr("y2", function(d) {
            return d.target.y;
          });

        linksText
          .attr("x", function(d) {
            return (d.source.x + d.target.x) / 2;
          })
          .attr("y", function(d) {
            return (d.source.y + d.target.y) / 2;
          });

        gs.attr("transform", function(d) {
          return "translate(" + d.x + "," + d.y + ")";
        });
      }
    },
    /**
     * 数据预处理
     */
    dataPreprocess() {},
    started: function(d) {
      if (!d3.event.active) {
        this.forceSimulation.alphaTarget(0.8).restart();
      }
      d.fx = d.x;
      d.fy = d.y;
    },
    dragged: function(d) {
      d.fx = d3.event.x;
      d.fy = d3.event.y;
    },
    ended: function(d) {
      if (!d3.event.active) {
        this.forceSimulation.alphaTarget(0);
      }
      d.fx = null;
      d.fy = null;
    }
  }
};
</script>

<style scoped>
.graph {
  width: 100%;
  height: 100%;
}

svg {
  width: 100%;
  height: 100%;
}

g {
  /* transform: translate(100, 100); */
}
</style>
