<template>
  <div class="repo-view">
    <n-page-header>
      <template #header>
        <n-breadcrumb>
          <n-breadcrumb-item>配置</n-breadcrumb-item>
          <n-breadcrumb-item>存储库</n-breadcrumb-item>
        </n-breadcrumb>
      </template>
    </n-page-header>
    <div class="op-area">
      <n-input style="width: 700px; margin-right: 15px" v-model:value="repoName" type="text" placeholder="请输入名称"></n-input>
      <n-select style="width: 700px; margin-right: 15px" v-model:value="repoTypeSelectOptionValue" :options="repoTypeSelectOptions" placeholder="请选择类型"></n-select>
      <n-input style="margin-right: 15px" v-model:value="repoUsage" type="type" placeholder="请输入用途,支持全文检索.."></n-input>
      <n-tooltip trigger="hover">
        <template #trigger>
          <n-button tertiary circle style="margin-left: 5px" secondary type="info">
            <template #icon>
              <n-icon>
                <SearchOutlined/>
              </n-icon>
            </template>
          </n-button>
        </template>
        <span>按照指定条件进行搜索</span>
      </n-tooltip>
      <n-tooltip trigger="hover">
        <template #trigger>
          <n-button tertiary circle style="margin-left: 5px" secondary type="primary" @click="handleAddButtonClicked">
            <template #icon>
              <n-icon>
                <PlusOutlined/>
              </n-icon>
            </template>
          </n-button>
        </template>
        <span>添加</span>
      </n-tooltip>
      <n-tooltip trigger="hover">
        <template #trigger>
          <n-button tertiary circle style="margin-left: 5px" secondary type="error" @click="handleBatchDeleteButtonClicked">
            <template #icon>
              <n-icon>
                <DeleteOutlined/>
              </n-icon>
            </template>
          </n-button>
        </template>
        <span>删除选中条目</span>
      </n-tooltip>
      <n-tooltip trigger="hover">
        <template #trigger>
          <n-button tertiary circle style="margin-left: 5px" secondary type="error">
            <template #icon>
              <n-icon>
                <CloseOutlined/>
              </n-icon>
            </template>
          </n-button>
        </template>
        <span>清空搜索条件</span>
      </n-tooltip>
    </div>
    <n-data-table :columns="columns" :data="repos" :pagination="paginationReactive" striped/>
    <n-modal v-model:show="isShowAddModal" :segmented="false"
             :mask-closable="false" preset="card" title="添加存储库"
             :on-after-leave="onAddModalAfterLeave"
             style="width: 45%; min-width: 600px">
      <div style="display: flex; width: 100%; height: 100%; flex-direction: column">
        <div style="width: 100%">
          <div style="font-size: 12pt; font-weight: bold;">名称</div>
          <n-input type="text" placeholder="必填,请输入名称" style="margin-bottom: 10px;" />
          <div style="font-size: 12pt; font-weight: bold;">类型</div>
          <n-select  placeholder="必填,请选择类型" style="margin-bottom: 10px;" />
          <div style="font-size: 12pt; font-weight: bold;">地址</div>
          <n-input type="text" placeholder="必填,请输入地址" style="margin-bottom: 10px;" />
          <div style="font-size: 12pt; font-weight: bold;">用途</div>
          <n-input type="text" placeholder="必填,请输入用途" style="margin-bottom: 10px;" />
        </div>
        <div style="display: flex; width: 100%; height: 100%; justify-content: flex-end; margin-top: 10px">
          <n-button @click="onAddModalFailed" style="margin-right: 10px">取消</n-button>
          <n-button @click="onAddModalOk" type="primary">添加</n-button>
        </div>
      </div>
    </n-modal>
    <n-modal v-model:show="isShowDetailModal" :segmented="false"
             :mask-closable="false" preset="card" title="xxxxx存储库的详细信息"
             :on-after-leave="onDetailModalAfterLeave"
             style="width: 45%; min-width: 600px">
      <div style="display: flex; width: 100%; height: 100%; flex-direction: column">
        <div style="width: 100%">
          <div style="font-size: 12pt; font-weight: bold;">类型</div>
          <n-select  placeholder="必填,请选择类型" style="margin-bottom: 10px;" disabled/>
          <div style="font-size: 12pt; font-weight: bold;">地址</div>
          <n-input type="text" placeholder="必填,请输入地址" style="margin-bottom: 10px;" disabled />
          <div style="font-size: 12pt; font-weight: bold;">用途</div>
          <n-input type="text" placeholder="必填,请输入用途" style="margin-bottom: 10px;" disabled />
        </div>
        <div style="display: flex; width: 100%; height: 100%; justify-content: flex-end; margin-top: 10px">
          <n-button @click="onDetailModalOk" type="primary">关闭</n-button>
        </div>
      </div>
    </n-modal>
    <n-modal v-model:show="isShowModifyModal" :segmented="false"
             :mask-closable="false" preset="card" title="修改存储库"
             :on-after-leave="onModifyModalAfterLeave"
             style="width: 45%; min-width: 600px">
      <div style="display: flex; width: 100%; height: 100%; flex-direction: column">
        <div style="width: 100%">
          <div style="font-size: 12pt; font-weight: bold;">类型</div>
          <n-select  placeholder="必填,请选择类型" style="margin-bottom: 10px;" />
          <div style="font-size: 12pt; font-weight: bold;">地址</div>
          <n-input type="text" placeholder="必填,请输入地址" style="margin-bottom: 10px;" />
          <div style="font-size: 12pt; font-weight: bold;">用途</div>
          <n-input type="text" placeholder="必填,请输入用途" style="margin-bottom: 10px;" />
        </div>
        <div style="display: flex; width: 100%; height: 100%; justify-content: flex-end; margin-top: 10px">
          <n-button @click="onModifyModalFailed" style="margin-right: 10px">取消</n-button>
          <n-button @click="onModifyModalOk" type="primary">修改</n-button>
        </div>
      </div>
    </n-modal>
    <div style="width: 100%; min-height: 20px">&nbsp;</div>
  </div>
</template>

<script setup>
import {h, reactive, ref} from "vue";
import {NButton, NTag, useDialog, useMessage} from "naive-ui";
import {SearchOutlined, CloseOutlined, DeleteOutlined, PlusOutlined} from "@vicons/antd"
import TableOperationAreaButtonGroup from "@/components/TableOperationAreaButtonGroup.vue";

const dialog = useDialog();
const message = useMessage();

let isShowDetailModal = ref(false)
let isShowModifyModal = ref(false)

function onDetailModalAfterLeave() {
  isShowDetailModal.value = false;
}

function onDetailModalOk() {
  isShowDetailModal.value = false;
}

function onModifyModalAfterLeave() {
}

function onModifyModalFailed() {
  isShowModifyModal.value = false;
}

function onModifyModalOk() {
  isShowModifyModal.value = false;
}

function handleBatchDeleteButtonClicked() {
  dialog.warning({
    title: "批量删除",
    content: "即将删除 24 个条目, 是否继续?",
    positiveText: "确定",
    negativeText: "取消",
    onPositiveClick: () => {
      message.success("删除成功")
    },
  })
}

let isShowAddModal = ref(false)

function handleAddButtonClicked() {
  isShowAddModal.value = true
}

function onAddModalAfterLeave() {

}

function onAddModalFailed(){
  isShowAddModal.value = false;
}

function onAddModalOk() {
  isShowAddModal.value = false;
}

let repoName = ref("")
let repoUsage = ref("")
let repoTypeSelectOptionValue = ref(null)
const repoTypeSelectOptions = [
  {
    label: "全部",
    value: null
  },
  {
    label: "SSH",
    value: "SSH"
  },
  {
    label: "HTTPS",
    value: "HTTPS"
  }
];

let repos = ref([
  {
    "key": "0",
    "name": "ss/sss",
    "content-type": "ssh",
    "location": "git@github.com:swxfll/CMDB.git",
    "create-time": "2012/12/12 00:00:00",
    "usage": "用于调试环境"
  }
]);

const columns = [
  {
    type: "selection",
    fixed: "left"
  },
  {
    title: "名称",
    key: "name",
    fixed: "left",
    width: 200
  },
  {
    title: "类型",
    key: "content-type",
    width: 100
  },
  {
    title: "地址",
    key: "location",
    width: 250
  },
  {
    title: "添加时间",
    key: "create-time",
    width: 220
  },
  {
    title: "用途",
    key: "usage",
    width: 220,
    resizable: true
  },
  {
    title: "操作",
    key: "op",
    render(row) {
      return h(
          TableOperationAreaButtonGroup,
          {
            isShowDetail: true,
            isShowModify: true,
            isShowDelete: true,
            onDetailButtonClicked: () =>{
              isShowDetailModal.value = true;
            },
            onModifyButtonClicked: () => {
              isShowModifyModal.value = true;
            },
            onDeleteButtonClicked: () => {

            }
          }
      );
    },
    fixed: "right",
    width: 150
  }
]

const paginationReactive = reactive({
  page: 2,
  pageSize: 5,
  showSizePicker: true,
  pageSizes: [3, 5, 7],
  onChange: (page) => {
    paginationReactive.page = page;
  },
  onUpdatePageSize: (pageSize) => {
    paginationReactive.pageSize = pageSize;
    paginationReactive.page = 1;
  }
});
</script>

<style scoped>
.repo-view{
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.op-area {
  padding-bottom: 15px;
  display: flex;
}
</style>