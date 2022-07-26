<template>
  <QInput
    class="stl-datepicker-input"
    :value="localizedDate"
    :readonly="$attrs.disabled || $attrs.readOnly /* disabled is not enough to prevent input here */"
    v-bind="$attrs"
    input-class="cursor-pointer"
    @click.native.stop="!$attrs.disabled && $refs.datePopup && $refs.datePopup.show()"
  >
    <template #prepend>
      <QBtn
        v-if="calendarIconPosition === 'left' && !hideCalendarIcon"
        :aria-label="$t({ id: 'form.date_placeholder' })"
        :color="iconColor"
        :disabled="$attrs.disabled"
        :icon="icons.matEvent"
        rounded
        dense
        flat
      >
        <QPopupProxy ref="datePopup">
          <QDate
            :mask="dateMask"
            :value="datePickerDate"
            :options="getValidDates"
            @input="selectDate"
          />
        </QPopupProxy>
      </QBtn>
    </template>

    <template #append>
      <QBtn
        v-if="calendarIconPosition === 'right' || hideCalendarIcon"
        :class="[hideCalendarIcon ? 'no-pointer-events' : '']"
        :aria-label="$t({ id: 'form.date_placeholder' })"
        :color="iconColor"
        :disabled="$attrs.disabled"
        rounded
        dense
        flat
      >
        <QPopupProxy ref="datePopup">
          <QDate
            :mask="dateMask"
            :value="datePickerDate"
            :options="getValidDates"
            @input="selectDate"
          />
        </QPopupProxy>
      </QBtn>

      <QBtn
        v-show="date && !hideCloseIcon"
        :aria-label="$t({ id: 'prompt.reset_button' })"
        :icon="icons.matClose"
        rounded
        dense
        flat
        @click.stop="selectDate(null)"
      />
    </template>
  </QInput>
</template>

<script>
import { date } from 'quasar'
import { matEvent, matClose } from '@quasar/extras/material-icons'

export default {
  props: {
    date: {
      type: String,
      default: ''
    },
    iconColor: {
      type: String,
      default: undefined
    },
    calendarIconPosition: {
      type: String,
      validator: value => ['left', 'right'].includes(value),
      default: 'right'
    },
    getValidDates: {
      type: Function,
      default: () => true // all dates are valid
    },
    hideCalendarIcon: {
      type: Boolean,
      default: false
    },
    hideCloseIcon: {
      type: Boolean,
      default: false
    },
  },
  data () {
    return {
      dateMask: 'YYYY-MM-DD',
    }
  },
  computed: {
    localizedDate () {
      if (!this.date) return ''
      return this.$t({ id: 'time.date_short' }, { date: new Date(this.date) })
    },
    datePickerDate () {
      if (!this.date) return null
      return date.formatDate(this.date, this.dateMask)
    }
  },
  created () {
    this.icons = {
      matClose,
      matEvent
    }
  },
  methods: {
    selectDate (date) {
      this.$emit('change', date ? new Date(date).toISOString() : null)
      this.$refs.datePopup.hide()
    },
  }
}

</script>

<style lang="stylus" scoped>

</style>
