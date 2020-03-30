<template>
  <div class="component-editor">
    <input type="file" @change='handleChange' accept=".csv" />
    <div class="el-upload__tip" style='margin-bottom: 10px'>初始化数据，只能上传csv文件，且不超过500kb</div>
    <el-form size="mini">
      <el-form-item label="固定表头">
        <el-checkbox v-model="componentInfo.fixedTbody"></el-checkbox>
      </el-form-item>
      <el-form-item label="表格高度" v-if='componentInfo.fixedTbody'>
        <el-input placeholder="请输入表格内容高度" v-model.number="componentInfo.tbodyHeight" type='number'><template slot="append">px</template></el-input>
      </el-form-item>
      <el-form-item label="样式">
        <el-select v-model="componentInfo.cStyle" placeholder="请选择">
          <el-option label="无" value=""></el-option>
          <el-option label="条纹" value="stripe"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="边框">
        <el-checkbox v-model="componentInfo.border"></el-checkbox>
      </el-form-item>
      <el-form-item label="文字颜色">
        <el-color-picker v-model="componentInfo.customStyle.txtColor" show-alpha></el-color-picker>
      </el-form-item>
      <el-form-item label="文字大小">
        <el-input placeholder="请输入数值" v-model="componentInfo.customStyle.txtSize" type='number'><template slot="append">px</template></el-input>
      </el-form-item>
      <el-form-item label="边框颜色" v-if='componentInfo.border'>
        <el-color-picker v-model="componentInfo.customStyle.borderColor" show-alpha></el-color-picker>
      </el-form-item>
      <template v-if='componentInfo.cStyle == "stripe"'>
        <el-form-item label="条纹颜色一">
          <el-color-picker v-model="componentInfo.customStyle.stripe1" show-alpha></el-color-picker>
        </el-form-item>
        <el-form-item label="条纹颜色二">
          <el-color-picker v-model="componentInfo.customStyle.stripe2" show-alpha></el-color-picker>
        </el-form-item>
      </template>
    </el-form>
  </div>
</template>

<script>
  export default {
    name: 'maliangeditor',
    props: {
      // 编辑器会传递给编辑面板组件的属性值，编辑器可以修改这些值来达到控制组件数据的作用
      componentInfo: { // 固定字段，收集所有属性值
        type: [Object],
        default () {
          return {
            list: [],
            cStyle: '',
            tbodyHeight: 200,
            customStyle: {
              stripe1: '#f5f5f5',
              stripe2: '#ffffff'
            }
          }
        }
      }
    },
    data: function () {
      return {
        file: null,
      }
    },
    computed: {
    },
    watch: {
      'componentInfo': {
        handler (v) {
          console.log(v)
        },
        deep: true
      }
    },
    mounted: function () {
    },
    methods: {
      handleChange (e) {
        let that = this
        let file = e.target.files && e.target.files[0]
        if (!file) return
        if (file.size > 1024 * 512) {
          this.$alert('初始化数据不能超过512kb')
          return
        }
        var reader = new FileReader()
        reader.onload = function() {
          that.$set(that.componentInfo, 'list', this.result.trim().split(/\n/g) || [])
        }
        reader.readAsText(file, 'gb2312')
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component-editor {
  }
</style>
