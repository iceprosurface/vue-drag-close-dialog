<template>
  <div
    class="dialog"
    :class="{
      dialogShow: show,
    }"
  >
    <div
      class="dialogBody"
      ref="target"
      :style="{
        transform: onDraging
          ? `translateY(${y - startY > 0 ? y - startY : 0}px)`
          : undefined,
        transition: onDraging ? 'unset' : undefined,
      }"
    >
    
      <div class="dialogDrag" @mousedown.capture="startDrag" @touchstart.capture="startDrag">拖拽条</div>
      <div>
        {{ onDraging }} {{ y.toFixed(0) }} {{ startY.toFixed(0) }} {{ height.toFixed(0) }}
        <HelloWorld />
      </div>
    </div>
  </div>
</template>
<script setup>
import HelloWorld from './components/HelloWorld.vue';
import { nextTick, ref, useTemplateRef } from 'vue';
import {
  useEventListener,
  onClickOutside,
  useMouse,
  useElementSize,
} from '@vueuse/core';

const props = defineProps({
  show: {
    type: Boolean,
  },
});
const target = useTemplateRef('target');
const emit = defineEmits(['update:show']);
function close() {
  emit('update:show', false);
}
onClickOutside(target, (event) => {
  close();
});
const { y } = useMouse({
  type: 'screen',
  resetOnTouchEnds: true,
});
const onDraging = ref(false);
const { height } = useElementSize(target);
const startY = ref(0);
const getPos = event => [event.screenX, event.screenY]
async function startDrag(event) {
  onDraging.value = true;
  startY.value = getPos(event instanceof TouchEvent ? event.touches[0] : event)[1];
  console.log('start drag');
  document.body.style.overflow = 'hidden';
  document.body.style.touchAction = 'none';
}
function dragEnd(event) {
  if (onDraging.value) {
    event.stopPropagation();
    event.preventDefault();
    console.log(event);
    if (y.value - startY.value > height.value * 0.5) {
      emit('update:show', false);
    }
  }
  onDraging.value = false;
  console.log('end drag');
  document.body.style.overflow = '';
  document.body.style.touchAction = '';
  return false;
}
useEventListener(document.body, 'mouseup', dragEnd);
useEventListener(document.body, 'touchend', dragEnd);
</script>

<style>
.dialog {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  transition: opacity 0.3s ease-in-out;
  opacity: 0;
  pointer-events: none;
}
.dialogShow {
  opacity: 1;
  pointer-events: unset;
}
.dialogShow .dialogBody {
  transform: translateY(0%);
}

.dialogBody {
  height: 40vh;
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  background: #fff;
  padding: 32px;
  transform: translateY(100%);
  transition: transform 0.3s ease-in-out;
}
.dialogDrag {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  user-select: none;
}
</style>
