<template>
  <div>
    <VueQueryRule :rule="currentQuery.rules[0]" :options="options" />
  </div>
</template>

<script>
import VueQueryRule from '@/vue-query-rule.vue'

export default {
  name: 'ParentComponentForRule',
  components: {
    VueQueryRule
  },
  props: {
    currentQuery: {
      type: Object,
      default: undefined
    },
    options: {
      type: Array,
      default: undefined
    }
  },
}
</script>
