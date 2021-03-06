<template>
    <span
      tabindex="0"
      :class="wrapClasses"
      :style="wrapStyle"
      @click="toggle"
      @keydown.space="toggle"
    >
        <input type="hidden" :name="name" :value="currentValue">
        <span :class="innerClasses">
            <slot name="open" v-if="currentValue === trueValue"></slot>
            <slot name="close" v-if="currentValue === falseValue"></slot>
        </span>
    </span>
</template>
<script>
  import { oneOf } from '../../utils/util'
  import Emitter from '../../mixins/emitter'

  const prefixCls = 'bin-switch'

  export default {
    name: 'BSwitch',
    mixins: [Emitter],
    props: {
      value: {
        type: [String, Number, Boolean],
        default: false
      },
      trueValue: {
        type: [String, Number, Boolean],
        default: true
      },
      falseValue: {
        type: [String, Number, Boolean],
        default: false
      },
      disabled: {
        type: Boolean,
        default: false
      },
      size: {
        validator (value) {
          return oneOf(value, ['large', 'small', 'default', 'mini'])
        },
        default: 'default'
      },
      name: {
        type: String
      },
      activeColor: String,
      inactiveColor: String
    },
    data () {
      return {
        currentValue: this.value
      }
    },
    computed: {
      wrapClasses () {
        return [
          `${prefixCls}`,
          {
            [`${prefixCls}-checked`]: this.currentValue === this.trueValue,
            [`${prefixCls}-disabled`]: this.disabled,
            [`${prefixCls}-${this.size}`]: !!this.size
          }
        ]
      },
      wrapStyle () {
        let isChecked = this.currentValue === this.trueValue
        return {
          backgroundColor: isChecked ? this.activeColor : this.inactiveColor,
          borderColor: isChecked ? this.activeColor : this.inactiveColor
        }
      },
      innerClasses () {
        return `${prefixCls}-inner`
      }
    },
    methods: {
      toggle (event) {
        event.preventDefault()
        if (this.disabled || this.loading) {
          return false
        }
        const checked = this.currentValue === this.trueValue ? this.falseValue : this.trueValue
        this.currentValue = checked
        this.$emit('input', checked)
        this.$emit('on-change', checked)

        this.dispatch('BFormItem', 'on-form-change', checked)
      }
    },
    watch: {
      value (val) {
        if (val !== this.trueValue && val !== this.falseValue) {
          console.log('Value should be true or false.')
        }
        this.currentValue = val
      }
    }
  }
</script>
