<template>
  <div>
    <div
      :class="[open ? '' : 'hidden']"
      class="overflow-x-hidden overflow-y-auto fixed inset-0 z-50 outline-none focus:outline-none justify-center items-center"
    >
      <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
        <!--content-->
        <div class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
          <!--header-->
          <div class="flex items-start justify-between px-3 py-2 border-b border-solid border-blueGray-200 rounded-t">
            <h3
              class="text-xl font-semibold text-gray-700 m-0 p-0"
              v-text="title"
            />
            <button
              class="p-1 ml-auto bg-transparent border-0 text-black opacity-5 float-right text-3xl leading-none font-semibold outline-none focus:outline-none"
              @click="open=false"
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
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </button>
          </div>
          <!--body-->
          <div class="relative p-6 flex-auto">
            <slot />
          </div>
          <!--footer-->
          <div class="flex items-center border-t border-solid border-blueGray-200 rounded-b p-3">
            <button
              type="button"
              class="inline-flex justify-center w-full rounded-md   px-4 py-2 bg-indigo-600 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:w-auto sm:text-sm"
              @click="saveModal"
              v-text="saveText"
            />
            <button
              type="button"
              class="w-full inline-flex justify-center rounded-md border border-gray-300 px-4 py-2 bg-white text-base font-medium text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm"
              @click="open=false"
              v-text="cancelText"
            />
          </div>
        </div>
      </div>
    </div>
    <div
      :class="[open ? '' : 'hidden']"
      class="opacity-25 fixed inset-0 z-40 bg-black"
    />
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: 'Modal title',
    },
    saveText: {
      type: String,
      default: 'Save Query',
    },
    cancelText: {
      type: String,
      default: 'Cancel'
    }
  },
  data: () => ({
    open: false,
  }),
  methods: {
    openModal () {
      this.open = true
    },
    closeModal () {
      this.open = false
    },
    saveModal () {
      this.open = false
      this.$emit('saveModal')
    }
  }
}
</script>