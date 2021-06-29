<template>
  <div
    class="bg-white border border-gray-200 shadow"
    border-variant="primary"
    :style="{'pointer-events': loading ? 'none' : 'all'}"
  >
    <div
      class="flex  bg-white px-4 py-2 border-b border-gray-200 sm:px-6 justify-between items-center"
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
        <template v-slot:ruleID="{rule, options}">
          <!--
            @slot The user interface element to replace ruleID selector
            @binding {object} rule The options to be applied to this rule selector
            @binding {array} options The list of options to apply to new ruleID selector. Each object in array must include `id` and `name` attributes
          -->
          <slot
            name="ruleID"
            :rule="rule"
            :options="options"
          />
        </template>
        <template v-slot:ruleOperator="{rule, options}">
          <!--
            @slot The user interface element to replace ruleOperator selector
            @binding {object} rule The options to be applied to this operator selector
            @binding {array} options list of options to apply to new ruleOperator selector. Each object in array must include `id` and `name` attributes.
          -->
          <slot
            name="ruleOperator"
            :rule="rule"
            :options="options"
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
        <template v-slot:select="{rule, options, multiple}">
          <!--
            @slot The user interface element to replace select
            @binding {object} rule The rule to be connected to this select
            @binding {array} options list of options to apply to this select. Each object in array must include `id` and `name` attributes.
            @binding {boolean} multiple Determines whether select allows for singular or multiple selections.
          -->
          <slot
            name="select"
            :rule="rule"
            :options="options"
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
        >
          Save Query
        </button>
        <button
          v-if="savedQueries.length > 0"
          type="button"
          class="-ml-px relative inline-flex items-center px-4 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500"
          @click="$refs.listModal.openModal()"
        >
          View Saved Queries
        </button>
      </span>
      <div>
        <button
          type="button"
          class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          @click="resetToDefaultQuery"
        >
          Reset to Default Query
        </button>
        <button
          v-if="runQuery"
          :disabled="runQueryDisabled"
          type="button"
          class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          @click="runCurrentQuery(currentQuery)"
        >
          Run Query
        </button>
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
            v-for="query in savedQueries"
            :key="query.createdDate"
          >
            <td v-text="query.name" />
            <td>
              {{ new Date(query.createdDate).toLocaleDateString() }}
            </td>
            <td>
              <button @click="loadSavedQuery(query.query)">
                Load
              </button>
              <button @click="deleteSavedQuery(query.query)">
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
      type: Array
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
    runQueryDisabled: function () {
      const self = this
      return self.loading || !self.areAllQueriesValid
    },
    savedQueries: function () {
      const self = this
      return self.modals.viewSavedQueries.savedQueries
    },
    areAllQueriesValid: function () {
      const self = this

      function checkIfEntityAndChildrenAreValid (entity) {
        const keys = Object.keys(entity)
        let response = true

        if (keys.includes('value')) {
          if (entity.value === null || entity.value.toString().length === 0) {
            response = false
          }
        } else if (keys.includes('rules')) {
          for (let i = 0; i < entity.rules.length; i++) {
            const rule = entity.rules[i]
            if (!checkIfEntityAndChildrenAreValid(rule)) {
              response = false
            }
          }
        }

        return response
      }

      return checkIfEntityAndChildrenAreValid(self.currentQuery)
    }
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
  created: function () {
    const self = this

    if (self.storedQueries) {
      self.currentQuery = JSON.parse(JSON.stringify(self.storedQueries))
    } else {
      self.resetToDefaultQuery()
    }
  },
  methods: {
    getStoredQueries: function () {
      const self = this
      if (self.storage) {
        const storedQueries = localStorage.getItem('VueJSONQueryBuilder_stored_' + self.storage)
        if (storedQueries) {
          return JSON.parse(storedQueries)
        }
        return false
      }
      return false
    },
    getSavedQueries: function () {
      const self = this
      if (self.storage) {
        const savedQueries = localStorage.getItem('VueJSONQueryBuilder_saved_' + self.storage)
        if (savedQueries) {
          return JSON.parse(savedQueries)
        }
        return []
      }
      return []
    },
    runCurrentQuery: function () {
      const self = this
      if (self.runQuery) {
        self.loading = true
        Promise.resolve(self.runQuery(self.currentQuery)).then(function () {
          self.loading = false
        })
      }
    },
    addUUIDsToCurrentQuery: function () {
      const self = this

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

      addUUIDsToQueryAndChildren(self.currentQuery)
    },
    resetToDefaultQuery: function () {
      const self = this

      self.currentQuery = JSON.parse(JSON.stringify(self.query))
      self.addUUIDsToCurrentQuery()
    },
    saveQuery () {

      this.savedQueries.push({
        name: this.queryName,
        createdDate: new Date(),
        query: JSON.parse(JSON.stringify(this.currentQuery))
      })
      this.queryName = ''
    },
    loadSavedQuery: function (query) {
      const self = this
      self.currentQuery = JSON.parse(JSON.stringify(query))
      self.addUUIDsToCurrentQuery()
      self.modals.viewSavedQueries.visible = false
    },
    deleteSavedQuery: function (query) {
      const self = this
      self.$bvModal.msgBoxConfirm('Please confirm that you wish to delete this query.', {
        title: 'Confirm Query Deletion',
        size: 'sm',
        buttonSize: 'sm',
        okVariant: 'danger',
        okTitle: 'Yes',
        cancelTitle: 'No',
        footerClass: 'p-2',
        hideHeaderClose: true,
        centered: true
      }).then(function (response) {
        if (response === true) {
          self.savedQueries.splice(self.savedQueries.indexOf(query), 1)
          if (self.savedQueries.length === 0) {
            self.modals.viewSavedQueries.visible = false
          }
        } else {
          return false
        }
      })
    }
  }
}
</script>


