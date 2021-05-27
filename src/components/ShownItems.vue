<template>
  <span>
    <span>Show</span>
    <select v-model="per_page" :class="options.dt__shown_items_class">
      <option v-for="si in shownItems" :key="si" :value="si">{{si}}</option>
    </select>
    <span>entries</span>
  </span>
</template>

<script>
export default {
  props: {
    shown_items: Array,
    perPage: Number || 10,
    options: Object
  },
  data(){
    return {
      per_page: this.perPage,
    }
  },
  computed: {
    shownItems(){
      return this.shown_items || []
    },
  },
  methods: {
    emitEntries(){
      this.$emit('perPage', this.per_page)
    }
  },
  updated() {
    this.emitEntries()
  },
  created() {
    this.emitEntries()
  },
}
</script>
<style scoped>
.dt__shown_items_class{
    height: 21px;
    border-radius: 0;
    border: 1px solid #aaa;
    margin: 0 3px;
}
</style>