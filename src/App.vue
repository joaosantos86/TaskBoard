<script setup>
import { Plus, X, Pencil, Search } from 'lucide-vue-next';
import { ref, computed, watch } from 'vue';
const search = ref('')
const edit = ref(null)
const tasks = ref( JSON.parse(localStorage.getItem('tarefas')) ??[
  {id: 1, desc: 'Study Vuejs', status: 'pending'},
  {id: 2, desc: 'Make todolist', status: 'pending'},
  {id: 3, desc: 'Deploy counter', status: 'done'},
])

const filterTasks = computed(() => {
  return tasks.value.filter(task =>
    task.desc.toLowerCase().includes(search.value.toLowerCase())
  )
})


const newTask = ref('')

function addTask () {
  if(!newTask.value.trim() || newTask.value.length > 16)  return
  if (edit.value !== null) {
    const task = tasks.value.find(t => t.id === edit.value)
    task.desc = newTask.value
    edit.value = null
  } else {
    tasks.value.push({
      id: tasks.value.length + 1,
      desc: newTask.value,
      status: 'pending'
    })
  }
  newTask.value = ''
}

function updateTask(task) {
  edit.value = task.id
  newTask.value = task.desc
}

function deleteTask(task) {
  const index = tasks.value.indexOf(task)
  tasks.value.splice(index, 1)
}

const pending = computed(() => {
  return tasks.value.filter(t => t.status === 'pending').length
})
const done = computed(() => {
  return tasks.value.filter(t => t.status === 'done').length
})

function checkbox(task) {
  task.status = task.status === 'pending' ? 'done' : 'pending'
}

watch(tasks, (newTasks) => {
  localStorage.setItem('tarefas', JSON.stringify(newTasks))
}, { deep: true }) //verifica todo tipo de mudança DENTRO do objeto


</script>

<template>
  <main>
    <div class="taskboard">
      <h1>Tasks Board</h1>
        <div class="input-new-task">
          <input v-model="newTask" class="newtask" type="text" placeholder="Add your task">
          <Plus @click="addTask()" class="plus" color="white" />
        </div>
        <span v-if="newTask === ''" style="font-size: 0.7rem; opacity: 50%;">You need to enter something.</span>
        <span v-if="newTask.length > 16" style="font-size: 0.7rem; opacity: 50%;">A task can have a maximum of 16 characters.</span>
        <div class="divsearch">
        <Search :size="20" class="search-icon"/>
        <input
          class="search"
          v-model="search"
          type="search"
          placeholder="Search your task"
        />
        </div>
        <div>
          <ul>
            <li v-for="task in filterTasks" :key="task.id">
              <input class="checkbox" :checked="task.status === 'done'" @click="checkbox(task)" type="checkbox">
              <span :class="{ done: task.status === 'done' }">{{ task.desc }}</span>
              <div class="icons">
                <Pencil @click="updateTask(task)" style="cursor: pointer; margin-right: 10px;" size="18"/>
                <X @click="deleteTask(task)" style="cursor: pointer;" size="20"/>
              </div>
            </li>
          </ul>
        </div>
        <div class="list">
        <p>pending: {{ pending }}</p>
        <p>done: {{ done }}</p>
        </div>
    </div>
  </main>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');

  * {
    font-family: 'Poppins';
  }
  main {
  display: flex;
  justify-content: center;
  padding: 20px;

}

  .taskboard {
    display: flex;
    flex-direction: column;
    align-content: center;
    justify-content: center;
    width: 100%;
    max-width: 500px;
  }

  span {
  flex: 1;
  text-align: left;
}

  h1 {
    font-weight: 700;
    color: #555555;
  }

  .input-new-task {
    display: flex;
    align-items: center;
    caret-color: #696969;
    width: 100%;
  }
  .newtask {
    border: none;
    background: none;
    border-bottom: solid 2px rgb(105, 105, 105);
    width: 100%;
    padding: 7px 0 7px 10px;
    transition: 0.2s ease;

  }
  .newtask:focus  {
    outline: none;
    box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.151);
  }

  .plus {
    background: rgb(87, 87, 87);
    border-radius: 12px;
    padding: 5px;
    margin-left: 10px;
    cursor: pointer;
    margin-bottom: 10px;
    transition: 0.2s ease;
  }

  .plus:hover {
    background: rgb(54, 54, 54);
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.404);
  }
  ul {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-top: 10px;

  }
  li {
    box-sizing: border-box;
    border: solid 1px rgba(0, 0, 0, 0.404);
    font-size: 1rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 10px;
    border-radius: 4px;
    gap: 10px;
    width: 100%;
    box-shadow: 2px 2px 3px #6d6d6d21;
    text-align: left;
  }

  .list {
    font-size: 0.8rem;
    display: flex;
    gap: 2vw;
    opacity: 50%;
  }

  .icons {
    margin-left: 12vw;
  }

  .search {
    border: solid 1px rgba(0, 0, 0, 0.432);
    border-radius: 4px;
    margin-top: 20px;
     padding: 10px 30px 10px 40px;
    transition: 0.2s ease;
  }
  .search:focus {
    outline: none;
    border: solid 1px rgba(0, 0, 0, 0.479);
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.212);
  }

  .search::-webkit-search-cancel-button {
    display: none;
  }

  .divsearch {
  position: relative;
  display: flex;
  align-items: center;
  }

  .search-icon {
  position: absolute;
  left: 15px;
  top: 29px;
  color: #999;
  }

  .checkbox {
  width: 18px;
  height: 18px;
  border: 2px solid #aaa;
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: 0.2s ease;
  flex-shrink: 0;
    accent-color: #000000;
}

.checkbox.checked {
  background: #000000;
  border-color: #555;
  color: white;
  font-size: 12px;
    accent-color: #000000;
}

.done {
  text-decoration: line-through;
  opacity: 50%;
}
</style>
