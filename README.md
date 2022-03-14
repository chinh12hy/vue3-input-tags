#vue3-input-tags

A tags input component for Vue 3 with custom validation, templating...

[Demo & docs ](https://www.example.com)
#Install

```
npm i vue3-input-tags
```

#Usage

```vue
<template>
  <div>
    <vue3-input-tags :tags="tags"
                     placeholder="input tags" />
  </div>
</template>
```

```vue
<script>
import { defineComponent } from 'vue';
import Vue3InputTags from '@/vue3-input-tags.vue';

export default defineComponent({
  components: {
    Vue3InputTags
  },
  
  data() {
    return {
      tags: ['VUE', 'HTML', 'CSS']
    }
  },
})
</script>
```

