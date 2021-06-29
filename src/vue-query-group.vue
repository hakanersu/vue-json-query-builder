<template>
  <div>
    <div class="flex justify-between items-center">
      <span class="relative z-0 inline-flex shadow-sm rounded-md">
        <button
          type="button"
          :class="[andConditionButtonVariant]"
          class="relative inline-flex items-center px-2 py-1 rounded-l-md border border-gray-300  text-sm font-medium  hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="currentQuery.condition = 'and'"
        >
          AND
        </button>
        <button
          type="button"
          :class="[orConditionButtonVariant]"
          class="-ml-px relative inline-flex items-center px-2 py-1 rounded-r-md border border-gray-300 text-sm font-medium hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="currentQuery.condition = 'or'"
        >

          OR
        </button>
      </span>
      <span class="relative z-0 inline-flex shadow-sm rounded-md">
        <button
          v-if="level > 0"
          type="button"
          class="relative inline-flex items-center px-2 py-1 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="deleteGroup"
        >
          <svg
            class="w-6 h-6"
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
          Delete Group
        </button>
        <button
          type="button"
          :class="[level<=0 ? 'rounded-l-md' : '']"
          class="-ml-px relative inline-flex items-center px-2 py-1 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="addRule"
        >
          <svg
            class="w-6 h-6"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M12 6v6m0 0v6m0-6h6m-6 0H6"
            />
          </svg>
          Add Rule
        </button>
        <button
          type="button"
          class="-ml-px relative inline-flex items-center px-4 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="addGroup"
        >
          <svg
            class="w-6 h-6"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M9 13h6m-3-3v6m-9 1V7a2 2 0 012-2h6l2 2h6a2 2 0 012 2v8a2 2 0 01-2 2H5a2 2 0 01-2-2z"
            />
          </svg>
          Add Group
        </button>
      </span>
    </div>
    <template v-for="(item) in currentQuery.rules">
      <div
        v-if="typeof item.condition === 'string'"
        :key="item._uuid"
        class="mt-2"
      >
        <div class="p-2 border border-blue-600">
          <VueQueryGroup
            :current-query="item"
            :options="options"
            :level="level + 1"
          >
            <template v-slot:ruleID="{rule, opt}">
              <slot
                name="ruleID"
                :rule="rule"
                :options="opt"
              />
            </template>
            <template v-slot:ruleOperator="{rule, opt}">
              <slot
                name="ruleOperator"
                :rule="rule"
                :options="opt"
              />
            </template>
            <template v-slot:number="{rule}">
              <slot
                name="number"
                :rule="rule"
              />
            </template>
            <template v-slot:date="{rule}">
              <slot
                name="date"
                :rule="rule"
              />
            </template>
            <template v-slot:time="{rule}">
              <slot
                name="time"
                :rule="rule"
              />
            </template>
            <template v-slot:select="{rule, opt, multiple}">
              <slot
                name="select"
                :rule="rule"
                :options="opt"
                :multiple="multiple"
              />
            </template>
            <template v-slot:phone="{rule}">
              <slot
                name="phone"
                :rule="rule"
              />
            </template>
            <template v-slot:email="{rule}">
              <slot
                name="email"
                :rule="rule"
              />
            </template>
          </VueQueryGroup>
        </div>
      </div>
      <VueQueryRule
        v-else
        :key="item._uuid"
        :rule="item"
        :options="options"
        class="mt-2"
      >
        <template v-slot:ruleID="{rule, opt}">
          <slot
            name="ruleID"
            :rule="rule"
            :options="opt"
          />
        </template>
        <template v-slot:ruleOperator="{rule, opt}">
          <slot
            name="ruleOperator"
            :rule="rule"
            :options="opt"
          />
        </template>
        <template v-slot:number="{rule}">
          <slot
            name="number"
            :rule="rule"
          />
        </template>
        <template v-slot:date="{rule}">
          <slot
            name="date"
            :rule="rule"
          />
        </template>
        <template v-slot:time="{rule}">
          <slot
            name="time"
            :rule="rule"
          />
        </template>
        <template v-slot:select="{rule, opt, multiple}">
          <slot
            name="select"
            :rule="rule"
            :options="opt"
            :multiple="multiple"
          />
        </template>
        <template v-slot:phone="{rule}">
          <slot
            name="phone"
            :rule="rule"
          />
        </template>
        <template v-slot:email="{rule}">
          <slot
            name="email"
            :rule="rule"
          />
        </template>
      </VueQueryRule>
    </template>
  </div>
</template>

<script>

import { v4 as uuidv4 } from 'uuid'

import VueQueryRule from '@/vue-query-rule.vue'

export default {
  name: 'VueQueryGroup',
  components: {
    VueQueryRule
  },
  props: {
    currentQuery: {
      type: Object,
      required: true
    },
    options: {
      type: Array,
      default: () => []
    },
    level: {
      type: Number,
      required: false,
      default: 0
    }
  },
  data: function () {
    return {
      condition: 'and'
    }
  },
  computed: {
    andConditionButtonVariant: function () {
      const self = this
      if (self.currentQuery.condition === 'and') {
        return 'bg-indigo-600 hover:bg-indigo-700 text-white'
      }
      return 'bg-white text-gray-700'
    },
    orConditionButtonVariant: function () {
      const self = this
      if (self.currentQuery.condition === 'or') {
        return 'bg-indigo-600 hover:bg-indigo-700 text-white'
      }
      return 'bg-white text-gray-700'
    }
  },
  mounted: function () {
    const self = this
    if (self.currentQuery.rules.length === 0) {
      self.addRule()
    }
  },
  methods: {
    addRule: function () {
      const self = this
      const newGroup = {
        id: self.options[0].id,
        operator: '=',
        value: ''
      }

      Object.defineProperty(newGroup, '_uuid', {
        enumerable: false,
        value: uuidv4()
      })

      self.currentQuery.rules.push(newGroup)
    },
    addGroup: function () {
      const self = this
      const newRule = {
        condition: 'and',
        rules: []
      }

      Object.defineProperty(newRule, '_uuid', {
        enumerable: false,
        value: uuidv4()
      })

      self.currentQuery.rules.push(newRule)
    },
    deleteGroup: function () {
      const self = this
      const parentRules = self.$parent.currentQuery.rules
      parentRules.splice(parentRules.indexOf(self.currentQuery), 1)
    }
  }
}
</script>
