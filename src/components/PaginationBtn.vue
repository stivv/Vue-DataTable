<template>
  <li :class="show_btn ? options.pagination_element : options.pagination_disabled_element">
    <a v-if="show_btn" href="#" @click.prevent="$emit('goToPage', page)">{{btn_text}}</a>
    <a v-else @click.prevent>{{btn_text}}</a>
  </li>
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