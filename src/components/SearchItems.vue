<template>
  <div :class="options.dt__search_wrapper">
    <input 
      type="text" 
      :class="options.dt__search_input" 
      v-model="search_input" 
      @keyup="$emit('keyUp', search_input)" />
  </div>
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