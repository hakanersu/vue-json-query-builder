<template>
  <div
    class="bg-white border border-gray-200 shadow"
    border-variant="primary"
    :style="{'pointer-events': loading ? 'none' : 'all'}"
  >
    <div
      class="flex cursor-pointer bg-white px-4 py-2 border-b border-gray-200 sm:px-6 justify-between items-center"
      @click="isVisible = !isVisible"
    >
      <h3 class="text-lg leading-6 font-medium text-gray-900">
        Query Builder
      </h3>

      <div v-if="!isVisible">
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
            d="M5 15l7-7 7 7"
          />
        </svg>
      </div>
      <div v-else>
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
            d="M19 9l-7 7-7-7"
          />
        </svg>
      </div>
    </div>
    <div
      v-if="isVisible"
      class="p-2"
    >
      <VueQueryGroup
        :current-query="currentQuery"
        :options="options"
      >
        <template v-slot:ruleID="{rule, opt}">
          <!--
            @slot The user interface element to replace ruleID selector
            @binding {object} rule The options to be applied to this rule selector
            @binding {array} options The list of options to apply to new ruleID selector. Each object in array must include `id` and `name` attributes
          -->
          <slot
            name="ruleID"
            :rule="rule"
            :options="opt"
          />
        </template>
        <template v-slot:ruleOperator="{rule, opt}">
          <!--
            @slot The user interface element to replace ruleOperator selector
            @binding {object} rule The options to be applied to this operator selector
            @binding {array} options list of options to apply to new ruleOperator selector. Each object in array must include `id` and `name` attributes.
          -->
          <slot
            name="ruleOperator"
            :rule="rule"
            :options="opt"
          />
        </template>
        <template v-slot:number="{rule}">
          <!--
            @slot The user interface element to replace number input
            @binding {object} rule The rule to be connected to this input
          -->
          <slot
            name="number"
            :rule="rule"
          />
        </template>
        <template v-slot:date="{rule}">
          <!--
            @slot The user interface element to replace date input
            @binding {object} rule The rule to be connected to this input
          -->
          <slot
            name="date"
            :rule="rule"
          />
        </template>
        <template v-slot:time="{rule}">
          <!--
            @slot The user interface element to replace time input
            @binding {object} rule The rule to be connected to this input
          -->
          <slot
            name="time"
            :rule="rule"
          />
        </template>
        <template v-slot:select="{rule, opt, multiple}">
          <!--
            @slot The user interface element to replace select
            @binding {object} rule The rule to be connected to this select
            @binding {array} options list of options to apply to this select. Each object in array must include `id` and `name` attributes.
            @binding {boolean} multiple Determines whether select allows for singular or multiple selections.
          -->
          <slot
            name="select"
            :rule="rule"
            :options="opt"
            :multiple="multiple"
          />
        </template>
        <template v-slot:phone="{rule}">
          <!--
            @slot The user interface element to replace phone input
            @binding {object} rule The rule to be connected to this input
          -->
          <slot
            name="phone"
            :rule="rule"
          />
        </template>
        <template v-slot:email="{rule}">
          <!--
            @slot The user interface element to replace email input
            @binding {object} rule The rule to be connected to this input
          -->
          <slot
            name="email"
            :rule="rule"
          />
        </template>
      </VueQueryGroup>
    </div>
    <div
      v-if="isVisible"
      class="flex px-2 py-3 justify-between"
    >
      <span
        v-if="storage.length > 0"
        class="relative z-0 inline-flex shadow-sm rounded-md"
      >
        <button
          :disabled="runQueryDisabled"
          type="button"
          class="relative inline-flex items-center px-4 py-2 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="$refs.addModal.openModal()"
          v-text="saveText"
        />
        <button
          v-if="savedQueries.length > 0"
          type="button"
          class="-ml-px relative inline-flex items-center px-4 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="$refs.listModal.openModal()"
          v-text="viewQueryText"
        />
      </span>
      <div>
        <button
          type="button"
          class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          @click="resetToDefaultQuery"
          v-text="resetQueryText"
        />
        <button
          v-if="runQuery"
          :disabled="runQueryDisabled"
          type="button"
          class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          @click="runCurrentQuery(currentQuery)"
          v-text="runQueryText"
        />
      </div>
    </div>

    <modal ref="listModal">
      <table>
        <thead>
          <tr>
            <td>Name</td>
            <td>Created date</td>
            <td>Actions</td>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="sQuery in savedQueries"
            :key="sQuery.createdDate"
          >
            <td v-text="sQuery.name" />
            <td>
              {{ new Date(sQuery.createdDate).toLocaleDateString() }}
            </td>
            <td>
              <button @click="loadSavedQuery(sQuery.query)">
                Load
              </button>
              <button @click="deleteSavedQuery(sQuery.query)">
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </modal>
    <modal
      ref="addModal"
      @saveModal="saveQuery"
    >
      <input
        v-model="queryName"
        type="text"
        class="relative inline-flex items-center px-4 py-2 rounded-l-md border border-gray-300  text-sm font-medium  hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
      >
    </modal>
  </div>
</template>

<script>
import { v4 as uuidv4 } from 'uuid'

import VueQueryGroup from '@/vue-query-group.vue'
import Modal from '@/modal.vue'
/**
  * VueJSONQueryBuilder is a Vue-based UI component that allows the user to generate queries in JSON format.
  * @displayName Vue JSON Query Builder
*/

export default {
  name: 'VueJSONQueryBuilder',
  components: {
    VueQueryGroup,
    Modal
  },
  props: {
    saveText: {
      type: String,
      default: 'Save Query',
    },
    viewQueryText: {
      type: String,
      default: 'View Saved Queries'
    },
    resetQueryText: {
      type: String,
      default: 'Reset to Default Query',
    },
    runQueryText: {
      type: String,
      default: 'Run Query',
    },
    /**
      * The initial query to load into VueJSONQueryBuilder on load.
    */
    query: {
      type: Object,
      required: true
    },
    /**
      * The options to define the parameters of each rule.
    */
    options: {
      type: Array,
      default: () => [],
    },
    /**
      * @model
      * Returns current query to parent component.
    */
    value: {
      type: Object,
      required: false,
      default: function () {
        return {}
      }
    },
    /**
      * The location in localStorage in which to store current query.
    */
    storage: {
      type: String,
      required: false,
      default: ''
    },
    /**
      * The function to run upon click of "Run Query" button, returning current query.
    */
    runQuery: {
      type: [Function, Object],
      required: false,
      default: null
    },
    /**
      * Determines whether VueJSONQueryBuilder instance is collapsed or expanded upon load.
    */
    visible: {
      type: Boolean,
      required: false,
      default: true
    }
  },
  data: function () {
    return {
      currentQuery: {},
      isVisible: this.visible,
      storedQueries: this.getStoredQueries(),
      loading: false,
      queryName: '',
      modals: {
        viewSavedQueries: {
          visible: false,
          savedQueries: this.getSavedQueries()
        }
      }
    }
  },
  computed: {
    runQueryDisabled () {
      return this.loading || !this.areAllQueriesValid
    },
    savedQueries () {
      return this.modals.viewSavedQueries.savedQueries
    },
    areAllQueriesValid () {
      return this.checkIfEntityAndChildrenAreValid(this.currentQuery)
    },

  },
  watch: {
    currentQuery: {
      deep: true,
      handler: function () {
        const self = this
        self.$emit('input', self.currentQuery)
        if (self.storage) {
          localStorage.setItem('VueJSONQueryBuilder_stored_' + self.storage, JSON.stringify(self.currentQuery))
        }
      }
    },
    savedQueries: {
      deep: true,
      handler: function () {
        const self = this
        localStorage.setItem('VueJSONQueryBuilder_saved_' + self.storage, JSON.stringify(self.savedQueries))
      }
    }
  },
  created () {
    if (this.storedQueries) {
      this.currentQuery = JSON.parse(JSON.stringify(this.storedQueries))
    } else {
      this.resetToDefaultQuery()
    }
  },
  methods: {
    checkIfEntityAndChildrenAreValid (entity) {
      const keys = Object.keys(entity)
      let response = true

      if (keys.includes('value')) {
        if (entity.value === null || entity.value.toString().length === 0) {
          response = false
        }
      } else if (keys.includes('rules')) {
        for (let i = 0; i < entity.rules.length; i++) {
          const rule = entity.rules[i]
          if (!this.checkIfEntityAndChildrenAreValid(rule)) {
            response = false
          }
        }
      }

      return response
    },
    getStoredQueries () {
      if (this.storage) {
        const storedQueries = localStorage.getItem('VueJSONQueryBuilder_stored_' + this.storage)
        if (storedQueries) {
          return JSON.parse(storedQueries)
        }
        return false
      }
      return false
    },
    getSavedQueries () {
      if (this.storage) {
        const savedQueries = localStorage.getItem('VueJSONQueryBuilder_saved_' + this.storage)
        if (savedQueries) {
          return JSON.parse(savedQueries)
        }
        return []
      }
      return []
    },
    runCurrentQuery () {
      if (this.runQuery) {
        this.loading = true
        Promise.resolve(this.runQuery(this.currentQuery)).then(function () {
          this.loading = false
        })
      }
    },
    addUUIDsToCurrentQuery () {

      function addUUIDsToQueryAndChildren (query) {
        const keys = Object.keys(query)
        Object.defineProperty(query, '_uuid', {
          enumerable: false,
          value: uuidv4()
        })

        if (keys.includes('rules')) {
          for (let i = 0; i < query.rules.length; i++) {
            const rule = query.rules[i]
            addUUIDsToQueryAndChildren(rule)
          }
        }
      }

      addUUIDsToQueryAndChildren(this.currentQuery)
    },
    resetToDefaultQuery () {
      this.currentQuery = JSON.parse(JSON.stringify(this.query))
      this.addUUIDsToCurrentQuery()
    },
    saveQuery () {
      this.savedQueries.push({
        name: this.queryName,
        createdDate: new Date(),
        query: JSON.parse(JSON.stringify(this.currentQuery))
      })
      this.queryName = ''
    },
    loadSavedQuery (query) {
      this.currentQuery = JSON.parse(JSON.stringify(query))
      this.addUUIDsToCurrentQuery()
      this.modals.viewSavedQueries.visible = false
    },
    deleteSavedQuery (query) {
      this.savedQueries.splice(this.savedQueries.indexOf(query), 1)
    }
  }
}
</script>


