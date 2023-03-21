<template>
  <h1>{{ msg }}</h1>
  <input
    type="text"
    :value="msg"
    @input="handler" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 'Hello world!'
      }
    },
    methods: {
      handler(event) {
        console.log(event.target.value)
        this.msg = event.target.value
      }
    }
  }
</script>