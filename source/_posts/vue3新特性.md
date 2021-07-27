---
title: vue3新特性
date: 2021-07-27 10:34:14
tags:
  - vue
  - 前端
---
# vue3新特性

+ Performance 性能
+ Tree-shaking support  把无用模块剪枝，不会被打包
+ Composition API
+ Fragment， Teleport， Suspense （原生）
+ Better Typescript support
+ Custom Renderer API

## Performance

+ 重写virtual dom
+ compiler-informed fast
+ more effcient component initialzation
+ better update performance
+ faster SSR

## Composition API

> 创建Composition API 的原因是：大型组件变得难以阅读和维护。逻辑复用。

Composition API带来的特性：

- 更好的typescript支持
- 更容易复用逻辑
- 需要开发者组织代码结构


## Vue2 and Vue3核心API对比

### 性能上的提升

- 核心体积更小 gzipped ~10kb
- 支持 three-shaking
- 静态节点优化

  *input*

  ```js
  <div>
    <span class="foo">
      Static
    </span>
    <span>
      {{ dynamic }}
    </span>
  </div>
  ```

  *output*

  ```js
  const __static1 = h(
    "span", 
    { class: "foo" },
    "static"
  );
  
  render() {
    return h('div', [
      __static1,
      h('span', dynamic)
    ])
  }
  ```

### 创建App

##### Vue2

```js
import Vue from 'vue'
import App from './App.vue'

new Vue({
  render: h => h(App),
}).$mount('#app')
```

##### Vue3

```js

import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

### 多个根节点

##### Vue2

```vue
<template>
  <div>
    <h1>{{ msg }}</h1>
    <button @click="count++">count is: {{ count }}</button>
    <p>Edit <code>components/HelloWorld.vue</code> to test hot module replacement.</p>
  </div>
</template>
```

##### Vue3

```vue
<template>
  <h1>{{ msg }}</h1>
  <button @click="count++">count is: {{ count }}</button>
  <p>Edit <code>components/HelloWorld.vue</code> to test hot module replacement.</p>
</template>
```

#### Vue3 setup

- Props
- Data
- Methods
- Computed Properties
- Lifecycle methods

```vue
<template>
  <!-- your template code -->
</template>
<script lang="ts">
import { ref, onMounted } from 'vue';

interface SetupContext {
  attrs: Data
  slots: Slots
  emit: (event: string, ...args: unknown[]) => void
}
 
export default {
  setup(props, context: SetupContext) {
  	onMounted(() => {})
  }
};
</script>
```


#### Vue2

```vue
<template>
  <!-- template code -->
</template>
<script>
export default {
  name: "App",
  props: {
  }
  components: {
  },
  data(){
    return {
    }
  },
  computed:{
  },
  watch:{
  },
  methods: {
  },
  created(){
  },
  mounted(){
  }
};
</script>
```

### Data

#### vue2

```vue
<template>
  <div id="app">
    <div>Name: {{ name }}</div>
  </div>
</template>

<script>
export default {
  name: "App",
  data(){
    return {
      name: "Name"
    }
  }
};
</script>
```

#### vue3

##### ref

```vue
<template>
  <div>Name: {{ name }}</div>
</template>
<script>
import { ref } from "vue";
export default {
  setup() {
    const name = ref('Name');
    console.log(name.value);
    return { name };
  }
};
</script>
```

##### reactive

```js
const obj = reactive({ count: 0 })
obj.count ++
console.log(obj.count) // 1
```

##### reactive and ref

```js
const count = ref(0);
const state = reactive({
  count,
});
// 不用state.count.value
console.log(state.count); // 0

```

**区别：其实ref相当于reactive的简化版，ref背后也是通过reactive实现的，唯一的区别是ref返回的是包装对象**

```js
const count = ref(0)  
// 等价于 
const count = reactive({value:0})
```


##### isRef

用来判断某个值是否为 ref() 创建出来的对象，应用场景：当需要展开某个可能为 ref() 创建出来的值的时候

```js
 const unwrapped = isRef(foo) ? foo.value : foo;
```

##### toRefs

```vue
<template>
  <p>{{ obj.count }}</p>
  <p>{{ count }}</p>
  <p>{{ value }}</p>
</template>

<script setup lang="ts">
export default {
  setup() {
    const obj = reactive({
      count: 0,
      value: 100,
    });
    return {
      obj,
      ...toRefs(obj),
    };
  },
}
</script>

```

##### Readonly

*接收一个ref或者reactive包装对象，返回一个只读的响应式对象*

```js
const original = reactive({ count: 0 })

const copy = readonly(original)

watchEffect(() => {
  // 用于响应性追踪
  console.log(copy.count)
})

// 变更 original 会触发依赖于副本的侦听器
original.count++

// 变更副本将失败并导致警告
copy.count++ // 警告!
```

### Methods

#### vue2

```vue
<template>
  <div>
    <div>Amount: {{ amount }}</div>
    <button @click="increaseAmount()">Increase Capacity</button>
  </div>
</template>

<script>
export default {
  data(){
    return {
      amount: 3,
    }
  },
  methods: {
    increaseAmount() {
      this.amount+=1
    }
  }
};
</script>
```

#### vue3

```vue
<template>
  <div>
    <div>Amount: {{ amount }}</div>
    <button @click="increaseAmount()">Increase Capacity</button>
  </div>
</template>

<script>
import { ref, reactive } from "vue";

export default {
  setup() {
    const amount = ref(3);
    const increaseAmount = () => {
      amount.value+=1
    }
    return { amount, increaseAmount };
  }
};
</script>
```

### Computed

#### vue2

```vue
<template>
  <div id="app">
    <p>Upper: {{ upperName }} out of {{ name }}</p>
  </div>
</template>
<script>
export default {
  data(){
    return {
      name: "author",
    }
  },
  computed: {
    upperName(){
      return this.name.toUpperCase() + "VUE_3";
    }
  }
};
</script>
```


#### Vue3

```vue
<template>
  <div id="app">
    <p>Upper: {{ upperName }} out of {{ name }}</p>
  </div>
</template>
<script>
import { ref, computed } from "vue";
export default {
  setup() {
    const name = ref("author");
    const upperName = computed(() => {
      return name.value.toUpperCase() + "VUE_3";
    });
    return { name, upperName };
  }
};
</script>
```

### Watch

#### Vue2

```vue
<template>
  <div>
    <input type="text" v-model="name" />
  </div>
</template>

<script>
export default {
  data(){
    return {
      name: null,
    }
  },
  watch: {
    name(newVal, oldVal){
      console.log(`${newVal} ${oldVal}`);
    }
  }
};
</script>
```

#### Vue3

```vue
<template>
  <div>
    <input type="text" v-model="name" />
  </div>
</template>

<script>
import { ref, watchEffect } from "vue";
export default {
  setup() {
    const name = ref('');
    watchEffect(() => {
      console.log(name.value);
    })
    return { name };
  }
};
</script>
```

### 删除了 filter

**删除原因：** 将鼓励开发者创建公用的函数来重用或在每个组件中建立明确的方法。

#### Vue2

```vue
<template>
  <div id="app">
    {{ new Date() | formatUnix }}
  </div>
</template>

<script>
import moment from "moment";
export default {
  name: "App",
  filters: {
    formatUnix(value) {
      if (value) {
        return moment(value).format("DD/MM/YYYY");
      }
    },
  },
};
</script>
```

#### Vue3

```vue
<template>
  <div id="app">
    <div>{{new Date |> formatUnix> }}</div>
  </div>
</template>

<script>
import formatUnix from "./Utils/DateFormat.js";
export default {
  name: "App",
  components: {
  },
  setup() {
    return {
      formatUnix 
    };
  },
};
</script>
```

### 多个v-models

#### Vue2

```js
<input v-model="property" />

<input                          
  :value="property"                         
  @input="property = $event.target.value"                       
/>
```

#### Vue3

```vue
<template>
  <form>
    <div>
      <label for="name">Name</label>
      <input type="text" :value="name" @input="updateName($event.target.value)" />
    </div>
    <div>
      <label for="email">Email</label>
      <input type="email" :value="email" @input="updateEmail($event.target.value)" />
    </div>
  </form>
</template>
<script>
export default {
  props: {
    name: String,
    email: String,
  },
  setup(props, { emit }) {
    const updateName = (value) => {
      emit("update:name", value);
    };
    const updateEmail = (value) => {
      emit("update:email", value);
    };
    return { updateName, updateEmail };
  },
};
</script>
```

```vue
<template>
  <div id="app">
    <InviteForm v-model:name="inviteName" v-model:email="inviteEmail" />
    <div>
      <div>{{ inviteName }}</div>
      <div>{{ inviteEmail }}</div>
    </div>
  </div>
</template>

<script>
import InviteForm from "./components/InviteForm.vue";
import { ref } from "vue";
export default {
  name: "App",
  components: {
    InviteForm,
  },
  setup() {
    const inviteName = ref("name");
    const inviteEmail = ref("invite");
    return {
      inviteName,
      inviteEmail,
    };
  },
};
</script>
```

### Teleport

**相当于react的Portals**

```vue
<template>
  <teleport to="#out-side-app">
  	This is teleport
  </teleport>
  <div>APP</div>
</template>Z
```


**支持多个teleport**

```vue
<template>
  <teleport to="#modals">
    <div>A</div>
  </teleport>
  <teleport to="#modals">
    <div>B</div>
  </teleport>
</template>

```
```js
  <!-- result-->
  <div id="modals">
    <div>A</div>
    <div>B</div>
  </div>
```

### 生命周期

- `beforeCreate` ->  `setup()`
- `created` ->  `setup()`
- `beforeMount` -> `onBeforeMount`
- `mounted` -> `onMounted`
- `beforeUpdate` -> `onBeforeUpdate`
- `updated` -> `onUpdated`
- `beforeUnmount` -> `onBeforeUnmount`
- `unmounted` -> `onUnmounted`
- `errorCaptured` -> `onErrorCaptured`


### script-setup

```vue
<template>
  <button @click="inc">{{ count }}</button>
</template>

<script setup lang="ts">
import { ref } from "vue";
const count = ref(0);
const inc = () => count.value++;
</script>
```


### ref-sugar

https://github.com/vuejs/rfcs/pull/228

```vue
<template>
  <button @click="inc">{{ count }}</button>
</template>

<script setup lang="ts"> // =>  lang="vs"
ref: count = 0;
const inc = () => count++;
</script>
```

*社区中其他的方案？？？*

```js
  $variable
  // @ref
  let count = 1;

  let @ref count = 1;

  ref count = 1;
```


