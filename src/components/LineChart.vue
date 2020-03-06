<template>
<div>
  <h3 class="text">Schedule Analysis</h3>
  <svg width="100%" height="100%" viewBox="0 0 800 330"
  preserveAspectRatio="xMidYMid meet" >
    
    <g class='lineChart' v-bind:transform="translate">
      <axis class='yA' v-bind:scales="getScales().yAxis" v-bind:chartDefaults='chartDefaults' v-bind:data='data' v-bind:transformations='trnsY'/>
      <axis class='xA' v-bind:scales="getScales().xAxis" v-bind:chartDefaults='chartDefaults' v-bind:data='data' v-bind:transformations='trnsX()'/>
      <axis class='grid' v-bind:scales="getScales().yGrid" v-bind:chartDefaults='chartDefaults' v-bind:data='data' v-bind:transformations='trnsY' v-bind:style="{opacity: chartDefaults.gridOpacity}"/>
    <path class='line' :d="line" />
    </g>
      
  </svg>

  <p class='text' > {{chartDefaults.title}}</p>
</div>
</template>

<script>
import * as d3 from "d3";
import ChartAxis from "./ChartAxis.vue";
export default {
  name: "LineChart",
  components: {
    axis: ChartAxis // Using reusable component to draw x,y axis and Grid.
  },
  data() {
    // TODO - this needs to be broken down based on the activity time periods set by the user
    //! - data.hour should stay as a single integer or string with one number
    return {
      data: [
        {
          hour: 8,
          performance: 0
        },
        {
          hour: 9,
          performance: 55
        },
        {
          hour: 10,
          performance: 85
        },
        {
          hour: 11,
          performance: 75
        },
        {
          hour: 12,
          performance: 60
        },
        {
          hour: 13,
          performance: 45
        },
        {
          hour: 14,
          performance: 25
        },
        {
          hour: 15,
          performance: 15
        },
        {
          hour: 16,
          performance: 30
        },
        {
          hour: 17,
          performance: 40
        },
        {
          hour: 18,
          performance: 45
        },
        {
          hour: 19,
          performance: 55
        },
        {
          hour: 20,
          performance: 55
        },
        {
          hour: 21,
          performance: 45
        },
        {
          hour: 22,
          performance: 0
        },
      ],
      chartDefaults: {
        width: 800,
        height: 300,
        chartId: "linechart-vue",
        title: "Strain Duration",
        margin: {
          top: 5,
          right: 5,
          bottom: 15,
          left: 50
        },
        gridOpacity: 1,
        data: []
      },
      line: "",
      //Translate co-ords for chart, x axis and yaxis. This is injected into template
      translate: "translate(" + 50 + "," + 5 + ")",
      trnsY: "translate(0,0)",
      trnsX: this.getTrnsx,
      toggleClass: true
    };
  },
  mounted() {
    // var scale = {};
    //Kick off drawing chart once component is mounted
    this.calculatePath();
  },
  methods: {
    getScales() {
      // All the maths to work chart coordinates and working out Axis
      var parseTime = d3.timeParse("%I");

      this.data.forEach(function(d) {
        d.time = parseTime(d.hour);
      });

      const x = d3
        .scaleTime()
        .domain(
          d3.extent(this.data, function(d) {
            return d.time;
          })
        )
        .rangeRound([0, this.chartDefaults.width - 100]);
      const y = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(this.data, function(d) {
            return d.performance + 50;
          })
        ])
        .range([this.chartDefaults.height, 0]);
      d3.axisBottom().scale(x);
      d3.axisLeft().scale(y);

      //Key funtions to draw X-axis,Y-axis and the grid. All uses component axis
      //play around with time format to get it to display as you want : d3.timeFormat("%b-%d")
      var xAxis = d3
        .axisBottom()
        .scale(x)
        .tickFormat(d3.timeFormat("%-I %p"))
        .tickValues(
          this.data
            .map(function(d, i) {
              if (i > 0) {
                return d.time;
              }
              return false;
            })
            .splice(1)
        )
        .ticks(4);

      var yAxis = d3
        .axisLeft()
        .scale(y)
        .ticks(5);
      var yGrid = d3
        .axisLeft()
        .scale(y)
        .tickSize(-(this.chartDefaults.width - 100), 0, 0)
        .tickFormat("");
      // Return the key calculations and functions to draw the chart
      return {
        x,
        y,
        xAxis,
        yAxis,
        yGrid
      };
    },
    getTrnsx() {
      //works out translate value in relative to dynamic height
      const t = "translate(0," + this.chartDefaults.height + ")";
      console.log(t)
      return t;
    },
    calculatePath() {
      //Get key calculation funtions to draw chart , Ie scale, axis mapping and plotting
      const scale = this.getScales();
      // Define calcultion to draw chart
      const path = d3
        .line()
        .x(d => {
          return scale.x(d.time);
        })
        .y(d => {
          return scale.y(d.performance);
        })
        .curve(d3.curveCardinal);

      // draw line then this.line is injected into the template
      this.line = path(this.data);
    }
  }
};
</script>
<!-- css loaderhttps://vue-loader.vuejs.org/guide/scoped-css.html#mixing-local-and-global-styles -->
<style>
text {
  color: #fff;
}

path.line {
  fill: none;
  stroke: rgb(0, 192, 222);
  stroke-width: 3px;
}

.grid line {
  opacity: 0.05;
}
.xA line {
  opacity: 0.5;
}

/*Some fancy animation to draw chart*/
svg .lineChart > path {
  stroke: rgb(0, 192, 222);
  stroke-width: 3;
  stroke-dasharray: 4813.713;
  stroke-dashoffset: 4813.713;
  -webkit-animation-name: draw2;
  animation-name: draw2;
  -webkit-animation-duration: 10s;
  animation-duration: 10s;
  -webkit-animation-fill-mode: forwards;
  animation-fill-mode: forwards;
  -webkit-animation-iteration-count: 1;
  animation-iteration-count: 1;
  -webkit-animation-timing-function: linear;
  animation-timing-function: linear;
}

.ani2 svg .lineChart > path {
  stroke: rgb(0, 192, 222);
  -webkit-animation-name: draw-2;
  animation-name: draw-2;
}
.ani1 svg .lineChart > path {
  stroke: rgb(0, 192, 222);
  -webkit-animation-name: draw;
  animation-name: draw;
}
#Layer_1 {
  width: 100%;
}
@-webkit-keyframes draw {
  85% {
  }
  100% {
    stroke-dashoffset: 0;
  }
}
@keyframes draw {
  85% {
  }
  100% {
    stroke-dashoffset: 0;
  }
}

@-webkit-keyframes draw-2 {
  85% {
  }
  100% {
    stroke-dashoffset: 0;
  }
}

@keyframes draw-2 {
  85% {
  }
  100% {
    stroke-dashoffset: 0;
  }
}
.text {
  display: inline-block;
  font-size: 3vw;
  margin: 0.5vw 0 1.5vw;
}

svg {
  background-color: rgb(85, 87, 89);
}
</style>
