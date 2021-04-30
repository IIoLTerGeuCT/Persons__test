<template>
  <div class="v-layout__wrapper container">
    <div class="v-layout__header">
      <h1>Список контактов:</h1>
    </div>
    <div class="v-layout__body">
      <div class="v-layout__content">
        <Persons />
      </div>
    </div>
  </div>
</template>
<script>
import Persons from "@/components/v-persons";
export default {
  components: { Persons },
  methods: {},
};
</script>
<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.v-layout__wrapper {
  /* background-color: #f3f3f3fd; */
  box-shadow: 2px 2px 10px gray;
  max-width: 1200px;
  margin: 0 auto;
  border-radius: 8px;
}
.v-layout__header {
  text-align: center;
  font-family: Roboto;
}
.v-layout__body {
  display: flex;
  justify-content: space-around;
}
.v-layout__content {
  padding: 20px;
}
</style>
