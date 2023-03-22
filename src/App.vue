<template>
  <h1>{{ msg }}</h1>
  <!-- <input
    type="text"
    :value="msg"
    @change="msg = $event.target.value" /> -->
  <input
    type="text"
    v-model.trim="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 'Hello world'
      }
    },
    watch: {
      msg(){
        console.log(this.msg)
      }
    }
  }
</script>