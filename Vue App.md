# Filtes structure with Vue CLI 

index.html
src/App.vue
src/main.js
src/assets/logo.png
src/components/HelloWorld.vue

## Vue Notes

### 1. Installation et Initialisation

```yarn global add @vue/cli```

```vue create example```

### 2. Presets

A preset is a colliection of plugins.

Example of preset that you can push to a repo :

```
{
  "useConfigFiles": true,
  "plugins": {
    "@vue/cli-plugin-babel": {},
    "@vue/cli-plugin-eslint": {
      "config": "prettier",
      "lintOn": [
        "save"
      ]
    },
    "@vue/cli-plugin-unit-jest": {}
  },
  "router": true,
  "vuex": true
}
```

Then to use it from your repo :

```vue create --preset userName/vue-cli-preset appName```

### 3. Components

Using new Vue() or Vue.component() is the standard way to use Vue when you're building an application that is not a Single Page Application (SPA).

### 4. CSS

```
<template>
  <p :class="{underline: isUnderlined, weight: isBold}">Hi!</p>
</template>
<script>
export default {
  data() {
    return {
      isUnderlined: true,
      isBold: true
    }
  }
}
</script>
<style>
.underline { text-decoration: underline; }
.weight { font-weight: bold; }
</style>
```






