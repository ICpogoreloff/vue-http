<template>
  <div class="container">
    <app-alert :alert="alert" @close="alert = null"></app-alert>

    <form action="" class="card" @submit.prevent="createPerson">
      <h2>Работа с базой данных</h2>

    <div class="card">
      <label for="name">Введите имя: </label>
      <input type="text" id="name" v-model.trim="name">
    </div>

      <button class="btn primary" :disabled="name.length === 0">Создать человека</button>
    </form>

    <app-loader v-if="loading"></app-loader>

    <app-people-list
        v-else
        :people="people"
        @load="loadPeople"
        @remove="removePerson"
    ></app-people-list>
  </div>
</template>

<!--suppress ExceptionCaughtLocallyJS -->
<script>
import AppPeopleList from "@/Components/AppPeopleList"
import axios from 'axios'
import AppAlert from "@/Components/AppAlert"
import AppLoader from "@/Components/AppLoader"

export default {
  data() {
    return {
      name: '',
      people: [],
      alert: null,
      loading: false
    }
  },
  mounted() {
    this.loadPeople()
  },
  methods: {
    async createPerson() {
      const response = await fetch('https://vue-with-http-2b73d-default-rtdb.europe-west1.firebasedatabase.app/.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          firstName: this.name
        })
      })

      const firebaseData = await response.json()
      this.people.push({
        firstName: this.name,
        id: firebaseData.name
      })
      this.name=''
    },
    loadPeople() {
      this.loading = true
      setTimeout (  async () => {
      try {
        const {data} = await axios.get('https://vue-with-http-2b73d-default-rtdb.europe-west1.firebasedatabase.app/.json')
        if (!data) {
          throw new Error('Список пуст')
        }
        this.people = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
        this.loading = false
      } catch (e) {

        this.alert = {
          type: 'danger',
          title: 'Ошибка!',
          text: e.message
        }
        this.loading = false
      }
      }, 700)
    },
    async removePerson(id) {
      try {
        const name = this.people.find(person => person.id === id).firstName
        await axios.delete(`https://vue-with-http-2b73d-default-rtdb.europe-west1.firebasedatabase.app/${id}.json`) //обратные кавычки для динамики
        this.people = this.people.filter(person => person.id !== id)
        this.alert = {
          type: 'primary',
          title: 'Успешно!',
          text: `Пользователь с именем "${name}" был удален`
        }
      } catch (e) {

      }
    }
  },
  components: {
    AppPeopleList,
    AppAlert,
    AppLoader
  }
}
</script>

<style>

</style>
