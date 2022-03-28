<template>
  <div>
    <h1>Hello!</h1>
  </div>
  <select v-model="selectedColumn">
    <option v-for="c in columns" :key="c" v-bind:value="c">{{ c }}</option>
  </select>
  <div/>
  <select v-model="selectedQueryType" id="query-types-select">
    <option v-for="qt in queryTypes" :key="qt" v-bind:value="qt">{{ qt }}</option>
  </select>
  <div v-show="findByColumnNameAndQueryType()"></div>
  <p v-if="result" v-html="result"></p>
  <p v-else-if="errorMsg">
    {{ errorMsg }}
  </p>
</template>

<script>
export default {
  name: 'HelloSuncode',
  mounted() {
    this.getColumnsWithQueryTypes();
  },
  data() {
    return {
      columns: [],
      queryTypes: [],
      selectedColumn: null,
      selectedQueryType: null,
      errorMsg: null,
      result: null
    }
  },
  methods: {
    getColumnsWithQueryTypes() {
      this.axios.get('http://localhost:8081/api/test-table/columns-with-query-types').then((response) => {
        this.columns = response.data['columns'];
        this.queryTypes = response.data['queryTypes'];
        this.columns = [null].concat(this.columns);
        this.queryTypes = [null].concat(this.queryTypes);
      });
    },
    findByColumnNameAndQueryType() {
      let isColumnNameNotProvided = this.selectedColumn === undefined || this.selectedColumn == null;
      let isQueryTypeNotProvided = this.selectedQueryType === undefined || this.selectedQueryType == null;
      if (isColumnNameNotProvided || isQueryTypeNotProvided) {
        this.errorMsg = 'Please choose column name and query type :)';
        this.result = null;
        return;
      }
      this.errorMsg = null;
      const url = `http://localhost:8081/api/test-table/${this.selectedColumn}?query_type=${this.selectedQueryType}`;
      this.axios.get(url).then((response) => this.result = response.data);
    }
  }
};
</script>

<style>
#query-types-select {
  margin-top: 1%;
}
</style>
