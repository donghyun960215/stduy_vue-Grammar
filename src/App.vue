<template>
  <div
    class="parent"
    @click.self="handlerA">
    <div
      class="child"></div>
  </div>
</template>

<script>
export default {
  methods: {
    handlerA(event) {
      console.log(event.target)
      console.log(event.currentTarget)
      console.log('A')
    },
    handlerB() {
      console.log('B')
    }
  }
}
</script>

<style scoped lang="scss">
.parent {
  background-color: royalblue;
  width: 200px;
  height: 100px;
  margin: 10px;
  padding: 10px;
  .child {
    background-color: orange;
    width: 100px;
    height: 100px;
  }
}
</style>