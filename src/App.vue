<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const listItems = ref([])
const name = ref("")
const input_content = ref("")
const input_category = ref(null)

const listItems_asc = computed(() => listItems.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

function addListItem() {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return
  }

  listItems.value.push({
    content: input_content.value,
    category: input_category.value,
    checked: false,
    createdAt: new Date().getTime()
  })
  
  input_category.value = null
  input_content.value = ""
}

function removeItem(item) {
  listItems.value = listItems.value.filter(i => i !== item )
  console.log("done")
}

watch(listItems, (newVal) => {
  localStorage.setItem('listItems', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal) || ''
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  listItems.value = JSON.parse(localStorage.getItem('listItems')) || []
})


</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
        What's on your mind, <input placeholder="add your name" type="text" v-model="name">
      </h1>
    </section>

    <section class="create-list">
      <h2>Create a shopping list</h2>
      <form @submit.prevent="addListItem" action="">
        <h3>add an item to shop</h3>
        <input placeholder="bread" type="text" v-model="input_content">

        <h3>Pick a category</h3>

        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="food" v-model="input_category">
            <span class="bubble food"></span>
            <div>Food</div>
          </label>
          <label>
            <input type="radio" name="category" id="category2" value="household" v-model="input_category">
            <span class="bubble household"></span>
            <div>Household</div>
          </label>
          <label>
            <input type="radio" name="category" id="category3" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal Care</div>
          </label>
          <label>
            <input type="radio" name="category" id="category4" value="pharmacy" v-model="input_category">
            <span class="bubble pharmacy"></span>
            <div>Pharmacy</div>
          </label>
          <label>
            <input type="radio" name="category" id="category5" value="pet" v-model="input_category">
            <span class="bubble pet"></span>
            <div>Pet Supplies</div>
          </label>
        </div>

        <input type="submit" value="Add item">
      </form>
    </section>

    <section class="shopping-list">
      <h3>My shopping list</h3>
      <div class="list">
        <div :class="`list-item ${item.checked && 'checked'}`" v-for="item in listItems_asc">
        <label>
          <input type="checkbox" v-model="item.checked">
          <span :class="`bubble ${item.category} box`"></span>
        </label>

        <div class="item-content">
          <input type="text" v-model="item.content">
        </div>

        <div class="actions">
          <button @click="removeItem(item)" class="delete">Remove item</button>
        </div>
        </div>
      </div>
    </section>

  </main>
</template>

