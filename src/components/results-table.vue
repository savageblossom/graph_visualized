<template>
    <div class="left-panel">
      <h1>Results</h1>
      <sui-table basic="very" celled collapsing>
        <sui-table-header>
          <sui-table-row>
            <sui-table-header-cell>Edge</sui-table-header-cell>
            <sui-table-header-cell>Weight</sui-table-header-cell>
          </sui-table-row>
        </sui-table-header>

        <sui-table-body>
          <sui-table-row
            v-for="result in results"
            :key="result.id"
          >
            <sui-table-cell>
              {{ result.id }}
            </sui-table-cell>
            <sui-table-cell>
              {{ result.weight }}
            </sui-table-cell>
          </sui-table-row>
        </sui-table-body>
      </sui-table>
    </div>
</template>

<script>
export default {
  name: "results-table",
  props: ['results']
}
</script>

<style>

</style>