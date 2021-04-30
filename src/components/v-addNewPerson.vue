<template>
  <div class="v-addNewPerson">
    <div class="v-addNewPerson__wrapper">
      <div class="v-addNewPerson__container">
        <div class="v-addNewPerson__header">
          <span class="header__title">{{ title }}</span>
        </div>
        <form @submit="sendNewPerson" onsubmit="return false">
          <div class="form__surnameNp confirm">
            <label for="surnameNp">ФИО: </label>
            <input
              type="text"
              id="surnameNp"
              v-model="surnameNp"
              :class="{
                invalid:
                  ($v.surnameNp.$dirty && !$v.surnameNp.required) ||
                  ($v.surnameNp.$dirty && !$v.surnameNp.minLength),
              }"
            />

            <label
              v-if="$v.surnameNp.$dirty && !$v.surnameNp.required"
              class="invalide"
              >Заполните данные!</label
            >
          </div>
          <div class="form__age confirm">
            <label for="age">Возраст: </label>
            <input
              type="text"
              id="age"
              v-model="age"
              :class="{
                invalid: $v.age.$dirty && !$v.age.required,
              }"
            />
            <label v-if="$v.age.$dirty && !$v.age.required" class="invalide">
              Укажите возраст!
            </label>
          </div>
          <div class="form__habitation">
            <label>Страна: </label>
            <select name="habitation" v-model="habitation" id="habitation">
              <option
                v-for="item of habitationData"
                :key="item"
                :value="item"
                :class="{
                  invalid: $v.habitation.$dirty && !$v.habitation.required,
                }"
              >
                {{ item }}
              </option>
            </select>
            <label
              v-if="$v.habitation.$dirty && !$v.habitation.required"
              class="invalide"
            >
              Укажите страну проживания!
            </label>
          </div>
          <div class="form__date_born">
            <label for="date_born"
              >Дата рождения:
              <input
                type="date"
                v-model="date_born"
                id="date_born"
                :class="{
                  invalid: $v.date_born.$dirty && !$v.date_born.required,
                }"
            /></label>
            <label
              v-if="$v.date_born.$dirty && !$v.date_born.required"
              class="invalide"
            >
              Укажите дату рождения!
            </label>
          </div>
          <div class="form__gender">
            <div class="radio">
              <label>Пол: </label>
              <div class="form-check">
                <input
                  class="form-check-input"
                  type="radio"
                  name="flexRadioDefault"
                  id="flexRadioDefault1"
                  v-model="gender"
                  value="Мужской"
                />
                <label class="form-check-label" for="flexRadioDefault1">
                  Мужской
                </label>
              </div>
              <div class="form-check">
                <input
                  class="form-check-input"
                  type="radio"
                  name="flexRadioDefault"
                  id="flexRadioDefault2"
                  v-model="gender"
                  value="Женский"
                  checked
                />
                <label class="form-check-label" for="flexRadioDefault2">
                  Женский
                </label>
              </div>
            </div>
          </div>
          <div class="form__img">
            <label for="img"
              >Ссылка на фото:
              <input
                type="url"
                id="img"
                v-model="img"
                :class="{
                  invalid: $v.img.$dirty && !$v.img.required,
                }"
            /></label>
            <label v-if="$v.img.$dirty && !$v.img.required" class="invalide">
              Укажите ссылку на фото!
            </label>
          </div>
          <div class="container__controls">
            <div class="container__btn-confirm">
              <button class="btn-confirm btn" type="submit">Ок</button>
            </div>
            <div class="container__btn-cancel">
              <button class="btn-cancel btn" @click="$emit('confirm', false)">
                Отмена
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import { required, minLength } from "vuelidate/lib/validators";
export default {
  props: ["title", "contact"],
  data() {
    return {
      id: "",
      surnameNp: "",
      age: "",
      date_born: "",
      habitation: "",
      gender: "",
      img: "",
      // addState: false,
      // updateState: false,
    };
  },
  validations: {
    surnameNp: { required, minLength: minLength(3) },
    age: { required },
    date_born: { required },
    habitation: { required, minLength: minLength(3) },
    gender: { required },
    img: { required },
  },
  computed: {
    habitationData() {
      return [
        "Россия",
        "США",
        "Канада",
        "Китай",
        "Italy",
        "Индия",
        "Германия",
        "Франция",
        "Англия",
        "Молдова",
        "Game persons",
        "Куба",
      ];
    },
  },
  mounted() {
    this.checked();
  },
  methods: {
    checked() {
      if (this.contact) {
        this.id = this.contact.id;
        this.surnameNp = this.contact.surnameNp;
        this.age = this.contact.age;
        this.habitation = this.contact.habitation;
        this.date_born = this.contact.date_born;
        this.gender = this.contact.gender;
        this.img = this.contact.img;
      }
    },
    sendNewPerson() {
      if (this.$v.$invalid) {
        this.$v.$touch();
        return false;
      }
      const obj = {
        id: this.id,
        surnameNp: this.surnameNp,
        age: this.age,
        date_born: this.date_born,
        habitation: this.habitation,
        gender: this.gender,
        img: this.img,
      };
      if (this.contact) {
        this.$emit("updatePerson", obj);
      } else {
        this.$emit("addNewPerson", obj);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.v-addNewPerson {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}
.v-addNewPerson__wrapper {
  display: table-cell;
  vertical-align: middle;
}
.header__title {
  margin-top: 10px;
}
.v-addNewPerson__header {
  margin-bottom: 10px;
}
.v-addNewPerson__container {
  width: 400px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}
.title {
  margin-top: 0;
}
.container__controls {
  display: flex;
  justify-content: space-around;
  padding-top: 8px;
}
.radio {
  display: flex;
  justify-content: space-between;
}
.btn {
  padding: 2px 10px;
  width: 100%;
  height: 32px;
  cursor: pointer;
  color: #fff;
  font-weight: 500;
  border: transparent;
  border-radius: 4px;
}
.btn-confirm {
  width: 100px;
  background-color: #8ac926;
  color: #fff;
  font-weight: 500;
}
.btn-cancel {
  width: 100px;
  color: #fff;
  background-color: #ff595e;
  font-weight: 500;
}
#img:invalid {
  border: 2px solid red;
}
label {
  display: block;
  margin: 0;
  padding: 0;
  color: #42b983;
}
input {
  border: none;
  border-bottom: 1px solid #000;
  margin-bottom: 2px;
  width: 100%;
  text-align: center;
}
select {
  border: none;
  border-bottom: 1px solid #000;
  width: 100%;
  text-align: center;
}
.confirm {
  display: block;
}
.form-check {
  display: flex;
  justify-content: flex-end;
  flex-basis: 50%;
}
.invalide {
  color: red;
  font-weight: bold;
}
</style>
