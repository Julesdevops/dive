<script lang="ts">
import { defineComponent } from '@vue/composition-api';
import { throttle, debounce } from 'lodash';

export default defineComponent({
  name: 'TrackLengthFilter',
  props: {
    trackLength: {
      type: Number,
      default: 0,
    },
    text: {
      type: String,
      default: 'Track Length Threshold',
    },
    min: {
      type: Number,
      default: 0,
    },
    color: {
      type: String,
      default: null,
    },
  },
  setup(props, { emit }) {
    function _updateTrackLength(event: InputEvent) {
      if (event.target) {
        // eslint-disable-next-line @typescript-eslint/ban-ts-ignore
        // @ts-ignore
        emit('update:trackLength', Number.parseFloat(event.target.value));
      }
    }
    function _emitEnd() {
      emit('end');
    }
    const updateTrackLength = throttle(_updateTrackLength, 100);
    const emitEnd = debounce(_emitEnd, 200);
    return { updateTrackLength, emitEnd };
  },
});
</script>

<template>
  <div>
    <div class="text-body-2 grey--text text--lighten-1 d-flex flex-row py-0">
      <span
        v-if="color"
        :style="{ color }"
        class="pr-1 pb-1"
      >
        █
      </span>
      <span
        :class="{'main-track-length':text ==='Confidence Threshold'}"
      >{{ text }}</span>
      <v-spacer v-if="!$scopedSlots.default" />
      <span class="pl-2">
        {{ trackLength.toFixed(2) }}
      </span>
      <v-spacer v-if="$scopedSlots.default" />
      <slot />
    </div>
    <input
      type="range"
      style="width: 100%"
      :min="min"
      :max="100"
      :step="1"
      :value="Math.max(min, trackLength)"
      persistent-hint
      @input="updateTrackLength"
      @end="emitEnd"
      @mouseup="emitEnd"
    >
  </div>
</template>

<style scoped>
.main-track-length {
  color: white;
  font-weight: bold;
}
</style>
