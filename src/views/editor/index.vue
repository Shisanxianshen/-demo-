<!--
 * @des:编辑器页面 
-->
<template>
  <div style="padding-bottom: 20px">
    <div class="wrap">
      <!-- 文章标题 -->
      <template>
        <div>
          <span class="label"> 文章标题:</span>
          <el-input v-model="title" placeholder="请输入文章标题"></el-input>
        </div>
      </template>
      <template>
        <div>
          <span class="label"> 介绍:</span>
          <el-input v-model="introduce" placeholder="请输入文章介绍"></el-input>
        </div>
      </template>
      <!-- 模块 -->
      <template>
        <div>
          <span class="label">选择文章模块:</span>
          <el-select v-model="moudle" placeholder="请选择文章模块">
            <el-option
              v-for="item in modelOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </div>
      </template>
      <!-- 编辑器 -->
      <mavon-editor ref="md" v-model="textValue" @imgAdd="$imgAdd" @imgDel="$imgDel" />
    </div>
    <button class="submit" @click="submit">提交</button>
  </div>
</template>
<script>
export default {
  name: 'editor',
  data() {
    return {
      textValue: '',
      moudle: '',
      title: '',
      introduce:'',
      modelOptions: [
        {
          value: 'writeCode',
          label: '前端手写合集',
        },
        {
          value: 'vue',
          label: 'vue源码解析',
        },
        {
          value: 'css',
          label: '奇妙的css',
        },
        {
          value: 'http',
          label: 'http基础知识',
        },
        {
          value: 'koa',
          label: 'koa基础应用',
        },
        {
          value: 'webSet',
          label: '网站部署相关',
        },
        {
          value: 'interview',
          label: '面试收集',
        },
        {
          value: 'gossip',
          label: '闲谈',
        },
      ],
    }
  },
  methods: {
    submit() {
      if (!this.title) {
        this.$message.error('请输入文章标题')
        return
      }
      if (!this.moudle) {
        this.$message.error('请选择文章模块')
        return
      }
      if (!this.introduce) {
        this.$message.error('文章介绍不能为空！')
        return
      }
      if (!this.textValue) {
        this.$message.error('发布内容不能为空！')
        return
      }

      this.$ajax.post('/saveArticle', {
        title: this.title,
        content: encodeURIComponent(this.textValue.replace(/'/g,'"')),
        module: this.moudle,
        author: this.$store.state.user.info.name,
        introduce: this.introduce,
      }).then(res => {
        if(res.code === 0) this.$message.success('发布成功')
      })
    },
    // 新增图片
    async $imgAdd(pos,file){
      let formdata = new FormData()
      formdata.append('image', file)
      const data = await this.$ajax.post('/uploadImage',formdata,'noheader') 
      this.$refs.md.$img2Url(pos, data.url)
    },
    // 删除图片
    async $imgDel(file){
      const data = await this.$ajax.post(`/deleteImage`,{url:file[0]})
    },
  },
}
</script>
<style lang="less" scoped>
.markdown-body {
  min-height: 800px;
  position: relative;
  z-index: 0;
}
.wrap {
  width: 1200px;
  margin: 0 auto;
  padding-top: 100px;
}
.el-input {
  width: 800px;
  margin-bottom: 30px;
}
.el-select {
  width: 800px;
  margin-bottom: 30px;
}
.label {
  display: inline-block;
  width: 150px;
}
.editor-t {
  text-align: center;
  font-size: 32px;
  font-weight: bold;
  margin-bottom: 20px;
}
.submit {
  width: 120px;
  height: 40px;
  font-size: 16px;
  display: block;
  margin: 50px auto;
}
</style>
