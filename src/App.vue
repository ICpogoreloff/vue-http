<template>
  <div class="container">
    <form action="" class="card" @submit.prevent="createPerson">
      <h2>Работа с базой данных</h2>

    <div class="card">
      <label for="name">Введите имя: </label>
      <input type="text" id="name" v-model.trim="name">
    </div>

      <button class="btn primary" :disabled="name.length === 0">Создать человека</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: ''
    }
  },
  methods: {
    createPerson() {
      // this.name
    }
  }
}
</script>

<style>

</style>
