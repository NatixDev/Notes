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

### 5. Show or hide
You can choose to only show an element in the DOM if a particular property of the Vue instance evaluates to true, using v-show:

### 6. When to use...

#### When to use methods

To react to some event happening in the DOM
To call a function when something happens in your component.
You can call a method from computed properties or watchers.

#### When to use computed properties

You need to compose new data from existing data sources
You have a variable you use in your template that’s built from one or more data properties
You want to reduce a complicated, nested property name to a more readable and easy to use one (but update it when the original property changes)
You need to reference a value from the template. In this case, creating a computed property is the best thing, because it’s cached.
You need to listen to changes of more than one data property

#### When to use watchers

You want to listen when a data property changes, and perform some action
You want to listen to a prop value change
You only need to listen to one specific property (you can’t watch multiple properties at the same time)
You want to watch a data property until it reaches some specific value and then do something






