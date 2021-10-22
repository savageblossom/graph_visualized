<template>
  <div id="app">
    <cytoscape
      ref="cy"
      :config="config"
      v-on:mousedown="addNode"
      v-on:cxttapstart="updateNode"
      class="cy-container"
      :afterCreated="afterCreated"
    />

    <results-table :results="primResult" />

    <div class="bottom-panel">
      Made by Muradasiloff U.D. I-1-18
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import config from "./cyto-config";

import ResultsTable from './components/results-table.vue'

delete config.elements;

export default {
  components: { ResultsTable },
  data() {
    return {
      config,
      graphToDisplay: [],
      primResult: [],
      initialGraph: [
        [0, 9, 75, 0, 0],
        [9, 0, 95, 19, 42],
        [75, 95, 0, 51, 66],
        [0, 19, 51, 0, 31],
        [0, 42, 66, 31, 0],
      ]
    };
  },
  computed: {
    cyInstance() {
      return this.$refs.cy.instance;
    }
  },
  mounted () {
    this.initGraph();
    this.primAlgorithm();
    this.highlightResult();
  },
  methods: {
    highlightResult() {
      const idsToHighlight = this.primResult.map(edge => edge.id)

      this.cyInstance.filter(e => {
        return idsToHighlight.includes(e.data('id'))
      }).style({ "line-color": "green" })
      
      this.primResult.forEach(resultEl => {
        const idToRemove = resultEl.id.split('').reverse().join('')
        this.cyInstance.remove(`edge[id = '${idToRemove}']`)
      })
    },
    primAlgorithm() {
      let selected = this.initialGraph.map(_ => false)
      
      selected[0] = true

      let edge_count = 0
      let x, y

      while(edge_count < this.initialGraph.length - 1) {
        let min = Infinity
        x = 0;
        y = 0;

        this.initialGraph.forEach((_, i) => {

          if(selected[i]) {

            this.initialGraph.forEach((_, j) => {
              if(!selected[j] && this.initialGraph[i][j]) {
                if(min > this.initialGraph[i][j]) {
                  min = this.initialGraph[i][j]

                  x = i;
                  y = j;
                }
              }
            })
            
          }

        })
        this.primResult.push({
          id: `${x}${y}`,
          source: x,
          target: y,
          weight: this.initialGraph[x][y]
        })
        selected[y] = true;
        edge_count += 1
      }
    },
    initGraph() {
      const nodes = this.initialGraph.map((_, index) => {
        return {
          data: { id: index },
          position: { 
            x: 589 + index * 30 + Math.random()*1000, 
            y: 182 + index * 30 + Math.random()*200
          },
          group: "nodes"
        }
      })


      let edges = []

      const isEdgeAlreadyPresent = (edgeName) => edges.some(
        edge => edge.data.id === edgeName || edge.data.id === edgeName.split("").reverse().join("")
      )
      

      this.initialGraph.forEach((edge, edgeIndex) => {
        edge.forEach((relationWeight, weightIndex) => {
          const edgeName = `${edgeIndex}${weightIndex}`

          if(relationWeight !== 0) {
            edges.push({
              data: {
                id: edgeName,
                source: edgeIndex,
                target: weightIndex,
                weight: relationWeight
              },
              group: "edges"
            })
          }
        })
      })

      this.graphToDisplay = [...nodes, ...edges]

      this.cyInstance.add(this.graphToDisplay)
    },
    addNode(event) {
      // console.log(event.target, this.$refs.cyRef.instance);
      // if (event.target === this.$refs.cyRef.instance)
        // console.log("adding node", event.target);
    },
    deleteNode(id) {
      // console.log("node clicked", id);
    },
    updateNode(event) {
      // console.log("right click node", event);
    },
    preConfig(cytoscape) {
      // console.log("calling pre-config", cytoscape);
    },
    afterCreated(cy) {
      // this.cy = cy
      // console.log(cy)
    },
  }
};
</script>

<style>
.red {
  background: red;
}

.left-panel {
  position: absolute;
  left: 0;
  top: 0;
  width: 250px;
  padding: 10px 20px;
  box-sizing: border-box;
}

.cy-container > * {
  min-height: 97vh !important;
}

.bottom-panel {
  position: absolute;
  bottom: 0;
  background: black;
  color: white;
  width: 100%;
  left: 0;
  text-align: right;
  padding: 3px;
  box-sizing: border-box;
}

</style>