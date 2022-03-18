# vue3-input-tags

A tags input component for Vue 3 with custom validation, templating...

[Demo & docs ](https://vue3-input-tags.netlify.app)
# Install

```
npm i vue3-input-tags
```

# Usage

```vue
<template>
    <vue3-input-tags :tags="tags"
                     placeholder="input tags" />
</template>
```

```vue
<script>
import { defineComponent } from 'vue';
import Vue3InputTags from 'vue3-input-tags';

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

# License

[MIT](https://opensource.org/licenses/MIT)

Copyright (c) 2022 Chinh Pham Duc (chinh12hy@gmail.com)

