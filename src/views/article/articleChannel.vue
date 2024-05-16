<script setup>
import { ref } from 'vue'
import { artDelChannelService, artGetChannelsService } from '@/api/article'
import { Edit, Delete } from '@element-plus/icons-vue'
import channelEdit from './components/channelEdit.vue'
import { ElMessage, ElMessageBox } from 'element-plus'
const channelList = ref([])
const loading = ref(false)
const dialog = ref()

const getChannelList = async () => {
  loading.value = true
  const res = await artGetChannelsService()
  channelList.value = res.data.data
  console.log(channelList.value)
  loading.value = false
}
getChannelList()
const onEdit = (row, $index) => {
  console.log(row, $index)
  dialog.value.open(row)
}
const onDelete = async (row, $index) => {
  console.log(row, $index)
  await ElMessageBox.confirm('确定删除该分类吗?', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  })
  await artDelChannelService(row.id)
  ElMessage.success('删除成功')
  getChannelList()
}
const onAddChannel = () => {
  dialog.value.open({})
}
const onSuccess = () => {
  getChannelList()
}
</script>

<template>
  <pageContainer title="文章分类">
    <template #extra>
      <el-button type="primary" @click="onAddChannel">添加分类</el-button>
    </template>

    <el-table v-loading="loading" :data="channelList" style="width: 100%">
      <el-table-column type="index" label="序号" width="100"></el-table-column>
      <el-table-column prop="cate_name" label="分类名称"></el-table-column>
      <el-table-column prop="cate_alias" label="分类别名"></el-table-column>
      <el-table-column label="操作" width="100">
        <template #default="{ row, $index }">
          <el-button
            :icon="Edit"
            size="small"
            type="primary"
            circle
            plain
            @click="onEdit(row, $index)"
          ></el-button>
          <el-button
            type="danger"
            :icon="Delete"
            size="small"
            circle
            plain
            @click="onDelete(row, $index)"
          ></el-button>
        </template>
      </el-table-column>

      <template #empty>
        <el-empty description="暂无数据"></el-empty>
      </template>
    </el-table>

    <channel-edit ref="dialog" @success="onSuccess"></channel-edit>
  </pageContainer>
</template>
