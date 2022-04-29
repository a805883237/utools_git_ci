<template>
<div class="container">
  <el-row :gutter="12">
    <el-col :span="6">
      <el-select 
        v-model="type" 
        filterable 
        class="full_width"
        placeholder="Select">
        <el-option
          v-for="item in options"
          :key="item.value"
          :label="item.label + '(' + item.value + ')'"
          :value="item.value"
        />
      </el-select>
    </el-col>
    <el-col :span="18">
      <el-input v-model="title" placeholder="请输入简短更新内容（50字内）" />
    </el-col>
  </el-row>
  <el-row />
  <el-row >
    <el-input 
      v-model="content" >
      <template #prefix><span class="tip_text">描述:</span></template>
    </el-input>
  </el-row>
  <el-row >
    <el-input 
      v-model="scope" >
      <template #prefix><span class="tip_text">影响范围:</span></template>
    </el-input>
  </el-row>

  <div class="result_preview">
    <el-input
      v-model="compuedResult"
      :autosize="{ minRows: 5, maxRows: 8 }"
      type="textarea"
      :disabled="true"
      placeholder="效果预览，请填写上方内容"
    />
    <el-button class="copy_btn" type="primary" :icon="DocumentCopy" size="small" plain @click="handleCopy">复制</el-button>
  </div>
</div>
</template>

<script>
import { reactive, toRefs, ref, onMounted, computed } from "vue";
import { DocumentCopy } from '@element-plus/icons-vue';
import { ElMessage } from 'element-plus'

export default {
  setup() {
    const state = reactive({
      type: 'feat',
      options: [
        {label: '新功能(feature)', value: 'feat'},
        {label: '修补bug', value: 'fix'},
        {label: '更新某功能', value: 'upd'},
        {label: '文档更改', value: 'docs'},
        {label: '格式（不影响代码运行的变动）', value: 'style'},
        {label: '重构（即不是新增功能，也不是修改bug的代码变动）', value: 'refactor'},
        {label: '增加测试', value: 'test'},
        {label: '构建过程或辅助工具的变动', value: 'chore'},
      ],
      title: '',
      content: '',
      scope: '',
      result: '',
    });
    
    const compuedResult = computed(() => {
      const result = `${state.type}: ${state.title}${state.content? '\n\n描述：' + state.content : ''}${state.scope? '\n影响范围：' + state.scope : ''}`;
      state.result = result;
      return result
    })

    /**
     * @param {String} text 需要复制的内容
     * @return {Boolean} 复制成功:true或者复制失败:false  执行完函数后，按ctrl + v试试
     */
    const copyText = (text) => {
      let textareaC = document.createElement('textarea');
      textareaC.setAttribute('readonly', 'readonly'); //设置只读属性防止手机上弹出软键盘
      textareaC.value = text;
      document.body.appendChild(textareaC); //将textarea添加为body子元素
      textareaC.select();
      let res = document.execCommand('copy');
      document.body.removeChild(textareaC);//移除DOM元素
      console.log("复制成功");
      ElMessage({
        message: '复制成功~',
        type: 'success',
      })
      return res;
    }

    const handleCopy = () => {
      if (!state.title) {
        ElMessage.error('简短更新内容为必填项!')
        return;
      }
      copyText(state.result);
    }
    return {
      ...toRefs(state),
      compuedResult,
      handleCopy,
      DocumentCopy,
    }
  }
}


</script>

<style>
html,body {
  padding: 0;
  margin: 0;
}
#app {
  width: 100vw;
  height: 100vh;
  background-color: #f5f5f5;
}
.full_width {
  width: 100%;
}
.container {
  padding: 20px;
}
.container .el-row {
  margin-top: 10px;
}
.container .el-row:first-child {
  margin-top: 0;
}
.tip_text {
  min-width: 4.2em;
  color: #606266;
  padding-right: 10px;
  border-right: 1px solid #dcdfe6;
}
.result_preview {
  margin-top: 40px;
  position: relative;
}
.result_preview .copy_btn {
  position: absolute;
  top:0;
  right: 0;
}
</style>
