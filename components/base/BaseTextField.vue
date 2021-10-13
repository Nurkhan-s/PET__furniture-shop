<template>
  <div class="text-block" :class="{ magnifier: magnifier }">
    <div class="text-block__field">
      <input
        v-if="!withMask"
        ref="input"
        class="text-block__input"
        :value="value"
        :class="{
          error: hasErrorFeedback,
          'status-error':
            (status === 'danger' || status === 'warning') && !hasErrorFeedback,
          'status-success': status === 'success' && !hasErrorFeedback,
        }"
        required
        @input="inputHandler($event.target.value)"
        @keypress.enter="addException(value)"
        @keypress.space="addException(value)"
        @focus="$emit('openAutocomplete')"
      />
      <TheMask
        v-else
        ref="input"
        :mask="mask"
        :masked="false"
        :placeholder="inputPlaceholder"
        :value="value"
        class="text-block__input text-block__mask"
        :class="{ error: hasErrorFeedback }"
        required
        @input.native="inputHandler($event.target.value)"
        @focus.native="showPlaceholder = true"
        @blur.native="showPlaceholder = false"
      />
      <BaseIcon
        v-if="trash && value.length === 0"
        class="ic-size-16 text-block__icon"
        @click.native="clearInput"
      >
        trash
      </BaseIcon>
      <BaseIcon class="ic-size-16 text-block__magnifier"> magnifier </BaseIcon>
      <label class="text-block__label"
        >{{ labelText
        }}<span v-if="required" class="text-block__label__red">*</span></label
      >
      <BaseIcon
        v-if="passwordShowAble && !showPassword"
        class="ic-size-14 text-block__icon"
        @click.native="toggleShowPassword"
      >
        closedEye
      </BaseIcon>
      <BaseIcon
        v-if="passwordShowAble && showPassword"
        class="ic-size-14 text-block__icon"
        @click.native="toggleShowPassword"
      >
        openedEye
      </BaseIcon>
      <BaseIcon
        v-if="clearable && value && value.length > 0"
        class="ic-size-14 text-block__icon"
        @click.native="clearTheField(value)"
      >
        clearIcon
      </BaseIcon>
    </div>

    <div
      v-if="isException && exceptions.length > 0"
      class="text-block__exceptions"
    >
      <div class="text-block__exceptions--block">
        <div v-for="(exception, i) in exceptions" :key="i" class="exceptions">
          <span>
            {{ exception }}
          </span>
          <BaseIcon class="trash" @click.native="removeException(exception, i)">
            trashGray
          </BaseIcon>
        </div>
      </div>
      <div class="exceptions__icons">
        <BaseIcon class="ic-size-16 svg" @click.native="$emit('removeAll')">
          trashGray
        </BaseIcon>
        <BaseIcon class="ic-size-16 svg"> save </BaseIcon>
      </div>
    </div>
    <div v-if="showFeedback" class="text-block__info">
      <div class="create-info" :class="status">
        <div class="create-info__progress">
          <div class="create-info__indicator" />
        </div>
        <div class="create-info__text">
          {{ text }}
        </div>
      </div>
    </div>
    <div v-if="hasErrorFeedback" class="text-block__error">
      {{ errorText }}
    </div>
  </div>
</template>
<script>
import TheMask from "vue-the-mask";
import vClickOutside from "v-click-outside";

export default {
  directives: {
    clickOutside: vClickOutside,
  },
  components: {
    TheMask,
  },
  props: {
    value: {
      type: [String, null],
      required: true,
    },
    inputPlaceholder: {
      type: String,
      default: "",
    },
    magnifier: {
      type: Boolean,
      default: false,
    },
    hasErrorFeedback: {
      type: Boolean,
      default: false,
    },
    errorText: {
      type: String,
      default: "",
    },
    labelText: {
      type: String,
      required: true,
    },
    passwordShowAble: {
      type: Boolean,
      default: false,
    },
    clearable: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      showPassword: false,
      text: "",
      status: "",
      showFeedback: false,
      showPlaceholder: false,
      optionIndex: 1,
      ownInputValue: "",
      countInput: 1,
    };
  },
  methods: {
    addException(val) {
      if (this.isException && val.split(" ").join("").length > 1) {
        this.$emit("addException", val);
      }
    },
    removeException(val, index) {
      if (this.isException) {
        this.$emit("removeException", index);
      }
    },
    inputHandler(value) {
      this.$emit("input", value);
      // if (this.signup) this.passwordValidationHandler(value);
    },
    clearInput() {
      this.countInput--;
      this.$emit("clear", this.countInput);
    },
    toggleShowPassword() {
      if (!this.showPassword) {
        this.showPasswordHandler("text", true);
      } else {
        this.showPasswordHandler("password", false);
      }
    },
    showPasswordHandler(type, showPassword) {
      this.$refs.input.type = type;
      this.showPassword = showPassword;
    },
    clearTheField() {
      this.$emit("input", "");
    },
  },
};
</script>
<style lang="scss" scoped>
*:focus {
  outline: none;
}

.text-block {
  $self: &;
  position: relative;
  width: 100%;

  &__error {
    text-align: left;
  }

  &.magnifier {
    #{$self}__magnifier {
      display: block;
    }

    #{$self}__input {
      padding: 19px 40px 5px 40px;
    }

    #{$self}__label {
      left: 40px;
    }
  }

  &__field {
    position: relative;
    overflow: hidden;
  }

  &__input {
    background: #ffffff;
    border: 1px solid #c4c4c4;
    box-sizing: border-box;
    border-radius: 2px;
    width: 100%;
    height: 48px;
    font-size: 14px;
    line-height: 22px;
    display: flex;
    align-items: center;
    color: #2c3e50;
    padding: 19px 40px 5px 16px;
    transition: 0.3s all;

    &:focus ~ #{$self}__label,
    &:not(:focus):valid ~ #{$self}__label {
      top: 15px;
      font-size: 10px;
      color: #71757a;
    }

    &:focus {
      border: 1px solid #2c3e50;
    }

    &::placeholder {
      font-size: 12px;
    }

    &.error {
      background: #ff2e431f;
    }
  }

  &__mask {
    letter-spacing: 1px;
  }

  &__label {
    position: absolute;
    pointer-events: none;
    left: 16px;
    top: 50%;
    transform: translateY(-50%);
    white-space: nowrap;
    overflow: hidden;
    line-height: 40px;
    transition: 0.3s;
    color: #c4c4c4;

    &__red {
      color: rgba(255, 0, 0, 0.53);
    }
  }

  &__icon {
    position: absolute;
    right: 16px;
    top: 50%;
    transform: translateY(-50%);
    cursor: pointer;
  }

  &__magnifier {
    position: absolute;
    left: 18px;
    top: 50%;
    transform: translateY(-50%);
    cursor: pointer;
    display: none;
  }

  &__error {
    font-size: 10px;
    line-height: 14px;
    color: #ff2e43;
    margin-top: 6px;
  }

  &__options {
    position: absolute;
    top: calc(100% + 4px);
    left: 0;
    width: 100%;
    background: #fff;
    z-index: 1000;
    border-radius: 2px;
    border: 1px solid #c4c4c480;
    padding: 12px 0;
  }

  &__exceptions {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 5px;
    background: #ffffff;
    border: 0.5px solid #c4c4c4;
    border-radius: 2px;
    padding: 12px 16px;

    &--block {
      width: 100%;
      display: grid;
      column-gap: 10px;
      grid-template-columns: repeat(auto-fit, minmax(80px, max-content));

      .exceptions {
        height: 30px;
        background: rgba(236, 241, 247, 0.7);
        border-radius: 2px;
        padding: 8px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 5px;

        span {
          font-size: 12px;
          line-height: 16px;
          color: #9da3ac;
          width: 100%;
        }

        .trash {
          width: 16px;
          height: 16px;
          cursor: pointer;
        }
      }
    }
  }

  .exceptions__icons {
    display: flex;
    .svg {
      width: 16px;
      height: 16px;
      cursor: pointer;
    }

    .svg + .svg {
      margin-left: 16px;
    }
  }
}

.search-options {
  &__item {
    padding: 5px 16px;
    line-height: 16px;
    font-size: 14px;
    color: #9da3ac;
    transition: 0.3s all;
    cursor: pointer;

    &:hover {
      background: #eef0f5;
    }
  }
}

.create-info {
  $self: &;

  margin-top: 9px;
  margin-bottom: 12px;

  &.danger {
    #{$self}__indicator {
      background: #ff2e43;
      width: 33.333%;
    }

    #{$self}__text {
      color: #ff2e43;
    }
  }

  &.warning {
    #{$self}__indicator {
      background: #ffcd33;
      width: 66.666%;
    }

    #{$self}__text {
      color: #fab619;
    }
  }

  &.success {
    #{$self}__indicator {
      background: #00b92d;
      width: 100%;
    }

    #{$self}__text {
      color: #00b92d;
    }
  }

  &__progress {
    height: 1px;
    background: #c4c4c4;
    position: relative;
  }

  &__indicator {
    position: absolute;
    top: 33.333%;
    transform: translateY(-50%);
    height: 2px;
    border-radius: 10px;
  }

  &__text {
    font-style: normal;
    font-weight: normal;
    font-size: 10px;
    line-height: 14px;
    display: flex;
    align-items: center;
    margin-top: 4px;
  }
}

.ps {
  &__thumb-y {
    width: 2px;
  }
}

.underlines {
  display: flex;
  position: absolute;
  bottom: 24px;
  left: 40px;
  height: 1px;
  width: 100%;
  pointer-events: none;
  justify-content: flex-end;

  &.active {
    display: none;
  }

  & > div {
    display: flex;
  }

  span {
    display: inline-block;
    margin-right: 2px;
  }
}

#searchVariant {
  background: #eef0f5;
}
</style>
