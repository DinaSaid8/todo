<script setup>
import { ref, onMounted, computed, watch } from 'vue'

// import { useDark,useToggle } from "@vueuse/core";
// const isDark = useDark();
// const toggleDark = useToggle(isDark);

const userTheme = ref("light-theme")
const setTheme = (theme) => {
  localStorage.setItem("user-theme", theme);
  userTheme.value = theme
  document.documentElement.className = theme;
}
const toggleTheme = () => {
  const activeTheme = localStorage.getItem("user-theme");
  if (activeTheme === "light-theme") {
    setTheme("dark-theme")
  } else {
    setTheme("light-theme")
  }
}
const getMediaPreference = () => {
  const hasDarkPreference = window.matchMedia("(prefers-color-scheme: dark)").matches;
  if (hasDarkPreference) {
    return "dark-theme";
  } else {
    return "light-theme";
  }
}

const todos = ref([])
const name = ref('')

const input_content = ref('')
// const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '') {
		return
	}
	todos.value.push({
		content: input_content.value,
		done: false,
		editable: false,
		createdAt: new Date().toLocaleString('en-US')
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((e) => e !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
	const currentTheme = localStorage.getItem('user-theme');
  if (currentTheme !== null) {
    setTheme(currentTheme);
  } else {
    const initUserTheme = getMediaPreference();
    setTheme(initUserTheme);
  }
})
</script>

<template>
	<main class="app container container-center">
		
		<div>
			<input
          @change="toggleTheme"
          id="checkbox"
          type="checkbox"
          class="switch-checkbox"
      />
	  <label for="checkbox" class="switch-label mt-4">
		<i class="bi bi-sun-fill text-warning sun-icon"></i>
		<i class="bi bi-moon-fill text-dark opacity-50 moon-icon"></i>

        <div
            class="switch-toggle"
            :class="{ 'switch-toggle-checked': userTheme === 'dark-theme' }"
        ></div>
      </label>

		</div>

		<section class="text-center">
			<h2 class="fs-2 fw-bold text-uppercase text-center">
				What would you like to do?<br>
			</h2>
		</section>

		<!-- add task -->
		<section class="create-todo container shadow-lg p-5">
			<h3 class="fw-bold d-inline"> Add New To-Do </h3><span class="fs-2">👌</span>

			<form id="new-todo-form" @submit.prevent="addTodo" class="mt-2">
				
					<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Enter new task ^^"
					v-model="input_content" />

				<button type="submit"
				  class=" form-control btn btn-outline-secondary fs-5" >
				 ADD 👆
				</button>
			</form>
		</section>
        <!-- display task -->
		<section class="todo-list container shadow-lg p-5">
			<h3 class="fw-bold text-capitalize">let's get some work done!</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc"
				 :class="`todo-item ${todo.done && 'done'} shadow-lg p-4`">
					
					<div class="d-flex justify-content-between 
					flex-xl-row flex-lg-row flex-md-row
					 flex-column
					 w-100 pt-3">
						<label>
							<input type="checkbox" v-model="todo.done" />
							<span :class="`bubble`"></span>
						</label>
						<div class="todo-content">
						<input type="text" v-model="todo.content" />
						<p> {{todo.createdAt}}</p>
					</div>
					<div class="">
						<button class="btn btn-outline-danger px-3 rounded shadow " 
						type="button"
						@click="removeTodo(todo)"
						>Delete ❌</button>

					</div>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
