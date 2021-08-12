<template>
    <trading-vue :data="chart" :width="this.width" :height="this.height"
                 :chart-config="{customLegend: (values) => [{text: 'Value ', value: values[1].toFixed(2)}]}"
                 :color-back="colors.colorBack"
                 :color-grid="colors.colorGrid"
                 :legend-buttons="['display']"
                 offmain
                 @legend-button-click="on_button_click"
                 :color-text="colors.colorText">
    </trading-vue>
</template>

<script>
import TradingVue from './TradingVue.vue'
import Data from '../data/data.json'
import DataCube from '../src/helpers/datacube.js'

export default {
    name: 'app',
    components: {
        TradingVue
    },
    methods: {
        on_button_click(event) {
            if (event.button === 'display') {
                let d = event.type === 'chart' ? this.chart.data[event.type] : this.chart.data[event.type][event.dataIndex]
                if (d) {
                    if (!('display' in d.settings)) {
                        this.$set(
                            d.settings, 'display', true
                        )
                    }
                    this.$set(
                        d.settings, 'display', !d.settings.display
                    )
                }
            }
        },
        onResize() {
            this.width = window.innerWidth
            this.height = window.innerHeight
        }
    },
    mounted() {
        let data = Data
        data.chart.data = data.chart.data.map(obj => [obj[0], obj[1]])
        data.onchart[0].data = data.chart.data.map(obj => [obj[0], obj[1] + 50])
        data.onchart[1].data = data.chart.data.map(obj => [obj[0], obj[1] + 1550])
        this.chart = new DataCube(data)

        window.addEventListener('resize', this.onResize)
        window.dc = this.chart
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.onResize)
    },
    data() {
        return {
            chart: {},
            width: window.innerWidth,
            height: window.innerHeight,
            colors: {
                colorBack: '#fff',
                colorGrid: '#eee',
                colorText: '#333',
            }
        };
    },
};
</script>

<style>
html,
body {
    background-color: #000;
    margin: 0;
    padding: 0;
    overflow: hidden;
}
</style>
