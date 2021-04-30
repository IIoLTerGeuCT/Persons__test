<template>
  <div class="v-persons__wrapper" v-if="loadSuccess">
    <div class="v-persons__controls">
      <button
        class="btn btn-success v-persons__addNewPerson"
        @click="showVisibleAddForm"
      >
        Добавить контакт
      </button>
      <div class="dropdown">
        <button
          class="btn btn-secondary dropdown-toggle"
          type="button"
          id="dropdownMenu2"
          data-toggle="dropdown"
          aria-haspopup="true"
          aria-expanded="false"
        >
          Сортировка:
        </button>
        <div class="dropdown-menu" aria-labelledby="dropdownMenu2">
          <button class="dropdown-item" type="button" @click="sortById">
            По Id
          </button>
          <button class="dropdown-item" type="button" @click="sortBySurnameNp">
            По ФИО
          </button>
          <button class="dropdown-item" type="button" @click="sortByAge">
            По возрасту
          </button>
          <button class="dropdown-item" type="button" @click="sortByDate_born">
            По дате рождения
          </button>
          <button class="dropdown-item" type="button" @click="sortByHabitation">
            По месту проживания
          </button>
          <button class="dropdown-item" type="button" @click="sortByGender">
            По полу
          </button>
        </div>
      </div>
    </div>
    <div class="v-persons__addNewPerson-form" v-if="showFormAdd">
      <AddNewPerson
        :title="title"
        @addNewPerson="addNewPerson"
        @confirm="cancelAddNewPerson"
      />
    </div>
    <div class="v-persons__UpdatePerson-form" v-if="showFormUpdate">
      <AddNewPerson
        :contact="selectedContactForUpdate"
        @updatePerson="updatePerson"
        @confirm="cancelAddNewPerson"
      />
    </div>

    <div class="v-persons__container">
      <div class="v-persons__filter">
        <label for="">Фильтрация:</label>
        <div class="filter__gender">
          <label for="">По полу:</label>
          <div class="btn-group btn-group-toggle" data-toggle="buttons">
            <label class="btn btn-secondary active">
              <input
                type="radio"
                name="options"
                id="option1"
                v-model="gender"
                value="Мужской"
              />
              Мужской
            </label>
            <label class="btn btn-secondary">
              <input
                type="radio"
                name="options"
                id="option2"
                v-model="gender"
                value="Женский"
              />
              Женский
            </label>
          </div>
        </div>
        <div class="filter__age">
          <label for="">По возрасту:</label>
          <div class="age_input_controls">
            <input
              type="text"
              class="style_input"
              placeholder="от"
              v-model="age_from"
            />
            <input
              type="text"
              class="style_input"
              placeholder="до"
              v-model="age_to"
            />
          </div>
        </div>
        <div class="filter__date">
          <label for="">По дате рождения:</label>
          <div class="date_input_controls">
            <input type="date" placeholder="от" v-model="date_from" />
            <input type="date" placeholder="до" v-model="date_to" />
          </div>
        </div>
        <div class="filter__btn-controls">
          <button
            class="btn btn-info apply_cancel_style"
            @click="clickApplyFilter"
          >
            Применить
          </button>
          <button
            class="btn btn-danger apply_cancel_style"
            @click="clickCancelFilter"
          >
            Сбросить
          </button>
        </div>
      </div>
      <div class="v-persons__no-cards" v-if="!persons.length">
        <span class="no-find-data">Ничего не найдено!!!</span>
      </div>
      <div class="v-persons__cards" v-if="persons.length">
        <Person
          v-for="item of paginatedPersons"
          :key="item.id"
          :itemPerson="item"
          @saveIdForRemove="saveIdForRemove"
          @saveSelectedContact="saveContactForUpdate"
        />
      </div>
    </div>

    <Modal
      v-if="showModalRemove"
      :title="modalTitle"
      @confirm="confirmRemoveItem"
    />
    <div class="v-persons__pagination">
      <div
        class="page"
        v-for="page in pages"
        :key="page"
        @click="pageClick(page)"
      >
        {{ page }}
      </div>
    </div>
  </div>
</template>
<script>
import Person from "@/components/v-person.vue";
import AddNewPerson from "@/components/v-addNewPerson.vue";
import Modal from "@/components/v-modal.vue";
export default {
  data() {
    return {
      loadSuccess: false,
      persons: [], // Массив персон
      title: "",
      showFormAdd: false,
      showFormUpdate: false,
      showModalRemove: false,
      selectedContactForUpdate: {}, // Выбранный контакт для изменения
      modalTitle: "",
      selectedIdForRemove: "", // Выбранный Id для удаления
      personAmoutFromPage: 5, // Количество person на странице
      pageNumber: 1, // Текущая страница
      //////// Фильтрация
      gender: "",
      age_from: "",
      age_to: "",
      date_from: "",
      date_to: "",
    };
  },
  computed: {
    pages() {
      // Посчитаем количество страниц
      return Math.ceil(this.persons.length / 5);
    },
    paginatedPersons() {
      let from = (this.pageNumber - 1) * this.personAmoutFromPage;
      let to = from + this.personAmoutFromPage;
      return this.persons.slice(from, to);
    },
  },
  components: { Person, AddNewPerson, Modal },
  created() {
    try {
      this.getPersonsDataFromApi();
      this.loadSuccess = true;
    } catch {
      this.loadSuccess = false;
      console.log(`Ошибка загрузки данных, необходимо запустить json-server`);
    }
  },
  methods: {
    async getPersonsDataFromApi() {
      const response = await fetch("http://localhost:3000/persons", {
        method: "GET",
        headers: { "Content-Type": "application/json; charset=utf-8" },
      });
      const res = await response.json();
      this.persons = res;
    },
    showVisibleAddForm() {
      this.title = "Добавление нового контакта";
      this.showFormAdd = true;
    },
    showVisibleUpdateForm() {
      this.title = "Изменение контакта";
      this.showFormUpdate = true;
    },
    saveContactForUpdate(contact) {
      this.selectedContactForUpdate = contact;

      this.showVisibleUpdateForm();
    },
    addNewPerson(newPerson) {
      this.showFormAdd = false;
      // Получим последний id
      let currentId = this.persons[this.persons.length - 1].id + 1;
      // Добавим поле id
      Object.assign(newPerson, { id: currentId });
      // Добавим в исходные данные
      this.persons.push(newPerson);
    },

    updatePerson(updatePerson) {
      this.showFormUpdate = false;
      Object.assign(this.persons[updatePerson.id - 1], updatePerson);
    },

    cancelAddNewPerson() {
      this.showFormAdd = false;
      this.showFormUpdate = false;
    },
    saveIdForRemove(id) {
      this.selectedIdForRemove = id;
      this.modalTitle = "Вы действительно хотите удалить контакт?";
      this.showModalRemove = true;
    },

    confirmRemoveItem(event) {
      this.showModalRemove = false;
      if (event) {
        this.persons = this.persons.filter(
          (item) => item.id !== this.selectedIdForRemove
        );
      }
    },
    pageClick(page) {
      this.pageNumber = page;
    },
    sortById() {
      this.persons.sort((a, b) => (a.id < b.id ? -1 : 0));
    },
    sortBySurnameNp() {
      this.persons.sort((a, b) => (a.surnameNp < b.surnameNp ? -1 : 0));
    },
    sortByAge() {
      this.persons.sort((a, b) => (a.age < b.age ? -1 : 0));
    },
    sortByHabitation() {
      this.persons.sort((a, b) => (a.habitation < b.habitation ? -1 : 0));
    },
    sortByDate_born() {
      this.persons.sort((a, b) => (a.date_born < b.date_born ? -1 : 0));
    },
    sortByGender() {
      this.persons.sort((a, b) => (a.gender < b.gender ? -1 : 0));
    },
    clickApplyFilter() {
      if (this.age_from !== "" && this.age_to !== "") {
        this.persons = this.persons.filter(
          (item) => item.age > this.age_from && item.age < this.age_to
        );
      }
      if (this.date_from !== "" && this.date_to !== "") {
        this.persons = this.persons.filter(
          (item) =>
            item.date_born > this.date_from && item.date_born < this.date_to
        );
      }
      if (this.gender) {
        this.persons = this.persons.filter(
          (item) => item.gender === this.gender
        );
      }
    },
    clickCancelFilter() {
      this.gender = "";
      this.age_from = "";
      this.age_to = "";
      this.date_from = "";
      this.date_to = "";
      this.getPersonsDataFromApi();
    },
  },
};
</script>
<style lang="scss">
.v-persons__wrapper {
}
.v-persons__controls {
  display: flex;
  justify-content: center;
}
.v-persons__container {
  display: flex;
  justify-content: space-between;
}
.v-persons__filter {
  width: 200px;
  margin-top: 5px;
  display: flex;
  flex-direction: column;
  border-right: 1px solid gray;
  border-top: 1px solid gray;
  padding-right: 5px;
}

.age_input_controls {
  display: flex;
  justify-content: space-evenly;
}
.date_input_controls {
  text-align: center;
}
.style_input {
  width: 70px;
}
.filter__btn-controls {
  display: flex;
}
.apply_cancel_style {
  margin-top: 10px;
  width: 100px;
}
.v-persons__addNewPerson {
  margin-right: 10px;
}
.v-persons__cards {
  border-top: 1px solid gray;
  margin-top: 5px;
  padding: 3px;
}
.v-persons__no-cards {
  margin-left: 100px;
  margin-top: 50px;
}
.no-find-data {
  color: red;
  font-weight: bold;
}
.v-persons__pagination {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
.page {
  padding: 8px;
  border: 1px solid lightgray;
  margin-right: 8px;
  &:hover {
    background-color: gray;
    cursor: pointer;
  }
}
</style>
