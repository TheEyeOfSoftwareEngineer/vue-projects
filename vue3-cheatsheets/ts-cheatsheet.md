## TypeScript

### Types
- String
```typescript
const myText: string = 'My Simple Text';
const myTextAgain: string = "My Simple Text";
const greeting: string = `Welcome back ${myName}!`;
```
- Number
```typescript
const myAge: number = 31;
const hexNumber: number = 0xf010d;
const binaryNumber: number = 0b1011;
const octalNumber: number = 0o744;
```
- Boolean
```typescript
const isTaskDone: boolean = false;
const isGreaterThen: boolean = 10 > 5;
```
- Arrays
```typescript
const primeNumbers: number[] = [1, 3, 5, 7, 11];
const switchInstructions: Array<boolean> = [true, false, false, true];
```
- Tuple
```typescript
let person: [string, number];
person = ['Heitor', 31];
console.log(`My name is ${person[0]} and I am ${person[1]} years old`);
```

- Enum
```typescript
enum ErrorLevel {
  Info,
  Debug,
  Warning,
  Error,
  Critical,
}
console.log(ErrorLevel.Error); // 3
console.log(ErrorLevel[3]); // Error

enum Color {
  Red = '#FF0000',
  Blue = '#0000FF',
  Green = '#00FF00',
}

enum Languages {
  JavaScript = 1,
  PHP,
  Python,
  Java = 10,
  Ruby,
  Rust,
  TypeScript,
}
console.log(Color.Red) // '#FF0000'
console.log(Languages.TypeScript) // 13

```

- Any
```typescript
let maybeIs: any = 4;
maybeIs = 'a string?';
maybeIs = true;
```

- Void
```typescript
function logThis(str: string): void{
  console.log(str);
}
```

- Objects
```typescript
interface IPerson {
  name: string;
  age: number;
}
const person: IPerson = {
  name: 'Heitor',
  age: 31, 
};

function greetingUser(user: {name: string, lastName: string}) {
  console.log(`Hello, ${user.name} ${user.lastName}`);
}


type Person = {
  name: string,
  age: number,
};
const person: Person = {
  name: 'Heitor',
  age: 31, 
};
console.log(`My name is ${person.name}, I am ${person.age} years old`);
```
- Functions
```typescript
const sumOfValues: (a:number, b:number): number = (a: number, b: number): number => a + b;

const complexFunction: (a: number) => (b:number) => number = (a: number):
   (b: number) => number => (b: number): number => a + b;

function foo(a: number, b:number): number{
  return a + b;
}
```

### Interfaces
```typescript
function greetingStudent(student: {name: string}) {
  console.log(`Hello ${student.name}`);
}
const newStudent = {name: 'Heitor'};
greetingStudent(newStudent);

interface IStudent {
  name: string;
  course?: string;
  readonly university: string;
}
function greetingStudent(student: IStudent) {
  console.log(`Hello ${student.name}`);
  if(student.course) {
    console.log(`Welcome to the ${student.course} semester`);
  }
}

const newStudent: IStudent = { name: 'Heitor', university: 'UDF' };
greetingStudent(newStudent);

```

### Decorators
```json
{
  "compilerOptions": {
      "target": "ES5",
      "experimentalDecorators": true
  }
}
```
```typescript
function classSeal(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@classSeal
class Animal {
  sound: string;
  constructor(sound: string) {
      this.sound = sound;
  }
  emitSound() {
      return "The animal says, " + this.sound;
  } 
}
```


### TS class
```typescript
export enum FoodChainType {
  Carnivorous = 'carnivorous',
  Herbivorous = 'herbivorous',
  Omnivorous = 'omnivorous',
}

interface IAnimal {
  name: string;
  sound?: string;
  family: string;
  foodChainType: FoodChainType;
}

interface IAnimalConstructor extends IAnimal{
}

interface IBasicAnimal extends IAnimal {
  whoAmI: () => void;
  makeSound: () => void;
}

export class Animal implements IBasicAnimal {
  public name: string;
  public sound: string;
  public family: string;
  public foodChainType: FoodChainType;
  constructor(params: IAnimalConstructor) {
    this.name = params.name;
    this.sound = params.sound || '';
    this.family = params.family;
    this.foodChainType = params.foodChainType;
  }
  public whoAmI(): void {
    console.log(`I am a ${this.name}, my family is ${this.family}.
    My diet is ${this.foodChainType}.`);
    if (this.sound) {
      console.log([...Array(2).fill(this.sound)].join(', '));
    }
  }
  public makeSound(): void {
    console.log(this.sound);
  } 
}

import {Animal, FoodChainType} from './Animal';
class Dog extends Animal {
  constructor() {
    super({
      name: 'Dog',
      sound: 'Wof!',
      family: 'Canidae',
      foodChainType: FoodChainType.Carnivorous,
    }); 
  }
}



```

### TS-vue-component with vue-class-component
```typescript
<script lang="ts">
import Vue from 'vue';
import Component from 'vue-class-component';
@Component
export default class Counter extends Vue {
  valueNumber: number = 0;
  get formattedNumber() {
    return `Your total number is: ${this.valueNumber}`;
  }
  increase() {
    this.valueNumber += 1;
  }
  decrease() {
    this.valueNumber -= 1;
  } 
}
</script>

<template>
  <div id="app">
    <Counter />
  </div>
</template>
<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator';
  import Counter from './components/Counter.vue';
  @Component({
    components: {Counter, },
  })
  export default class App extends Vue {
      
  }
</script>
<style lang="stylus">
  #app
    font-family 'Avenir', Helvetica, Arial, sans-serif
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
    text-align center
    color #2c3e50
    margin-top 60px
</style>
```

### custom mixin with vue-class- component
```typescript
<script lang="ts">
  import Vue from 'vue';
  import Component from 'vue-class-component';
  @Component
  export default class CounterByTen extends Vue {
    valueNumber: number = 0;
    get formattedNumber() {
      return `Your total number is: ${this.valueNumber}`;
    }
    increase() {
      this.valueNumber += 10;
    }
    decrease() {
      this.valueNumber -= 10;
    }
  } 
</script>

<template>
  <div id="app">
    <Counter />
    <hr />
    <CounterByTen />
  </div>
</template>

<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator';
  import Counter from './components/Counter.vue';
  import CounterByTen from './components/CounterByTen.vue';
  @Component({
    components: {Counter,CounterByTen,},
  })
  export default class App extends Vue {
  }
</script>

// mixin
import Vue from 'vue';
import Component from 'vue-class-component';
@Component
export default class DefaultNumber extends Vue {
  valueNumber: number = 0;
  get formattedNumber() {
    return `Your total number is: ${this.valueNumber}`;
  } 
}

<script lang="ts">
  import Vue from 'vue';
  import Component, { mixins } from 'vue-class-component';
  import DefaultNumber from '../mixins/defaultNumber';
  @Component
  export default class CounterByTen extends mixins(DefaultNumber) {
    increase() {
      this.valueNumber += 10;
    }
    decrease() {
      this.valueNumber -= 10;
    } 
  }
</script>

```

### custom function decorator with vue-class-component
```typescript
import { createDecorator } from 'vue-class-component';
import componentMountLogger from './componentLogger';
export default createDecorator((options) => {
  options.mixins = [...options.mixins,componentMountLogger];
});

// componentLogger.js
export default {
  mounted() {
    if (this.debug) {
      const componentName = this.name || '';
      console.log(`The ${componentName} was mounted successfully.`);
      const dataKeys = Object.keys(this.$data);
      if (dataKeys.length) {
        console.log('The base data are:');
        console.table(dataKeys);
        dataKeys.forEach((key) => {
          this.$watch(key, (newValue, oldValue) => {
            console.log(`The new value for ${key} is: ${newValue}`);
            console.log(`The old value for ${key} is: ${oldValue}`);
          }, {
            deep: true,
          });
        }); 
      }
    } 
  },
};


<template>
  <div>
    <fieldset>
      <legend>{{ this.formattedNumber }}</legend>
      <button @click="increase">Increase</button>
      <button@click="decrease">Decrease</button>
    </fieldset>
  </div>
</template>
<script lang="ts">
  import Vue from 'vue';
  import Component from 'vue-class-component';
  import componentMount from '../decorators/componentMount';
  @Component
  @componentMount
  export default class Counter extends Vue {
    valueNumber: number = 0;
    debug: boolean = true;
    get formattedNumber() {
      return `Your total number is: ${this.valueNumber}`;
    }
    increase() {
      this.valueNumber += 1;
    }
    decrease() {
      this.valueNumber -= 1;
    } 
  }
</script>
```

### custom hooks to vue-class- component
```typescript
import Component from 'vue-class-component';
Component.registerHooks([
  'beforeRouteEnter',
  'beforeRouteLeave',
]);

import './classComponentsHooks/vue-router';
import Vue from 'vue';
import App from './App.vue';
import router from './router';
Vue.config.productionTip = false;
new Vue({
  router,
  render: h => h(App),
}).$mount('#app');

<template>
  <div class="secure">
    <h1>This is an secure page</h1>
  </div>
</template>
           
<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator';
  import { Route, RawLocation } from 'vue-router';
  type RouteNext = (to?: RawLocation | false | ((vm: Vue) => any) | void) => void;
  @Component
  export default class Home extends Vue {
    beforeRouteEnter(to: Route, from: Route, next: RouteNext) {
      const securePassword = 'vuejs';
      const userPassword = prompt('What is the password?');
      if (userPassword === securePassword) {
        next();
      } else if (!userPassword) {
        next('/');
      } 
    }
    beforeRouteLeave(to: Route, from: Route, next: RouteNext) {
      alert('Bye!');
      next();
    } 
  }
</script>


import Vue from 'vue';
import Router from 'vue-router';
import Home from './views/Home.vue';
Vue.use(Router);
export default new Router({
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home,
    }, {
      path: '/about',
      name: 'about',
      component: () => import('./views/About.vue'),
    }, {
      path: '/secure',
      name: 'secure',
      component: () => import('./views/Secure.vue'),
    },
  ],
}); 

<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link> |
      <router-link to="/secure">Secure</router-link>
    </div>
    <router-view/>
  </div>
</template>

<style lang="stylus">
  #app
    font-family 'Avenir', Helvetica, Arial, sans-serif
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
      text-align center
      color #2c3e50
  #nav
    padding 30px
    a
      font-weight bold
      color #2c3e50
      &.router-link-exact-active
        color #42b983
</style>
```

### vue-property-decorator to vue-class- component
```typescript
// npm install -S vue-property-decorator

import { 
  Vue,
  Component,
  Prop,
} from 'vue-property-decorator';

@Component
export default class DefaultNumber extends Vue {
  valueNumber: number = 0;
  @Prop(Number) readonly value: number | undefined;
  get formattedNumber() {
    return `Your total number is: ${this.valueNumber}`;
  } 
}

import {
  Watch,
  Mixins,
} from 'vue-property-decorator';
import DefaultNumber from './defaultNumber';
export default class NumberWatchers extends Mixins(DefaultNumber) {
  @Watch('valueNumber')
  onValueNumberChanged(val: number) {
    this.$emit('input', val);
  }
  @Watch('value', { immediate: true })
  onValueChanged(val: number) {
    this.valueNumber = val;
  }
}

<template>
  <div>
    <fieldset>
      <legend>{{ this.formattedNumber }}</legend>
      <button @click="increase">Increase</button>
      <button @click="decrease">Decrease</button>
    </fieldset>
  </div>
</template>

<script lang="ts">
  import Vue from 'vue';
  import Component, { mixins } from 'vue-class-component';
  import NumberWatcher from '../mixins/numberWatcher';
  @Component
  export default class Counter extends mixins(NumberWatcher) {
    increase() {
      this.valueNumber += 1;
    }
    decrease() {
      this.valueNumber -= 1;
    } 
  }
</script>


<template>
  <div>
    <fieldset>
      <legend>{{ this.formattedNumber }}</legend>
      <button @click="increase">Increase By Ten</button>
      <button @click="decrease">Decrease By Ten</button>
    </fieldset>
  </div>
</template>
     
<script lang="ts">
  import Vue from 'vue';
  import Component, { mixins } from 'vue-class-component';
  import NumberWatcher from '../mixins/numberWatcher';
  @Component
  export default class CounterByTen extends mixins(NumberWatcher) {
    increase() {
      this.valueNumber += 10;
    }
    decrease() {
      this.valueNumber -= 10;
    } 
  }
</script>

<template>
  <div id="app">
    <Counter
      v-model="amount"
    />
    <hr />
    <CounterByTen
      v-model="amount"
    />
  </div>
</template>

<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator';
  import Counter from './components/Counter.vue';
  import CounterByTen from './components/CounterByTen.vue';
  @Component({
    components: {
      Counter,
      CounterByTen,
    },
  })
  export default class App extends Vue {
    amount: number = 0;
  }
</script>

<style lang="stylus">
  #app
    font-family 'Avenir', Helvetica, Arial, sans-serif
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
    text-align center
    color #2c3e50
    margin-top 60px
</style>
```


```typescript

```


