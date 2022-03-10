<template>
  <div @click="focusNewTag()"
       :class="{
          'cs-input--focus': isInputActive,
          'cs-input--error': isError
        }"
       class="cs-input vue-input-tag-wrapper">
    <div class="flex flex-row w-full">
      <div class="w-full flex flex-row flex-wrap">
        <span v-for="(tag, index) in innerTags"
              :key="index"
              class="input-tag">
          <slot name="item" v-bind="{ name: tag }"></slot>
          <a v-if="!readOnly" @click.prevent.stop="remove(index)" class="remove"></a>
        </span>
        <input
            ref="inputTag"
            :placeholder="placeholder"
            v-model="newTag"
            v-on:keydown.delete.stop="removeLastTag"
            v-on:keydown="addNew"
            v-on:blur="handleInputBlur"
            v-on:focus="handleInputFocus"
            v-on:input="makeItNormal"
            class="new-tag"/>
      </div>
    </div>
  </div>
</template>

<script>
const validators = {
  email: new RegExp(
      /^[a-z][a-z0-9_\.]{2,50}@[a-z0-9]{2,}(\.[a-z0-9]{2,4}){1,2}$/
  ),
  url: new RegExp(
      /^(https?|ftp|rmtp|mms):\/\/(([A-Z0-9][A-Z0-9_-]*)(\.[A-Z0-9][A-Z0-9_-]*)+)(:(\d+))?\/?/i
  ),
  text: new RegExp(/^[a-zA-Z]+$/),
};
export default {
  name: "InputTag",
  emits: ['update:modelValue', 'on-limit', 'on-tags-change', 'on-remove', 'on-remove-error', 'on-error', 'on-focus', 'on-blur', 'new-tag-change-length'],
  props: {
    readOnly: {
      type: Boolean,
      default: false
    },
    validate: {
      type: [String, Function, Object],
      default: ""
    },
    addTagOnKeys: {
      type: Array,
      default: function () {
        return [
          13, // Enter
          188, // Comma ','
          32, // Space
        ];
      }
    },
    placeholder: {
      type: String,
      default: ''
    },
    modelValue: {
      type: Array,
      default: () => []
    },
    limit: {
      type: Number,
      default: -1
    },
    allowDuplicates: {
      type: Boolean,
      default: false
    },
    addTagOnBlur: {
      type: Boolean,
      default: false
    },
  },
  data() {
    return {
      isInputActive: false,
      isError: false,
      newTag: '',
      innerTags: []
    }
  },
  computed: {
    isLimit() {
      let isLimit = this.limit > 0 && Number(this.limit) === this.innerTags.length;
      if (isLimit) {
        this.$emit('on-limit');
      }
      return isLimit;
    }
  },
  watch: {
    error() {
      this.isError = this.error;
    },
    modelValue: {
      deep: true,
      immediate: true,
      handler(tags) {
        this.innerTags = [...tags];
        this.newTag = ""
      }
    },
    newTag() {
      if(this.newTag.length > 50){
        this.$refs.inputTag.className = 'new-tag txt-error';
        this.$refs.inputTag.style.textDecoration="underline";
        this.$emit('new-tag-change-length', true);
      }
      else {
        this.$emit('new-tag-change-length', false);
      }
    }
  },
  methods: {
    makeItNormal() {
      this.$refs.inputTag.className = 'new-tag';
      this.$refs.inputTag.style.textDecoration="none";
    },
    resetData() {
      this.innerTags = []
    },
    focusNewTag() {
      if (this.readOnly || !this.$el.querySelector(".new-tag")) {
        return;
      }
      this.$el.querySelector(".new-tag").focus();
    },
    handleInputFocus(event) {
      this.isInputActive = true;
      this.$emit('on-focus', event);
    },
    handleInputBlur(e) {
      this.isInputActive = false;
      this.addNew(e);
      this.$emit('on-blur', e);
    },
    addNew(e) {
      const keyShouldAddTag = e
          ? this.addTagOnKeys.indexOf(e.keyCode) !== -1
          : true;
      const typeIsNotBlur = e && e.type !== "blur";
      this.$emit('on-remove-error');
      if (
          (!keyShouldAddTag && (typeIsNotBlur || !this.addTagOnBlur)) ||
          this.isLimit
      ) {
        return;
      }
      if (
          this.newTag &&
          (this.allowDuplicates || this.innerTags.indexOf(this.newTag) === -1) &&
          this.validateIfNeeded(this.newTag) && this.newTag.length <= 50
      ) {
        this.innerTags.push(this.newTag);
        this.newTag = "";
        this.tagChange();
        e && e.preventDefault();
      } else {
        if(this.validateIfNeeded(this.newTag)){
          if(this.newTag && this.newTag.length <= 50) {
            this.makeItError(true);
          }else {
            this.makeItError('maxLength');
          }
        } else {
          this.makeItError(false);
        }
        e && e.preventDefault();
      }
    },
    makeItError(isDuplicatedOrMaxLength) {
      this.$refs.inputTag.className = 'new-tag txt-error';
      this.$refs.inputTag.style.textDecoration="underline";
      this.$emit('on-error', isDuplicatedOrMaxLength);
    },
    validateIfNeeded(tagValue) {
      if (this.validate === "" || this.validate === undefined) {
        return true;
      }
      if (typeof this.validate === "function") {
        return this.validate(tagValue);
      }
      if (
          typeof this.validate === "string" &&
          Object.keys(validators).indexOf(this.validate) > -1
      ) {
        return validators[this.validate].test(tagValue);
      }
      if (
          typeof this.validate === "object" &&
          this.validate.test !== undefined
      ) {
        return this.validate.test(tagValue);
      }
      return true;
    },
    removeLastTag() {
      if (this.newTag) {
        return;
      }
      this.innerTags.pop();
      this.tagChange();
    },
    remove(index) {
      this.innerTags.splice(index, 1);
      this.tagChange();
      this.$emit("on-remove", index)
    },
    tagChange() {
      this.$emit("on-tags-change", this.innerTags);
    }
  }
};
</script>

<style>
.vue-input-tag-wrapper {
  border-radius: 5px;
  min-height: 40px;
  line-height: 1.4 !important;
  background-color: #fff;
  border: 1px solid #9ca3af;
  overflow: hidden;
  cursor: text;
  text-align: left;
  -webkit-appearance: textfield;
  display: flex;
  flex-wrap: wrap;
}
textarea {
  resize: none;
}
textarea ::placeholder {
  color: #9ca3af;
}

.txt-error {
  color: red;
}

.w-full {
  width: 100%;
}
.flex {
  display: flex;
}
.flex-row {
  flex-direction: row;
}
.flex-wrap{
  flex-wrap: wrap;
}
.vue-input-tag-wrapper .input-tag {
  display: inline-flex;
  font-weight: 400;
  margin: 4px;
  padding: 0 5px;
  background: #f7f7f7;
  max-height: 27px;
  border-radius: 5px;
  border: 1px solid #eee;
  flex-direction: row;
  align-items: center;
}
.vue-input-tag-wrapper .new-tag {
  background: transparent;
  border: 0;
  font-weight: 400;
  margin: 4px;
  outline: none;
  flex-grow: 1;
  padding: 4px 0;
}
.vue-input-tag-wrapper .input-tag .remove {
  font-weight: bold;
  color: #638421;
  cursor: pointer;
  padding: 0 5px 0 7px;
}
.vue-input-tag-wrapper .input-tag .remove:hover {
  text-decoration: none;
  color: red;
}
.vue-input-tag-wrapper .input-tag .remove::before {
  content: "x";
}
.cs-input--focus {
  outline: 0;
  border-color: #258456;
  box-shadow: 0 0 0 2px #258456;
}
.cs-input--error{
  border-color: red;
  box-shadow: 0 0 0 2px red;
}
</style>