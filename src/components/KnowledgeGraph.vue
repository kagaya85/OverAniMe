<template>
  <div class="graph">
    <svg></svg>
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
      ],
      edges: [
        // 边集
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
      // 预处理
      this.preprocess(this.tripleList);
      if (this.nodes == null) return;

      console.log(this.nodes);
      console.log(this.edges);
      
      var svg = d3.select("svg");
      svg.selectAll("*").remove();
      var svgRect = d3
        .select("svg")
        .node()
        .getBoundingClientRect(); // 用于获取元素的计算宽高
      var width = svgRect.width;
      var height = svgRect.height;

      var g = svg.append("g");

      //设置一个color的颜色比例尺
      var colorScale = d3
        .scaleOrdinal()
        .domain(d3.range(this.nodes.length)) // 输入范围
        .range(d3.schemePaired); // 输出范围

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
        .attr("stroke-width", 1)
        .attr("stroke-opacity", 0.8);

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
        .attr("fill", function(d) {
          return colorScale(d.type);
        });
      //文字
      gs.append("text")
        .attr("x", -10)
        .attr("y", -20)
        .attr("dy", 5)
        .text(function(d) {
          return d.name;
        });

      //生成节点数据
      this.forceSimulation.nodes(this.nodes).on("tick", function() {
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

        gs.attr("transform", function(d, i) {
          return "translate(" + d.x + "," + d.y + ")";
        });
      });

      //生成边数据
      this.forceSimulation.force("link").links(this.edges);

      // 结点间斥力（负值）
      this.forceSimulation.force("charge").strength(-width * 8);

      //设置图形的中心位置
      this.forceSimulation
        .force("center")
        .x(width / 2)
        .y(height / 2);
    },
    /**
     * 数据预处理
     * @param {array} tripleList - 三元组集合
     */
    preprocess(tripleList) {
      if (tripleList == null) return;

      var hashTable = {};
      var rank = 1;
      // 清空结点与边数组
      this.nodes = [];
      this.edges = [];

      this.nodes.push({
        name: tripleList[0].from,
        type: 0
      });

      tripleList.forEach((item, index) => {
        let typenum;
        // 判断是否出现过该属性
        if (!hashTable.hasOwnProperty(item.relation)) {
          hashTable[item.relation] = rank;
          rank++;
        }
        // 添加结点
        this.nodes.push({
          name: item.to,
          type: hashTable[item.relation]
        });
        // 添加边
        this.edges.push({
          source: 0,
          target: index + 1,
          relation: item.relation
        });
      });
    },
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
</style>
