<template>
  <input 
    type="text" 
    :class="options.dt__search_input_class" 
    v-model="search_input"
    placeholder="Type to search..."
    @keyup="$emit('keyUp', search_input)" />
</template>

<script>
export default {
  props: {
    options: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      search_input: ''
    }
  },
}
</script>
<style scoped>
.dt__search_input_class{
    height: 21px;
    border-radius: 0;
    border: 1px solid #aaa;
    float: right;
}
</style>