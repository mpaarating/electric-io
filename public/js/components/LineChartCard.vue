<template>
  <div :id="chartId" class="chart" :style="chartStyle"></div>
</template>

<script>
import Chartist from "chartist";
import { evaluatePath } from "../lib/messagePropertyEvaluation.js";

export default {
  name: "LineChartCard",

  props: {
    tile: {
      type: Object,
      required: true
    },
    blockSize: {
      type: Array,
      required: true
    },
    messages: {
      type: Array,
      required: true
    }
  },

  data() {
    return {
      chartData: {
        series: []
      },
      chart: null,
      lineColor: this.tile.lineColor,
      chartId: `chart-${this.tile.id}`
    };
  },

  computed: {
    chartStyle: function() {
      return {
        marginLeft: "-20px",
        width: `${this.blockSize[0] * this.tile.size[0]}px`,
        height: `${this.blockSize[1] * this.tile.size[1] - 70}px`,
        "--stroke-color": this.lineColor
      };
    }
  },

  watch: {
    messages: function() {
      const propPath = this.tile.property;
      const newData = this.messages
        .map(msg => ({
          t: new Date(msg.enqueuedTime),
          y: evaluatePath(propPath, msg)
        }))
        .filter(msg => msg.y)
        .splice(-20);

      this.chartData.series[0] = newData;
      this.chart.update(this.chartData);
    }
  },

  mounted() {
    this.chart = new Chartist.Line(`#${this.chartId}`, this.chartData);
  }
};
</script>

<style>
div.chart .ct-series-a .ct-line,
div.chart .ct-series-a .ct-point {
  stroke: var(--stroke-color);
}
</style>
