<script setup>
import { ref } from 'vue'
import { Edit, Delete } from '@element-plus/icons-vue'
import channelSelect from './components/channelSelect.vue'
import articleEdit from './components/articleEdit.vue'
import { artGetListService, artDelService } from '@/api/article.js'
import { formatTime } from '@/utils/format.js'
const artList = ref([])
const total = ref(0)
const loading = ref(false)
const params = ref({
  pagenum: 1,
  pagesize: 10,
  cate_id: '',
  state: ''
})

const articleEditRef = ref(null)
const onAddArticle = () => {
  articleEditRef.value.open({})
}
const onEdit = (row) => {
  articleEditRef.value.open(row)
}
const onDelete = async (row) => {
  await ElMessageBox.confirm('你确认删除该文章信息吗？', '温馨提示', {
    type: 'warning',
    confirmButtonText: '确认',
    cancelButtonText: '取消'
  })
  await artDelService(row.id)
  ElMessage({ type: 'success', message: '删除成功' })
  getArtList()
}
const getArtList = async () => {
  loading.value = true
  const res = await artGetListService(params.value)
  loading.value = false
  artList.value = res.data.data
  total.value = res.data.total
}
getArtList()

const onSizeChange = (size) => {
  params.value.pagenum = 1
  params.value.pagesize = size
  getArtList()
}
const onCurrentChange = (page) => {
  params.value.pagenum = page
  getArtList()
}
const onSearch = () => {
  params.value.pagenum = 1
  getArtList()
}
const onReset = () => {
  params.value = {
    pagenum: 1,
    pagesize: 10,
    cate_id: '',
    state: ''
  }
  getArtList()
}
const onSuccess = (state) => {
  if (state === 'add') {
    const lastPage = Math.ceil((total.value + 1) / params.value.pagesize)
    params.value.pagenum = lastPage
  }
  getArtList()
}
</script>
<template>
  <pageContainer title="文章管理">
    <template #extra>
      <el-button type="primary" @click="onAddArticle">添加文章</el-button>
    </template>

    <el-form inline>
      <el-form-item label="文章分类:" style="width: 200px">
        <channelSelect v-model:cid="params.cate_id"></channelSelect>
      </el-form-item>
      <el-form-item label="发布状态:" style="width: 200px">
        <el-select v-model="params.state">
          <el-option label="已发布" value="已发布"></el-option>
          <el-option label="草稿" value="草稿"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSearch">搜索</el-button>
        <el-button @click="onReset">重置</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="artList" v-loading="loading">
      <el-table-column label="文章标题" prop="title">
        <template #default="{ row }">
          <el-link type="primary" :underline="false">{{ row.title }}</el-link>
        </template>
      </el-table-column>
      <el-table-column label="分类" prop="cate_name"></el-table-column>
      <el-table-column label="发表时间" :prop="pub_date">
        <template #default="{ row }">
          {{ formatTime(row.pub_date) }}
        </template>
      </el-table-column>
      <el-table-column label="状态" prop="state"></el-table-column>
      <el-table-column label="操作">
        <template #default="{ row }">
          <el-button
            circle
            plain
            type="primary"
            :icon="Edit"
            @click="onEdit(row)"
          ></el-button>
          <el-button
            circle
            plain
            type="danger"
            :icon="Delete"
            @click="onDelete(row)"
          ></el-button>
        </template>
      </el-table-column>

      <template #empty>
        <el-empty description="暂无数据"></el-empty>
      </template>
    </el-table>

    <el-pagination
      style="margin-top: 20px; justify-content: flex-end"
      v-model:current-page="params.pagenum"
      v-model:page-size="params.pagesize"
      :page-sizes="[2, 5, 10]"
      :background="true"
      layout="jumper,total, sizes, prev, pager, next"
      :total="total"
      @size-change="onSizeChange"
      @current-change="onCurrentChange"
    />

    <articleEdit ref="articleEditRef" @success="onSuccess"></articleEdit>
  </pageContainer>
</template>
