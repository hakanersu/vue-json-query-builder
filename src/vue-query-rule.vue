<template>
  <div class="flex">
    <div class="p-0 w-full">
      <div class="grid grid-cols-6 gap-4">
        <div class="vue-query-rule-id pr-1 col-span-4">
          <slot
            name="ruleID"
            :rule="rule"
            :options="ruleIDOptions"
          >
            <div class="relative">
              <select
                v-model="rule.id"
                class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-1 px-2 pr-8 rounded text-sm leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
              >
                <template v-for="option in ruleIDOptions">
                  <option
                    :key="option.id"
                    :value="option.id"
                  >
                    {{ option.name }}
                  </option>
                </template>
              </select>
              <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                <svg
                  class="fill-current h-4 w-4"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                >
                  <path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z" />
                </svg>
              </div>
            </div>
          </slot>
        </div>
        <div class="vue-query-rule-operator pr-1">
          <slot
            name="ruleOperator"
            :rule="rule"
            :options="ruleOperatorOptions"
          >
            <div class="relative">
              <select
                v-model="rule.operator"
                class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-1 px-2 pr-8 rounded text-sm leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
              >
                <template v-for="option in ruleOperatorOptions">
                  <option
                    :key="option.id"
                    :value="option.id"
                  >
                    {{ option.name }}
                  </option>
                </template>
              </select>
              <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                <svg
                  class="fill-current h-4 w-4"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                >
                  <path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z" />
                </svg>
              </div>
            </div>
          </slot>
        </div>
        <div class="vue-query-rule-value pr-1">
          <slot
            v-if="ruleParams.type === 'number'"
            name="number"
            :rule="rule"
          >
            <input
              v-model="rule.value"
              type="number"
              class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-1 px-3 bg-white text-gray-700 placeholder-gray-400  text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
            >
          </slot>
          <slot
            v-else-if="ruleParams.type === 'date'"
            name="date"
            :rule="rule"
          >
            <input type="date" class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-1 px-2 pr-8 rounded leading-tight focus:outline-none focus:bg-white focus:border-gray-500">
          </slot>
          <slot
            v-else-if="ruleParams.type === 'time'"
            name="time"
            :rule="rule"
          >
            <input
              v-model="rule.value"
              type="time"
              class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-1 px-3 bg-white text-gray-700 placeholder-gray-400  text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
            >
          </slot>
          <slot
            v-else-if="ruleParams.type === 'select'"
            name="select"
            :rule="rule"
            :options="ruleParams.options"
            :multiple="multiple"
          >
            <div class="relative">
              <select
                v-model="rule.value"
                :multiple="multiple"
                class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-1 px-2 pr-8 rounded text-sm leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
              >
                <template v-for="option in ruleParams.options">
                  <option
                    :key="option.id"
                    :value="option.id"
                  >
                    {{ option.name }}
                  </option>
                </template>
              </select>
              <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                <svg
                  class="fill-current h-4 w-4"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                >
                  <path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z" />
                </svg>
              </div>
            </div>
          </slot>
          <slot
            v-else-if="ruleParams.type === 'phone'"
            name="phone"
            :rule="rule"
          >
            <input
              v-model="rule.value"
              type="tel"
              class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-1 px-3 bg-white text-gray-700 placeholder-gray-400  text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
            >
          </slot>
          <slot
            v-else-if="ruleParams.type === 'email'"
            name="email"
            :rule="rule"
          >
            <input
              v-model="rule.value"
              type="email"
              class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-1 px-3 bg-white text-gray-700 placeholder-gray-400  text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
            >
          </slot>
          <input
            v-else
            v-model="rule.value"
            type="email"
            class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-1 px-3 bg-white text-gray-700 placeholder-gray-400  text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
          >
        </div>
      </div>
    </div>

    <button
      type="button"
      class="relative inline-flex items-center px-1 py-1 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
      @click="deleteRule"
    >
      <svg
        class="-ml-1 mr-2 h-5 w-5 text-gray-400"
        fill="none"
        stroke="currentColor"
        viewBox="0 0 24 24"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
        />
      </svg>
    </button>
  </div>
</template>

<script>

export default {
  name: 'VueQueryRule',
  components: {
  },
  props: {
    rule: {
      type: Object,
      required: true
    },
    options: {
      type: Array,
      default: () => [],
    }
  },
  data: function () {
    return {}
  },
  computed: {
    ruleParams: function () {
      const self = this
      return self.options.find(function (option) {
        return option.id === self.rule.id
      })
    },
    ruleIDOptions: function () {
      const self = this
      const options = self.options
      return options.map(function (option) {
        return {
          id: option.id,
          name: option.name
        }
      })
    },
    currentRuleID: function () {
      const self = this
      return self.rule.id
    },
    currentRuleOperator: function () {
      const self = this
      return self.rule.operator
    },
    multiple: function () {
      const self = this
      return self.rule.operator.includes('in')
    },
    ruleOperatorOptions: function () {
      const self = this
      switch (self.ruleParams.type) {
        case 'select':
          return [
            { id: '=', name: 'is' },
            { id: '!=', name: 'is not' },
            { id: 'in', name: 'is (multiple)' },
            { id: 'not in', name: 'is not (multiple)' }
          ]
        case 'number':
        case 'date':
        case 'month':
        case 'time':
          return [
            { id: '=', name: 'is' },
            { id: '!=', name: 'is not' },
            { id: '>', name: 'is greater than' },
            { id: '<', name: 'is less than' },
            { id: '>=', name: '>=' },
            { id: '<=', name: '<=' }
          ]
        case 'phone':
          return [
            { id: '=', name: 'is' },
            { id: '!=', name: 'is not' },
            { id: '()', name: 'includes' },
            { id: '!()', name: 'does not include' }
          ]
        case 'email':
          return [
            { id: '=', name: 'is' },
            { id: '!=', name: 'is not' },
            { id: '()', name: 'includes' },
            { id: '!()', name: 'does not include' },
            { id: '(', name: 'starts with' },
            { id: ')', name: 'ends with' },
            { id: '!(', name: 'does not start with' },
            { id: '!)', name: 'does not end with' }
          ]
        default:
          return [
            { id: '=', name: 'is' },
            { id: '!=', name: 'is not' },
            { id: 'in', name: 'is (multiple)' },
            { id: 'not in', name: 'is not (multiple)' },
            { id: '()', name: 'includes' },
            { id: '!()', name: 'does not include' },
            { id: '(', name: 'starts with' },
            { id: ')', name: 'ends with' },
            { id: '!(', name: 'does not start with' },
            { id: '!)', name: 'does not end with' }
          ]
      }
    }
  },
  watch: {
    currentRuleID: function () {
      const self = this
      self.rule.operator = '='
      self.resetRuleValue()
    },
    currentRuleOperator: function (to, from) {
      const self = this
      if (to.includes('in') !== from.includes('in')) {
        self.resetRuleValue()
      }
      return false
    }
  },
  methods: {
    resetRuleValue: function () {
      const self = this
      if (self.multiple) {
        self.rule.value = []
      } else {
        self.rule.value = ''
      }
    },
    deleteRule: function () {
      const self = this
      const parentRules = self.$parent.currentQuery.rules
      parentRules.splice(parentRules.indexOf(self.rule), 1)
    }
  }
}
</script>

