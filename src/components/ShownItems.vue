<template>
  <div>
    <span>Show</span>
    <select v-model="per_page">
      <option v-for="si in shownItems" :key="si" :value="si">{{si}}</option>
    </select>
    <span>entries</span>
  </div>
</template>

<script>
export default {
  props: {
    shown_items: Array,
    perPage: Number || 10
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