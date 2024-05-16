<script setup>
import { ref } from 'vue'
import { artGetChannelsService } from '@/api/article.js'
const channelList = ref([])
const getChannelList = async () => {
  const res = await artGetChannelsService()
  channelList.value = res.data.data
}
defineProps({
  cid: {
    type: [String, Number]
  }
})
const emit = defineEmits(['update:cid'])
getChannelList()
</script>
<template>
  <el-select :modelValue="cid" @update:modelValue="emit('update:cid', $event)">
    <el-option
      v-for="channel in channelList"
      :key="channel.id"
      :label="channel.cate_name"
      :value="channel.id"
    ></el-option>
  </el-select>
</template>
