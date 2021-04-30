<template>
  <div class="v-person__wrapper">
    <div class="v-person__card-img">
      <img class="card-img" :src="itemPerson.img" alt="img" />
    </div>
    <div class="v-person__card-info">
      <div class="card__surnameNp">
        <span>ФИО: {{ itemPerson.surnameNp }}</span>
      </div>
      <div class="card__date_born">
        <span>Дата рождения: {{ itemPerson.date_born }}</span>
      </div>
      <div class="card__habitation">
        <span>Место жительства: {{ itemPerson.habitation }}</span>
      </div>
      <div class="card__age">
        <span>Возраст: {{ itemPerson.age }}</span>
      </div>
      <div class="card__gender">
        <span>Пол: {{ itemPerson.gender }}</span>
      </div>
      <div class="card__details">
        <button class="btn btn-info" @click="updateItem">Изменить</button>
        <button class="btn btn-danger" @click="removeItem">Удалить</button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ["itemPerson"],
  methods: {
    removeItem() {
      this.$emit("saveIdForRemove", this.itemPerson.id);
    },
    updateItem() {
      this.$emit("saveSelectedContact", this.itemPerson)
    }
  },
};
</script>
<style lang="scss" scoped>
.v-person__wrapper {
  display: flex;
  padding: 3px;
}
.v-person__card-info {
  margin-left: 20px;
  color: #086375;
}
.btn-info {
  margin-right: 10px;
}
.card-img {
  width: 150px;
  height: 150px;
  object-fit: cover;
}
</style>
