<template>
  <div id="app">
    <div class="form">
<!--      npm publish-->
      <vue3-input-tags placeholder="add tags"
                       :limit="2"
                       :tags="tags"
                       :validate="csValidate"
                       @on-focus="handleFocus"
                       @on-blur="handleBlur"
                       @on-remove="handleRemove"
                       @on-tags-change="handleChangeTag">
        <template #item="{ name, index }">
          {{ name }}
        </template>
      </vue3-input-tags>
      <br>
      <div class="custom-input-tags">
        <vue3-input-tags :tags="tags"
                         placeholder="custom input tags" />
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import Vue3InputTags from '@/vue3-input-tags.vue';

export default defineComponent({
  name: 'ServeDev',

  components: {
    Vue3InputTags
  },

  data() {
    return {
      tags: ['VUE', 'HTML', 'CSS']
    }
  },

  methods: {
    handleChangeTag(tags) {
      console.log('tags: ', tags);
      this.tags = tags;
    },

    csValidate(value) {
      const regex = new RegExp(/^[a-zA-Z]+$/);
      return regex.test(value)
    },

    handleFocus(event) {
      console.log('focus', event)
    },
    handleBlur(event) {
      console.log('blur', event)
    },
    handleRemove(index) {
      console.log('on remove', index)
    },
  }
});
</script>

<style lang="scss">
body {
  font-family: Inter var,ui-sans-serif,system-ui,-apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Helvetica Neue,Arial,Noto Sans,sans-serif,Apple Color Emoji,Segoe UI Emoji,Segoe UI Symbol,Noto Color Emoji;
}
.form {
  width: 450px;
}
.custom-input-tags {
  .v3it {
    .v3it-tag {
      background: #F56C6C;
    }
  }
}
</style>
