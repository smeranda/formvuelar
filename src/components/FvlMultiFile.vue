<template>
  <div :class="{'fvl-has-error' : $parent.hasErrors(name)}" class="fvl-multi-file-wrapper">
    <label
      v-if="label"
      :for="name"
      :class="labelClass"
      class="fvl-multi-file-label"
      v-html="label"
    />
    <div class="fvl-multi-file-button-wrapper">
      <button class="fvl-multi-file-button" tabindex="-1" @click.prevent>
        <slot name="button">
          <span>Add Files</span>
        </slot>
      </button>
      <span class="fvl-multi-file-name">
        <slot
          :files="files"
          name="selected-text"
        >You have selected {{ files ? files.length : 0 }} files</slot>
      </span>
      <input
        :name="name"
        :id="id"
        :ref="name"
        :placeholder="placeholder"
        :class="fieldClass"
        :required="required"
        :readonly="readonly"
        :accept="accept"
        :disabled="disabled || $parent.isLoading"
        multiple
        type="file"
        class="fvl-multi-file"
        @change="handleFileChange(); $parent.dirty(name);"
      >
    </div>
    <div v-for="(file, key) in files" :key="key" class="fvl-multi-file-list">
      {{ file.name }}
      <span class="fvl-multi-file-remove" @click="removeFile( key )">
        <slot name="remove">
          <svg viewBox="0 0 40 40">
            <path
              stroke="current"
              stroke-linecap="round"
              stroke-width="4"
              d="M 10,10 L 30,30 M 30,10 L 10,30"
            ></path>
          </svg>
        </slot>
      </span>
    </div>
    <slot name="hint"/>
    <slot :errors="$parent.getErrors(name)" name="errors">
      <validation-errors :errors="$parent.getErrors(name)"/>
    </slot>
  </div>
</template>

<script>
  import ValidationErrors from './FvlErrors.vue'
  export default {
    components: {
      ValidationErrors
    },
    props: {
      label: {
        type: String,
        required: false,
        default: null
      },
      name: {
        type: String,
        required: true
      },
      id: {
        type: String,
        default: null
      },
      accept: {
        type: String,
        required: false,
        default: null
      },
      placeholder: {
        type: String,
        required: false,
        default: null
      },
      fieldClass: {
        type: String,
        required: false,
        default: null
      },
      labelClass: {
        type: String,
        required: false,
        default: null
      },
      required: {
        type: Boolean,
        required: false,
        default: false
      },
      readonly: {
        type: Boolean,
        required: false,
        default: false
      },
      disabled: {
        type: Boolean,
        required: false,
        default: false
      }
    },
    data() {
      return {
        files: []
      }
    },
    methods: {
      //Handles a change on the file upload
      handleFileChange() {
        var uploadedFiles = this.$refs[this.name].files
        for (var i = 0; i < uploadedFiles.length; i++) {
          this.files.push(uploadedFiles[i])
        }
        this.$emit('update:files', this.files)
      },
      removeFile(key) {
        this.files.splice(key, 1)
      }
    }
  }
</script>
