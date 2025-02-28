<template>
  <div class="ui-checkbox-group" :class="classes">
    <div v-if="label || $slots.default" class="ui-checkbox-group__label-text">
      <slot>{{ label }}</slot>
    </div>

    <div class="ui-checkbox-group__checkboxes">
      <ui-checkbox
        v-for="(option, index) in options"
        :id="option[keys.id]"
        :key="option[keys.id]"
        v-model="checkboxValues[index]"
        class="ui-checkbox-group__checkbox"
        :box-position="boxPosition"
        :checked="isOptionCheckedByDefault(option)"
        :class="option[keys.class]"
        :color="color"
        :disabled="disabled || option[keys.disabled]"
        :name="name || option[keys.name]"
        @blur="onBlur"
        @change="(...args) => { onChange(args, option) }"
        @focus="onFocus"
        >{{ option[keys.label] || option }}</ui-checkbox
      >
    </div>

    <div v-if="hasFeedback" class="ui-checkbox-group__feedback">
      <div v-if="showError" class="ui-checkbox-group__feedback-text">
        <slot name="error">{{ error }}</slot>
      </div>

      <div v-else-if="showHelp" class="ui-checkbox-group__feedback-text">
        <slot name="help">{{ help }}</slot>
      </div>
    </div>
  </div>
</template>

<script>
import UiCheckbox from "./UiCheckbox.vue";

import { looseIndexOf } from "./helpers/util";

export default {
  name: "UiCheckboxGroup",

  components: {
    UiCheckbox,
  },

  props: {
    name: String,
    options: {
      type: Array,
      required: true,
    },
    modelValue: {
      type: Array,
      required: true,
    },
    keys: {
      type: Object,
      default() {
        return {
          id: "id",
          name: "name",
          class: "class",
          label: "label",
          value: "value",
          disabled: "disabled",
        };
      },
    },
    label: String,
    color: {
      type: String,
      default: "primary", // 'primary' or 'accent'
    },
    boxPosition: {
      type: String,
      default: "left", // 'left' or 'right'
    },
    vertical: {
      type: Boolean,
      default: false,
    },
    help: String,
    error: String,
    invalid: {
      type: Boolean,
      default: false,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
  },

  emits: ["update:modelValue", "focus", "blur", "change"],

  data() {
    return {
      isActive: false,
      ignoreChange: false,
      checkboxValues: [],
      initialValue: JSON.parse(JSON.stringify(this.modelValue)),
    };
  },

  computed: {
    classes() {
      return [
        `ui-checkbox-group--color-${this.color}`,
        `ui-checkbox-group--box-position-${this.boxPosition}`,
        { "is-vertical": this.vertical },
        { "is-active": this.isActive },
        { "is-invalid": this.invalid },
        { "is-disabled": this.disabled },
      ];
    },

    hasFeedback() {
      return this.showError || this.showHelp;
    },

    showError() {
      return this.invalid && (Boolean(this.error) || Boolean(this.$slots.error));
    },

    showHelp() {
      return Boolean(this.help) || Boolean(this.$slots.help);
    },
  },

  methods: {
    reset() {
      this.ignoreChange = true;
      this.options.forEach((option, index) => {
        this.checkboxValues[index] = this.isOptionCheckedByDefault(option);
      });
      this.ignoreChange = false;

      this.$emit(
        "update:modelValue",
        this.initialValue.length > 0 ? [].concat(this.initialValue) : []
      );
    },

    isOptionCheckedByDefault(option) {
      return looseIndexOf(this.initialValue, option[this.keys.value] || option) > -1;
    },

    onFocus(e) {
      this.isActive = true;
      this.$emit("focus", e);
    },

    onBlur(e) {
      this.isActive = false;
      this.$emit("blur", e);
    },

    onChange(args, option) {
      if (this.ignoreChange) {
        return;
      }

      const checked = args[0];
      const event = args[1];

      let value = [];
      const optionValue = option[this.keys.value] || option;
      const i = looseIndexOf(this.modelValue, optionValue);

      if (checked && i < 0) {
        value = this.modelValue.concat(optionValue);
      }

      if (!checked && i > -1) {
        value = this.modelValue.slice(0, i).concat(this.modelValue.slice(i + 1));
      }

      this.$emit("update:modelValue", value);
      this.$emit("change", value, event);
    },
  },
};
</script>

<style lang="scss">
@import "./styles/imports";

.ui-checkbox-group {
  font-family: $font-stack;
  line-height: normal;

  &:not(.is-disabled):not(.is-invalid):hover {
    .ui-checkbox-group__label-text {
      color: $ui-input-label-color--hover;
    }
  }

  &:not(.is-disabled):not(.is-invalid).is-active {
    .ui-checkbox-group__label-text {
      color: $ui-input-label-color--active;
    }
  }

  &.is-vertical {
    .ui-checkbox-group__checkboxes {
      flex-direction: column;
      padding-top: rem(8px);
    }

    .ui-checkbox-group__checkbox {
      margin-bottom: rem(12px);
      margin-left: 0;
      width: 100%;
    }
  }

  &.is-invalid {
    .ui-checkbox-group__label-text {
      color: $ui-input-label-color--invalid;
    }

    .ui-checkbox-group__feedback {
      color: $ui-input-feedback-color--invalid;
    }
  }

  &.is-disabled {
    .ui-checkbox-group__feedback {
      opacity: $ui-input-feedback-opacity--disabled;
    }
  }
}

.ui-checkbox-group__label-text {
  color: $ui-input-label-color;
  font-size: $ui-input-label-font-size;
  transition: color 0.1s ease;
}

.ui-checkbox-group__checkboxes {
  align-items: center;
  display: flex;
  min-height: $ui-input-height;
}

.ui-checkbox.ui-checkbox-group__checkbox {
  margin-bottom: 0;
  margin-left: rem(24px);

  &:first-child {
    margin-left: 0;
  }
}

.ui-checkbox-group__feedback {
  color: $ui-input-feedback-color;
  font-size: $ui-input-feedback-font-size;
  line-height: $ui-input-feedback-line-height;
  margin: 0;
  padding-top: $ui-input-feedback-padding-top - rem(4px);
  position: relative;
}

// ================================================
// Box Positions
// ================================================

.ui-checkbox-group--box-position-right {
  &:not(.is-vertical) {
    .ui-checkbox__label-text {
      margin-right: rem(8px);
    }
  }
}
</style>
