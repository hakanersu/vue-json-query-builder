<template>
  <div class="flex">
    <div class="p-0 w-full">
      <div class="grid grid-cols-6 gap-4">
        <div class="vue-query-rule-id pr-1 col-span-4">
          <slot name="ruleID" :rule="rule" :options="ruleIDOptions">
            <b-form-select
              size="sm"
              :options="ruleIDOptions"
              value-field="id"
              text-field="name"
              v-model="rule.id"
            />
          </slot>
        </div>
        <div class="vue-query-rule-operator pr-1">
          <slot name="ruleOperator" :rule="rule" :options="ruleOperatorOptions">
            <b-form-select
              size="sm"
              :options="ruleOperatorOptions"
              value-field="id"
              text-field="name"
              v-model="rule.operator"
            />
          </slot>
        </div>
        <div class="vue-query-rule-value pr-1">
          <slot name="number" :rule="rule" v-if="ruleParams.type === 'number'">
            <input v-model="rule.value" type="number" class="rounded-lg border-transparent flex-1 appearance-none border border-gray-300 w-full py-1 px-3 bg-white text-gray-700 placeholder-gray-400  text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"> 
          </slot>
          <slot name="date" :rule="rule" v-else-if="ruleParams.type === 'date'">
            <b-form-datepicker
              size="sm"
              v-model="rule.value"
              :date-format-options="{ year: 'numeric', month: 'numeric', day: 'numeric' }"
            />
          </slot>
          <slot name="time" :rule="rule" v-else-if="ruleParams.type === 'time'">
            <b-form-input
              size="sm"
              v-model="rule.value"
              type="time"
            />
          </slot>
          <slot name="select" :rule="rule" :options="ruleParams.options" :multiple="multiple" v-else-if="ruleParams.type === 'select'">
            <b-form-select
              size="sm"
              :options="ruleParams.options"
              value-field="id"
              text-field="name"
              v-model="rule.value"
              :multiple="multiple"
            />
          </slot>
          <slot name="phone" :rule="rule" v-else-if="ruleParams.type === 'phone'">
            <b-form-input
              size="sm"
              v-model="rule.value"
              type="tel"
            />
          </slot>
          <slot name="email" :rule="rule" v-else-if="ruleParams.type === 'email'">
            <b-form-input
              size="sm"
              v-model="rule.value"
              type="email"
            />
          </slot>
          <b-form-input
            size="sm"
            v-model="rule.value"
            v-else
          />
        </div>
      </div>
    </div>
  
    <button  v-on:click="deleteRule" type="button" class="relative inline-flex items-center px-1 py-1 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:z-10 focus:outline-none focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500">
    <svg class="-ml-1 mr-2 h-5 w-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg>
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
      type: Array
    }
  },
  data: function() {
    return {}
  },
  computed: {
    ruleParams: function() {
      const self = this;
      return self.options.find(function(option){
        return option.id === self.rule.id
      });
    },
    ruleIDOptions: function() {
      const self = this;
      const options = self.options;
      return options.map(function(option){
        return {
          id: option.id,
          name: option.name
        }
      })
    },
    currentRuleID: function(){
      const self = this;
      return self.rule.id;
    },
    currentRuleOperator: function(){
      const self = this;
      return self.rule.operator;
    },
    multiple: function(){
      const self = this;
      return self.rule.operator.includes('in');
    },
    ruleOperatorOptions: function() {
      const self = this;
      switch(self.ruleParams.type){
        case 'select':
          return [
            {id: '=',  name: 'is'},
            {id: '!=',  name: 'is not'},
            {id: 'in', name: 'is (multiple)'},
            {id: 'not in', name: 'is not (multiple)'}
          ];
        case 'number':
        case 'date':
        case 'month':
        case 'time':
          return [
            {id: '=',  name: 'is'},
            {id: '!=',  name: 'is not'},
            {id: '>',  name: 'is greater than'},
            {id: '<',  name: 'is less than'},
            {id: '>=', name: '>='},
            {id: '<=', name: '<='}
          ];
        case 'phone':
          return [
            {id: '=',  name: 'is'},
            {id: '!=',  name: 'is not'},
            {id: '()',  name: 'includes'},
            {id: '!()',  name: 'does not include'}
          ];
        case 'email':
          return [
            {id: '=',  name: 'is'},
            {id: '!=',  name: 'is not'},
            {id: '()',  name: 'includes'},
            {id: '!()',  name: 'does not include'},
            {id: '(',   name: 'starts with'},
            {id: ')',   name: 'ends with'},
            {id: '!(',   name: 'does not start with'},
            {id: '!)',   name: 'does not end with'}
          ];
        default:
          return [
            {id: '=',  name: 'is'},
            {id: '!=',  name: 'is not'},
            {id: 'in', name: 'is (multiple)'},
            {id: 'not in', name: 'is not (multiple)'},
            {id: '()',  name: 'includes'},
            {id: '!()',  name: 'does not include'},
            {id: '(',   name: 'starts with'},
            {id: ')',   name: 'ends with'},
            {id: '!(',   name: 'does not start with'},
            {id: '!)',   name: 'does not end with'}
          ];
      }
    }
  },
  methods: {
    resetRuleValue: function(){
      const self = this;
      if (self.multiple){
        self.rule.value = [];
      } else {
        self.rule.value = '';
      }
    },
    deleteRule: function(){
      const self = this;
      const parentRules = self.$parent.currentQuery.rules;
      parentRules.splice(parentRules.indexOf(self.rule), 1);
    }
  },
  watch: {
    currentRuleID: function() {
      const self = this;
      self.rule.operator = '=';
      self.resetRuleValue();
    },
    currentRuleOperator: function(to, from) {
      const self = this;
      if (to.includes('in') !== from.includes('in')){
        self.resetRuleValue();
      }
      return false;
    }
  }
}
</script>

<style lang="scss">
  .vue-query-rule {

    .vs__clear {
      display: none;
    }
  }
</style>
