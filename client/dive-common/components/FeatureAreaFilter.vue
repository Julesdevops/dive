<script lang="ts">
import { defineComponent } from '@vue/composition-api';
import { throttle, debounce } from 'lodash';

export default defineComponent({
  name: 'FeatureAreaFilter',
  props: {
    featureArea: {
      type: Number,
      default: 0,
    },
    text: {
      type: String,
      default: 'Feature Area Threshold',
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
    function _updateFeatureArea(event: InputEvent) {
      if (event.target) {
        // eslint-disable-next-line @typescript-eslint/ban-ts-ignore
        // @ts-ignore
        emit('update:featureArea', Number.parseFloat(event.target.value));
      }
    }
    function _emitEnd() {
      emit('end');
    }
    const updateFeatureArea = throttle(_updateFeatureArea, 100);
    const emitEnd = debounce(_emitEnd, 200);
    return { updateFeatureArea, emitEnd };
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
        :class="{'main-feature-area':text ==='Confidence Threshold'}"
      >{{ text }}</span>
      <v-spacer v-if="!$scopedSlots.default" />
      <span class="pl-2">
        {{ featureArea.toFixed(2) }}
      </span>
      <v-spacer v-if="$scopedSlots.default" />
      <slot />
    </div>
    <input
      type="range"
      style="width: 100%"
      :min="min"
      :max="500000"
      :step="1000"
      :value="Math.max(min, featureArea)"
      persistent-hint
      @input="updateFeatureArea"
      @end="emitEnd"
      @mouseup="emitEnd"
    >
  </div>
</template>

<style scoped>
.main-feature-area {
  color: white;
  font-weight: bold;
}
</style>
