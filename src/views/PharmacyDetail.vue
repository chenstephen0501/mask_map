<template>
    <div v-if="isVisible" class="modal-overlay fixed inset-0 bg-gray-600 bg-opacity-75 flex justify-center items-center">
      <div class="modal-content bg-white p-6 rounded-md w-11/12 md:w-1/2">
        <h2 class="text-xl font-bold mb-4">{{ pharmacy.properties.name }}</h2>
        <p><i class="fa-regular fa-user" style="color: #FFD43B;"></i> 大人口罩: {{ pharmacy.properties.mask_adult }} 個</p>
        <p><i class="fa-solid fa-baby" style="color: #63E6BE;"></i> 小孩口罩: {{ pharmacy.properties.mask_child }} 個</p>
        <p>地址: {{ pharmacy.properties.address }}</p>
        <p>電話: {{ pharmacy.properties.phone }}</p>
        <p>最後更新時間: {{ pharmacy.properties.updated }}</p>
        <button @click="showOnMap" class="mt-4 bg-blue-500 text-white py-2 px-4 rounded">顯示在地圖上</button>
        <button @click="closeModal" class="mt-4 bg-gray-500 text-white py-2 px-4 rounded">關閉</button>
      </div>
    </div>
</template>
  
<script setup>
import { defineProps, defineEmits } from 'vue';
const props = defineProps({
  pharmacy: {
    type: Object,
    required: true
  },
  isVisible: {
      type: Boolean,
      required: true
  }
})

const emit = defineEmits(["close", "show-on-map"])
const closeModal = () => {
  emit("close")
}
const showOnMap = () => {
  emit("show-on-map", props.pharmacy)
}

</script>

<style scoped>
.modal-overlay {
  z-index: 1000;
}

.modal-content {
  z-index: 1001;
}
</style>