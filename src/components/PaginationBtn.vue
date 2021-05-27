<template>
  <a v-if="show_btn" href="#" @click.prevent="$emit('goToPage', page)" :class="options.dt__pagination_element">
    {{btn_text}}
  </a>
  <a v-else :class="options.dt__pagination_disabled_element" @click.prevent>{{btn_text}}</a>
</template>

<script>
export default {
  props: {
    options: {
      type: Object,
      required: true
    },
    show_btn: {
      type: Boolean,
      required: true
    },
    page: {
      type: Number,
      required: true
    },
    btn_text: {
      type: String,
      required: true
    }
  }
}
</script>