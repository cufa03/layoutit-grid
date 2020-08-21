<template>
  <section
    :class="{ grayed }"
    :style="{ 'grid-area': getGridArea(area), 'border-color': area.color }"
    class="area-editor"
    @pointerdown="handleDown($event)"
  >
    <area-info :area="area" />

    <grid-editor
      v-if="area.grid"
      :area="area"
      :current-area="currentArea"
      :current-item="currentItem"
      :parent-active="isActive || parentActive"
    />

    <flex-editor
      v-if="area.flex"
      :area="area"
      :current-area="currentArea"
      :current-item="currentItem"
      :parent-active="isActive || parentActive"
    />
  </section>
</template>

<script setup="props">
import AreaInfo from './AreaInfo.vue'
import FlexEditor from '../flex/FlexEditor.vue'
import GridEditor from '../grid/GridEditor.vue'

import { computed } from 'vue'
import { store } from '../../store.js'

export { getGridArea } from '../../utils.js'

export default {
  components: {
    AreaInfo,
    FlexEditor,
    GridEditor,
  },
  props: {
    area: { type: Object, required: true },
    currentArea: { type: Object, required: true },
    currentItem: { type: Number, default: null },
    parentActive: { type: Boolean, default: false },
  },
}

export const isMain = computed(() => props.area.name === store.data.area.name)

export const isActive = computed(() => props.area.name === props.currentArea.name)

export const grayed = computed(() => !(isActive.value || props.parentActive))

export function handleDown(event) {
  if (!props.area.grid) {
    event.stopPropagation()
    event.preventDefault()
    const parent = store.getAreaParent(props.area)
    if (parent) {
      store.data.currentArea = parent
    }
  }
}
</script>

<style scoped lang="scss">
.current-grid-backdrop {
  display: none;
  position: fixed;
  top: 10px;
  left: calc(14em + 15px);
  right: 10px;
  bottom: 10px;
  background: rgba(0, 0, 0, 0.4);
  z-index: -1;
  &.reactive {
    display: block;
    z-index: 1;
  }
  &.active {
    display: block;
  }
  @media screen and (max-width: 768px) {
    left: 0;
    right: 0;
    bottom: 0;
    top: 45px;
  }
}

.area-editor {
  touch-action: none;
  background: #fff;
  height: 100%;
  cursor: pointer;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  border: 2px solid;
  background: rgba(255, 255, 255, 0.7);
  &.grayed {
    background: #dddddd;
  }
  z-index: 1;
  &:before {
    display: none;
  }
}
</style>