---
layout: post
title:  "vue-cli 설치 및 기본 프로젝트 설명"
date:   2019-08-06
category: "Javascript"
tags: [vue-cli, vue, javascript]
---

[vue-cli 홈페이지](https://cli.vuejs.org)

# installation and create project

* `npm install -g @vue/cli`
* `vue create <project-name>` 후
    * default / manual 중 고른다.
    * manual에서 router / vuex를 골라보자..
* `vuetify` plugin 설치
    * `vue add vuetify`
    * 잘 모르니 Default로...

# tree structure

위에 적힌대로 따라하면 프로젝트가 다음 구조로 생성되었을 것이다. `node_modules`는 너무 폴더가 많아서
지웠다. 하나하나 살펴보자!

```
  .
  ├── README.md
  ├── babel.config.js
  ├── node_modules
  ├── package-lock.json
  ├── package.json
  ├── public
  │   ├── favicon.ico
  │   └── index.html
  └── src
      ├── App.vue
      ├── assets
      │   ├── logo.png
      │   └── logo.svg
      ├── components
      │   └── HelloWorld.vue
      ├── main.js
      ├── plugins
      │   └── vuetify.js
      ├── router.js
      ├── store.js
      └── views
          ├── About.vue
          └── Home.vue
```

## `main.js`

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import vuetify from './plugins/vuetify';

Vue.config.productionTip = false

new Vue({
  router,
  store,
  vuetify,
  render: h => h(App)
}).$mount('#app')
```

별거 없고, Vue 객체를 만들고, router, store(vuex), vutify를 같이 인자로 하여 app에 mount한다. 
`public/index.html`에 있는 `<div id="app"></div>` 여기에 mount될 것이다.
rendering할 것은 App이기 때문에 App.vue를 살펴보도록 한다.

## `App.vue`

```html
<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Vuetify</span>
        <span class="font-weight-light">MATERIAL DESIGN</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        text
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
      >
        <span class="mr-2">Latest Release</span>
      </v-btn>
    </v-app-bar>

    <v-content>
      <HelloWorld/>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data: () => ({
    //
  })
}
</script>
```

template을 보면

* toolbar
	* title과 button
* content

로 이루어짐을 알 수 있으며, content에는 HelloWorld.Vuew를 가져와서 뿌려주는 것을 알 수 있다.

## `router.js`

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import Home from './views/Home.vue'

Vue.use(Router)

export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home
    },
    {
      path: '/about',
      name: 'about',
      // route level code-splitting
      // this generates a separate chunk (about.[hash].js) for this route
      // which is lazy-loaded when the route is visited.
      component: () => import(/* webpackChunkName: "about" */ './views/About.vue')
    }
  ]
})
```

router는 home과 about page가 있으며, 각각 path를 통해 routing을 하는 것을 볼 수 있다.
이제 위의 `App.vue`에서 path를 바꿔서 routing을 해보도록 하자!

## new `App.vue`

```html
  <template>
    <v-app>
      <v-app-bar app>
        <v-toolbar-title class="headline text-uppercase">
          <span>Vuetify</span>
          <span class="font-weight-light">MATERIAL DESIGN</span>
        </v-toolbar-title>
        <v-spacer></v-spacer>
        <!-- (1) -->
        <v-btn text href="/">
          <span class="mr-2">Home</span>
        </v-btn>
        <v-btn text href="/about">
          <span class="mr-2">About</span>
        </v-btn>
        <!-- (1) 끝 -->
      </v-app-bar>

      <v-content>
        <!-- (2) -->
        <router-view/>
        <!-- (2) 끝 -->
      </v-content>
    </v-app>
  </template>

  <script>

  export default {
    name: 'App',
    data: () => ({
      //
    })
  }
  </script>
```

1. 버튼을 누르면 `route.js`에 정의된 path로 넘어가게 만들어주고
2. 뿌릴 content를 `router-view`로 바꾸면 된다.

# 정리
1. vue-cli를 설치하고, 프로젝트를 만들어보았다.
2. 프로젝트에 vuex, vuetify, route 를 설치하고 이용해보았다.
    * vuex는 [여기](https://vuex.vuejs.org/kr/)를 참조하자.
