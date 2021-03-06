<template>
  <div
    v-resize.debounce="onResize"
    ref="wrapper"
    :style="wrapperStyle"
    class="ev-chart"
  />
</template>
<script>
  import resize from 'vue-resize-directive';
  import { defaultsDeep, isEqual } from 'lodash-es';
  import { getQuantity } from '@/common/utils';
  import EvChart from './chart.core';

  export default {
    directives: {
      resize,
    },
    props: {
      options: {
        type: Object,
        default: () => {},
      },
      data: {
        type: Object,
        default: () => {},
      },
    },
    data() {
      return {
        chart: null,
        normalizedOption: null,
        normalizedData: null,
      };
    },
    computed: {
      wrapperStyle() {
        return {
          width: this.getChartSize(getQuantity(this.options.width)),
          height: this.getChartSize(getQuantity(this.options.height)),
        };
      },
    },
    watch: {
      data: {
        handler(newVal) {
          const newData = defaultsDeep({}, newVal, this.normalizedData);
          const updatedSeries = !isEqual(newData.series, this.evChart.data.series);

          this.evChart.data = newData;
          this.evChart.update(updatedSeries);
        },
        deep: true,
      },
      options: {
        handler(newVal) {
          const newOpt = defaultsDeep({}, newVal, this.normalizedOption);
          const updatedSeries = !isEqual(newOpt.legend, this.evChart.options.legend);

          this.evChart.options = newOpt;
          this.evChart.update(updatedSeries);
        },
        deep: true,
      },
    },
    created() {
      this.normalizedOption = defaultsDeep({}, this.options, this.getDefaultOptions());
      this.normalizedData = defaultsDeep(this.data, this.getDefaultData());
    },
    mounted() {
      const wrapper = this.$refs.wrapper;
      const options = this.normalizedOption;
      const data = this.normalizedData;

      this.evChart = new EvChart(wrapper, data, options);

      const timer = setTimeout(() => {
        this.evChart.init();
        clearTimeout(timer);
      }, 1);
    },
    beforeDestroy() {
      this.evChart.destroy();

      delete this.evChart;
    },
    methods: {
      getDefaultOptions() {
        return {
          border: 2,
          title: {
            show: false,
            height: 40,
            text: '',
            style: {
              fontSize: 15,
              color: '#000',
              fontFamily: 'Roboto',
            },
          },
          legend: {
            show: true,
            position: 'right',
            color: '#353740',
            inactive: '#aaa',
            width: 140,
            height: 24,
          },
          itemHighlight: true,
          seriesHighlight: true,
          useSelect: false,
          doughnutHoleSize: 0,
          reverse: false,
          bufferSize: null,
          horizontal: false,
          width: '100%',
          height: '100%',
          thickness: 1,
          useTooltip: true,
          indicator: {
            use: true,
            color: '#EE7F44',
          },
          maxTip: {
            use: true,
            background: '#FFFFFF',
            color: '#000000',
          },
        };
      },
      getDefaultData() {
        return {
          series: {},
          groups: [],
          labels: [],
          data: {},
        };
      },
      getChartSize(size) {
        let sizeValue;

        if (size) {
          sizeValue = size.unit ? size.value + size.unit : `${size.value}px`;
        } else {
          sizeValue = undefined;
        }
        return sizeValue;
      },
      onResize() {
        this.evChart.resize();
      },
    },
  };
</script>
<style lang="scss">
  .ev-chart-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
    display: block;
  }

  .ev-chart-container {
    position: relative;
    overflow: hidden;
    width: 100%;
    height: 100%;
  }

  .ev-chart-title {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    padding-left: 10px;
    word-wrap: normal;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    user-select: none;
  }

  .ev-chart-legend {
    position: absolute;
    overflow: hidden;
  }

  .ev-chart-legend-box {
    overflow-x: hidden;
    overflow-y: auto;
  }

  .ev-chart-legend-container {
    position: relative;
    overflow: hidden;
    font-family: 'Roboto', 'sans-serif';
  }

  .ev-chart-legend-color {
    top: 50%;
    left: 0;
    transform: translate(0, -50%);
    width: 18px;
    height: 4px;
    position: absolute;
  }

  .ev-chart-legend-name {
    text-align: left;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    font-size: 12px;
    margin-left: 24px;
    padding-right: 16px;
    user-select: none;
    top: 50%;
    left: 0;
    width: 100%;
    transform: translate(0, -50%);
    position: absolute;

    &:hover {
      font-weight: bold;
    }
  }

  .ev-chart-legend-value {
    float: right;
    text-align: left;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
  }

  .ev-chart-resize-bar {
    position: absolute;
    background: transparent;
    opacity: 0.5;
    z-index: 10;
  }

  .ev-chart-resize-bar:hover {
    background-color: #E2E2E2;
  }

  .ev-chart-resize-ghost {
    position: absolute;
    width: 4px;
    height: 100%;
    cursor: col-resize;
    opacity: 0.5;
    background-color: #E2E2E2;
  }

  .ev-chart-resize-ghost.horizontal {
    width: 100%;
    height: 4px;
    cursor: row-resize;
  }

  .ev-chart-tooltip {
    position: absolute;
    z-index: 850;
    overflow-y: auto;
    overflow-x: hidden;
    padding-right: 17px;
  }

  .ev-chart-tooltip-canvas {
    position: absolute;
    top: 0;
    left: 0;
    display: block;
  }
</style>
