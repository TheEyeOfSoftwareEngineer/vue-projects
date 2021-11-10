## Vue3 cheatsheet

### from vue2 to vue3
- main.js
```typescript
import { createApp } from 'vue'
createApp(app)
  .use(router)
  .use(store)
  .mount('#app')
```
- store.js
```typescript
import { createStore } from 'vuex'
export default createStore({
  state: {...},
  mutations: {...},
  actions: {...},
  getters: {...},
  modules: {...},
})
```
- routers.js
```typescript
import {
  createRouter,
  createWebHistory
} from 'vue-router'

Vue.use(VueRouter)

export default createRouter({
  history: createWebHistory(),
  routes: [{
    path: '/',
    name: 'HomePage',
    component: () => import("pages/home"),
  }]
})
```

### creating components with multiple root elements
```typescript
const {
  defineComponent,
  createApp,
} = Vue

const component = defineComponent({
  data: () => ({
    count: 0
  }),
  methods: {
    addOne() {
      this.count += 1
    }
  },
  template: `
    <h1>This is a Vue 3 root element</h1>
    <button @click="addOne">
      Pressed {{count}} times.
    </button>
  `
})

createApp(component)
  .mount('#app')
```

### Creating the component with the render function
```typescript
const {
  defineComponent,
  h,
  createApp,
} = Vue

const component = defineComponent({
  data: () => ({
    count: 0,
  }),
  methods: {
    addOne() {
      this.count += 1
    }
  },
  render() {
    const h1 = h('h1', 'This ia a Vue 3 component element')
    const button = h('button', {
      onClick: this.addOne,
    }. `Press ${this.count} times`)
    return [
      h1,
      button,
    ]
  },
})

createApp(component)
  .mount('#app')
```
### Creating components with attribute inheritance
```typescript
const {
  defineComponent,  
  createApp,
} = Vue

const nameInput = defineComponent({
  name: 'NameInput',
  props: {
    modelValue: String,
  },
  inheritAttrs: false,
  template: `
    <label>
      <input 
        v-bind="{...$attrs,}"
        :value="modelValue"
        type="text"
        @change="(event) => $emit('update:modelValue',event.target.value)"
      /> 
    </label>
  `
});

const component = defineComponent({
  data: () => ({
    name: '',
  }),
  template: `
    <name-input
      v-model="name"
      style="border:0; border-bottom: 2px solid red;"
      data-test="name-input"
    />
  `,
});

const app = createApp(component);
app.component('NameInput', nameInput);
app.mount('#app');
```

### Using the reactivity and observable API outside the scope of Vue
```typescript
const {
  reactive,
  watch,
} = Vue;

const mod = (a, b) => (a % b)
const maxRoadLength = 50
const competitor = reactive({
  position: 0,
  speed: 1,
})

watch(() => {
  const street = Array(maxRoadLength).fill('_');
  const marathonEl = document.getElementById('marathon');
  street[competitor.position] = (competitor.position % 2 === 1) ? 'run' : 'walk'
  marathonEl.innerHTML = ''
  marathonEl.innerHTML = street.reverse().join('')
})

setInterval(() => {
  competitor.position = mod(competitor.position+competitior.speed, maxRoadLength)
}, 100)

```
### Creating a component using the composition API
```typescript
const {
  createApp,
  defineComponent,
  setup,
  ref,
  onMounted,
  onUnmounted,
} = Vue;

function fetchLocation() {
  let watcher;
  const geoLocation = navigator.geolocation;
  const gpsTime = ref(Date.now())
  const coordinates = ref({
    accuracy: 0,
    latitude: 0,
    longitude: 0,
    altitude: 0,
    altitudeAccuracy: 0,
    heading: 0,
    speed: 0,
  })

  function setPosition(payload) {
    gpsTime.value = payload.timestamp
    coordinates.value = payload.coords
  }

  onMounted(() => {
    if(geoLocation) {
      watcher = geoLocation.watchPosition(setPosition)
    }
  })

  onUnmounted(() => {
    if (watcher) geoLocation.clearWatch(watcher);
  });

  return {
    coordinates,
    gpsTime,
  };
}

const appComponent = defineComponent({
  setup() {
    const {
      coordinates,
      gpaTime,
    } = fetchLocation()
    const formatOptions = {
      year: 'numeric',
      month: 'numeric',
      day: 'numeric',
      hour: 'numeric',
      minute: 'numeric',
      hour12: true,
    }
    const formatDate = (date) => (
      new Intl.DateTimeFormat(navigator.language, formatOptions).format(date)
    )
    return {
      coordinates,
      gpsTime,
      formatDate
    }
  },
  template: `
    <h1>My Geo Position at {{formatDate(new Date(gpsTime))}}</h1>
    <ul>
      <li>Latitude: {{ coordinates.latitude }}</li>
      <li>Longitude: {{ coordinates.longitude }}</li>
      <li>Altitude: {{ coordinates.altitude  }}</li>
    </ul>
  `
})

createApp(appComponent)
  .mount('#app')
```

```typescript

```