# Filtes structure with Vue CLI 

index.html
src/App.vue
src/main.js
src/assets/logo.png
src/components/HelloWorld.vue

# Vue Notes

1. Installation et Initialisation

```yarn global add @vue/cli```

```vue create example```

2. Presets

A preset is a colliection of plugins.

Example of preset that you can push to a repo :

```{
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
}```

Then to use it from your repo :

```vue create --preset userName/vue-cli-preset appName```




