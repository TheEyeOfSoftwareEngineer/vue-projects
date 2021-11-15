## data-binding, form validation, events and computed property 
```typescript
<template>
  <div class='cardBox'>
    <div class='container'>
      <h2>Today is:</h2>
      <h3>{{ getCurrentDate }}</h3>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'CurrentTime',
    computed: {
      getCurrentDate() {
        const browserLocale =
          navigator.languages && navigator.languages.length
            ? navigator.languages[0]
            : navigator.language;
        const intlDateTime = new Intl.DateTimeFormat(browserLocale, {
            year: 'numeric',
            month: 'numeric',
            day: 'numeric',
            hour: 'numeric',
            minute: 'numeric'
        });
        return intlDateTime.format(new Date());
      }
    } 
  };
</script>

<template>
  <div id='app'>
    <current-time />
  </div>
</template>

<script>
  import CurrentTime from './components/CurrentTime.vue';
  export default {
    name: 'app',
    components: {
    CurrentTime
    }
  } 
</script>

```

### an input form with two-way data binding
```typescript
<template>
  <div class='cardBox'>
    <div class='container tasker'>
      <strong>My task is: {{ task }}</strong>
      <input
        type='text'
        v-model='task'
        class='taskInput' />
    </div>
  </div>
</template>

<script>
export default {
  name: 'TaskInput',
  data: () => ({
    task: '', 
  }),
}; 
</script>

<template>
  <div id='app'>
    <current-time class='col-4' />
    <task-input class='col-6' />
  </div>
</template>
            
<script>
  import CurrentTime from './components/CurrentTime.vue';
  import TaskInput from './components/TaskInput';
  export default {
    name: 'app',
    components: {
      CurrentTime,
      TaskInput, 
    }
  } 
</script>
```

### Adding an event listener to an element
```typescript
<template>
  <div class='cardBox'>
    <div class='container tasker'>
      <strong>My task is:</strong>
      <input
        type='text'
        v-model='task'
        class='taskInput' />
      <button
        v-on:click='addTask'>
            Add Task
      </button>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'TaskInput',
    data: () => ({
      task: '', 
    }),
    methods: {
      addTask(){
        this.$emit('add-task', this.task);
        this.task = '';
      },
    } 
  };
</script>


<template>
  <div id='app'>
    <current-time class='col-4' />
    <task-input
      class='col-6'
      @add-task='addNewTask'
    />
  </div>
</template>


<script>
  import CurrentTime from './components/CurrentTime.vue';
  import TaskInput from './components/TaskInput';
  export default {
    name: 'app',
    components: {
      CurrentTime,
      TaskInput,
    },
    methods:{
      addNewTask(task){
        alert(`New task added: ${task}`);
      },
    }, 
  }
</script>
```

### Removing the v-model from the input
```typescript
<template>
  <div class='cardBox'>
    <div class='container tasker'>
      <strong>My task is:</strong>
      <input
        type='text'
        :value='task'
        @input='task = $event.target.value'
        class='taskInput'
      />
      <button v-on:click='addTask'>
        Add Task
      </button>
    </div>
  </div>
</template>
```

### Creating a dynamic to-do list
```typescript
<script>
  import CurrentTime from './components/CurrentTime.vue';
  import TaskInput from './components/TaskInput';
  export default {
    name: 'TodoApp',
    components: {
      CurrentTime,
      TaskInput,
    },
    data: () => ({
      taskList: [],
    }),
    methods:{
      addNewTask(task){
        this.taskList.push({
          task,
          createdAt: Date.now(),
          finishedAt: undefined,
        }) 
    },
  }, 
}
</script>


<template>
  <div id='app'>
    <current-time class='col-4' />
    <task-input class='col-6' @add-task='addNewTask' />
    <div class='col-12'>
      <div class='cardBox'>
        <div class='container'>
          <h2>My Tasks</h2>
          <ul class='taskList'>
            <li
              v-for='(taskItem, index) in taskList'
              :key='`${index}_${Math.random()}`'
            >
              <input type='checkbox'
                :checked='!!taskItem.finishedAt'
                @input='changeStatus(index)'
              />
              {{ taskItem.task }}
              <span v-if='taskItem.finishedAt'>
                {{ taskItem.finishedAt }}
              </span>
            </li>
          </ul> 
        </div>
      </div>
    </div>
  </div>
</template>

changeStatus(taskIndex){
  const task = this.taskList[taskIndex];
    if(task.finishedAt){
      task.finishedAt = undefined;
    } else {
      task.finishedAt = Date.now();
  } 
}
```

### Creating computed properties and understanding how they work
```typescript
<script>
  import CurrentTime from './components/CurrentTime.vue';
  import TaskInput from './components/TaskInput';
  export default {
    name: 'TodoApp',
      components: {
        CurrentTime,
        TaskInput 
      },
      data: () => ({
        taskList: []
      }),
      computed: {
        displayList(){
          return this.taskList;
        },
      },
      methods: {
        addNewTask(task) {
          this.taskList.push({
            task,
            createdAt: Date.now(),
            finishedAt: undefined
          });
        },
        changeStatus(taskIndex){
          const task = this.taskList[taskIndex];
          if(task.finishedAt){
            task.finishedAt = undefined;
          } else {
            task.finishedAt = Date.now();
          }
        } 
      }
    }; 
</script>

<template>
  <div id='app'>
    <current-time class='col-4' />
    <task-input class='col-6' @add-task='addNewTask' />
    <div class='col-12'>
      <div class='cardBox'>
        <div class='container'>
          <h2>My Tasks</h2>
          <ul class='taskList'>
            <li
              v-for='(taskItem, index) in displayList'
              :key='`${index}_${Math.random()}`'
            >
              <input type='checkbox'
                :checked='!!taskItem.finishedAt'
                @input='changeStatus(index)'
              />
              {{ taskItem.task }}
              <span v-if='taskItem.finishedAt'>
                {{ taskItem.finishedAt }}
              </span>
            </li> 
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
```

### Displaying cleaner data and text with custom filters
```typescript
<script>
  import CurrentTime from './components/CurrentTime.vue';
  import TaskInput from './components/TaskInput';
  export default {
    name: 'TodoApp',
    components: {
      CurrentTime,
      TaskInput 
    },
    data: () => ({
      taskList: []
    }),
    computed: {
      displayList() {
        return this.taskList;
      } 
    },
    methods: {
      formatDate(value) {
        if (!value) return '';
        if (typeof value !== 'number') return value;
        const browserLocale =
          navigator.languages && navigator.languages.length
            ? navigator.languages[0]
            : navigator.language;
        const intlDateTime = new Intl.DateTimeFormat(browserLocale,
          {
            year: 'numeric',
            month: 'numeric',
            day: 'numeric',
            hour: 'numeric',
            minute: 'numeric'
          });
        return intlDateTime.format(new Date(value));
      },
      addNewTask(task) {
        this.taskList.push({
          task,
          createdAt: Date.now(),
          finishedAt: undefined
        });
      },
      changeStatus(taskIndex) {
        const task = this.taskList[taskIndex];
        if (task.finishedAt) {
          task.finishedAt = undefined;
        } else {
          task.finishedAt = Date.now();
        }
      }
    }
  }; 
</script>

<template>
  <div id='app'>
    <current-time class='col-4' />
    <task-input class='col-6' @add-task='addNewTask' />
    <div class='col-12'>
      <div class='cardBox'>
        <div class='container'>
          <h2>My Tasks</h2>
          <ul class='taskList'>
            <li
              v-for='(taskItem, index) in displayList'
              :key='`${index}_${Math.random()}`'
            >
              <input type='checkbox'
                :checked='!!taskItem.finishedAt'
                @input='changeStatus(index)'
              />
              {{ taskItem.task }}
              <span v-if='taskItem.finishedAt'> |
                Done at:
                {{formatDate(taskItem.finishedAt)}}
              </span>
            </li> 
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
```

### Adding form validation with Vuelidate
```typescript
import Vue from 'vue';
import App from './App.vue';
import Vuelidate from 'vuelidate';
import './style.css';
Vue.config.productionTip = false
Vue.use(Vuelidate);
new Vue({
  render: h => h(App),
}).$mount('#app')

<script>
  export default {
    name: 'TaskInput',
    data: () => ({
      task: '' 
    }),
    methods: {
      addTask() {
        this.$emit('add-task', this.task);
        this.task = '';
      }
    },
    validations: {
      task: {} 
    }
  }; 
</script>

<script>
  import { required, minLength } from 'vuelidate/lib/validators';
  export default {
    name: 'TaskInput',
    data: () => ({
      task: '' 
    }),
    methods: {
      addTask() {
        this.$emit('add-task', this.task);
        this.task = '';
      }
    },
    validations: {
      task: {
        required,
        minLength: minLength(5),
      } 
    }
  }; 
</script>

addTask() {
  this.$v.task.$touch();
  if (this.$v.task.$error) return false;
  this.$emit('add-task', this.task);
  this.task = '';
  this.$v.task.$reset();
  return true;
}

<template>
  <div class='cardBox'>
    <div class='container tasker'>
      <strong>My task is:</strong>
      <input
        type='text'
        :value='task'
        @input='task = $event.target.value'
        class='taskInput'
        :class="$v.task.$error ? 'fieldError' : ''"
      />
      <button v-on:click='addTask'>Add Task</button>
    </div>
  </div>
</template>

.fieldError {
  border: 2px solid red !important;
  color: red;
  border-radius: 3px;
}
```

### Creating filters and sorters for a list
```typescript
<script>
  import CurrentTime from "./components/CurrentTime.vue";
  import TaskInput from "./components/TaskInput";
  export default {
    name: "TodoApp",
    components: {
      CurrentTime,
      TaskInput 
    },
    data: () => ({
      taskList: [],
    }),
    computed: {
      baseList() {
        return [...this.taskList]
          .map((t, index) => ({
              ...t,
              id: index + 1
            }));
      },
      filteredList() {
        return [...this.baseList]
              .filter(t => !t.finishedAt);
      },
      sortedList() {
        return [...this.filteredList]
            .sort((a, b) => b.id - a.id);
      },
      displayList() {
        return this.sortedList;
      }
    }, 
    methods: {
      formatDate(value) {
        if (!value) return "";
        if (typeof value !== "number") return value;
        const browserLocale =
          navigator.languages && navigator.languages.length
            ? navigator.languages[0]
            : navigator.language;
        const intlDateTime = new Intl.DateTimeFormat(browserLocale, {
          year: "numeric",
          month: "numeric",
          day: "numeric",
          hour: "numeric",
          minute: "numeric"
        });
        return intlDateTime.format(new Date(value));
      },
      addNewTask(task) {
        this.taskList.push({
          task,
          createdAt: Date.now(),
          finishedAt: undefined
        }); 
      },
      changeStatus(taskIndex) {
        const task = this.taskList[taskIndex];
        if (task.finishedAt) {
          task.finishedAt = undefined;
        } else {
          task.finishedAt = Date.now();
        } 
      }
    }
  };
</script>

<template>
  <div id="app">
    <current-time class="col-4" />
    <task-input class="col-6" @add-task="addNewTask" />
    <div class="col-12">
      <div class="cardBox">
        <div class="container">
          <h2>My Tasks</h2>
          <ul class="taskList">
            <li
              v-for="(taskItem, index) in displayList"
              :key="`${index}_${Math.random()}`"
            >
              <input type="checkbox"
                :checked="!!taskItem.finishedAt"
                @input="changeStatus(taskItem.id)"
              />
              #{{ taskItem.id }} - {{ taskItem.task }}
              <span v-if="taskItem.finishedAt"> |
                Done at:
                {{ formatDate(taskItem.finishedAt) }}
              </span>
            </li> 
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>


changeStatus(taskId) {
  const task = this.taskList[taskId - 1];
  if (task.finishedAt) {
    task.finishedAt = undefined;
  } else {
    task.finishedAt = Date.now();
  }
}
```

### Creating conditional filters to sort list data
```typescript
<script>
  import CurrentTime from "./components/CurrentTime.vue";
  import TaskInput from "./components/TaskInput";
  export default {
    name: "TodoApp",
    components: {
      CurrentTime,
      TaskInput 
    },
    data: () => ({
      taskList: [],
      hideDone: false,
      reverse: false,
      sortById: false,
    }),
    computed: {
      baseList() {
        return [...this.taskList]
          .map((t, index) => ({
              ...t,
              id: index + 1
            }));
      },
      filteredList() {
        return this.hideDone
          ? [...this.baseList]
              .filter(t => !t.finishedAt)
          : [...this.baseList];
      },
      sortedList() {
        return [...this.filteredList]
            .sort((a, b) => (
              this.sortById
            ? b.id - a.id
            : (a.finishedAt || 0) - (b.finishedAt || 0)
        )); 
      },
      displayList() {
        const taskList = [...this.sortedList];
        return this.reverse
        ? taskList.reverse()
        : taskList;
      } },
      methods: {
        formatDate(value) {
          if (!value) return "";
          if (typeof value !== "number") return value;
          const browserLocale =
            navigator.languages && navigator.languages.length
              ? navigator.languages[0]
              : navigator.language;
          const intlDateTime = new Intl.DateTimeFormat(browserLocale, {
            year: "numeric",
            month: "numeric",
            day: "numeric",
            hour: "numeric",
            minute: "numeric"
          });
          return intlDateTime.format(new Date(value));
        },
        addNewTask(task) {
          this.taskList.push({
            task,
            createdAt: Date.now(),
            finishedAt: undefined
        }); },
        changeStatus(taskId) {
          const task = this.taskList[taskId - 1];
          if (task.finishedAt) {
            task.finishedAt = undefined;
          } else {
            task.finishedAt = Date.now();
          }
        } 
      }
    }; 
</script>


<template>
  <div id="app">
  <current-time class="col-4" />
  <task-input class="col-6" @add-task="addNewTask" />
  <div class="col-12">
  <div class="cardBox">
    <div class="container">
      <h2>My Tasks</h2>
      <hr />
      <div class="col-4">
        <input
          v-model="hideDone"
          type="checkbox"
          id="hideDone"
          name="hideDone"
        />
        <label for="hideDone">
          Hide Done Tasks
        </label>
      </div>
      <div class="col-4">
        <input
          v-model="reverse"
          type="checkbox"
          id="reverse"
          name="reverse"
        />
        <label for="reverse">
          Reverse Order
        </label>
      </div>
      <div class="col-4">
        <input
          v-model="sortById"
          type="checkbox"
          id="sortById"
          name="sortById"
        />
        <label for="sortById">
          Sort By Id
        </label>
      </div>
      <ul class="taskList">
        <li
          v-for="(taskItem, index) in displayList"
          :key="`${index}_${Math.random()}`"
        >
          <input type="checkbox"
            :checked="!!taskItem.finishedAt"
            @input="changeStatus(taskItem.id)"
          />
          #{{ taskItem.id }} - {{ taskItem.task }}
          <span v-if="taskItem.finishedAt"> |
            Done at:
            {{ formatDate(taskItem.finishedAt) }}
          </span>
        </li> 
      </ul>
    </div>
  </div>
</div>
</div>
</template>
```

### Adding custom styles and transitions
