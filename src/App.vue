<template>
  <h1
    :style="[fontStyle, backgroundColor]"
    @click="changestyle">
    Hello?!
  </h1>
</template>

<script>
export default {
  data() {
    return {
      fontStyle: {
      color: 'orange',
      fontSize: '30px'
      },
      backgroundColor: {
        backgroundColor: 'black'
      }
    }
  },
  methods: {
    changestyle() {
      this.fontStyle.color = 'red',
      this.fontStyle.fontSize = '50px'
    }
  }
}
</script>