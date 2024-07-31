<template>
  <div>
    <button @click="rowVirtualizer.scrollToIndex(0)">scroll to the top</button>
    <span style="padding: 0 4px"></span>
    <button @click="rowVirtualizer.scrollToIndex(sentences.length / 2)">
      scroll to the middle
    </button>
    <span style="padding: 0 4px"></span>
    <button @click="rowVirtualizer.scrollToIndex(sentences.length - 1)">
      scroll to the end
    </button>
    <hr />
    <div
      ref="parentRef"
      class="List"
      style="height: 400px; width: 400px; overflow-y: auto; contain: strict"
    >
      <div
        :style="{
          height: `${totalSize}px`,
          width: '100%',
          position: 'relative',
        }"
      >
        <div
          :style="{
            position: 'absolute',
            top: 0,
            left: 0,
            width: '100%',
            transform: `translateY(${virtualRows[0]?.start ?? 0}px)`,
          }"
        >

        <draggable class="list-group" :list="virtualRows" group="people" item-key="id">
          <!-- <div
            v-for="virtualRow in virtualRows"
            :key="virtualRow.key"
            :data-index="virtualRow.index"
            :ref="measureElement"
            :class="virtualRow.index % 2 ? 'ListItemOdd' : 'ListItemEven'"
          >
            <div style="padding: 10px 0">
              <div>Row {{ virtualRow.index }}</div>
              <div>{{ sentences[virtualRow.index] }}</div>
            </div>
          </div> -->

          <template #item="{element}">
            <div
              item-key="id"
              :data-index="element.index"
              :ref="measureElement"
              :class="element.index % 2 ? 'ListItemOdd' : 'ListItemEven'"
            >
              <div style="padding: 10px 0">
                <div>Row {{ element.index }}</div>
                <div>{{ sentences[element.index] }}</div>
              </div>
            </div>
          </template>

        </draggable>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { useVirtualizer } from '@tanstack/vue-virtual'
import { generateSentences } from './utils'
import draggable from 'vuedraggable'

const sentences = generateSentences()

const parentRef = ref<HTMLElement | null>(null)

const rowVirtualizer = useVirtualizer({
  count: sentences.length,
  getScrollElement: () => parentRef.value,
  estimateSize: () => 55,
})

const virtualRows = computed(() => rowVirtualizer.value.getVirtualItems())

const totalSize = computed(() => rowVirtualizer.value.getTotalSize())

const measureElement = (el) => {
  if (!el) {
    return
  }

  rowVirtualizer.value.measureElement(el)

  return undefined
}
</script>
