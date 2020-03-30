<template>
  <div class="component" :class='{flexTable: fixedTbody}'>
    <table :class='[border && "border-1px", cStyle]' :style="{color: customStyle.txtColor, 'font-size': (customStyle.txtSize || 14)+'px'}">
      <caption></caption>   
      <thead>
        <tr>
          <th v-for='(v, i) in theadList' :key='i' :style="borderColor">{{v}}</th>
        </tr>
      </thead>
      <tbody :style="tbodyH">
        <tr v-for='(tr, i) in currList' :key='i'>
          <td v-for='(td, j) in tr' :key='j' :style="borderColor">{{td}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
  import {VueExtend} from 'godspen-lib'

  export default {
    mixins: [VueExtend.mixin],
    name: 'yTable',
    label: process.env.LABEL,
    style: process.env.STYLE,
    stack: false, // 是否是堆叠模式 ，true：孩子元素会按楼层一个个向下排布， false: 孩子元素绝对定位，随意放置位置
    childLimit: 9999,  // 孩子元素最大限制数
    leaf: false, // 是否是叶子节点，为true的时候该节点下面不能添加节点
    props: {
      list: {
        type: Array,
        default () {
          return []
        },
        editor: {
          // label: '表格数据',
          // desc: '',
          // type: 'data',
          ignore: true
        }
      },
      fixedTbody: {
        type: Boolean,
        default: false,
        editor: {
          ignore: true
        }
      },
      tbodyHeight: {
        type: [String, Number],
        default: '200',
        editor: {
          ignore: true
        }
      },
      border: {
        type: Boolean,
        default: false,
        editor: {
          ignore: true
        }
      },
      cStyle: {
        type: String,
        default: '',
        editor: {
          ignore: true
        }
      },
      customStyle: {
        type: Object,
        default () {
          return {
            txtColor: '#666',
            txtSize: '14',
            stripe1: '#f5f5f5',
            stripe2: '#fff'
          }
        },
        editor: {
          ignore: true
        }
      }
    },
    computed: {
      theadList () {
        return ((this.list || [])[0] || '').split(',')
      },
      currList () {
        return (this.list || []).reduce((arr, v, i) => {
          if (i == 0) return arr
          arr.push(v.split(','))
          return arr
        }, [])
      },
      borderColor () {
        if (this.border) {
          return {
            borderColor: this.customStyle.borderColor || ''
          }
        }
        return {}
      },
      tbodyH () {
        if (this.fixedTbody && !!this.tbodyHeight) {
          return {
            height: `${this.tbodyHeight}px`
          }
        }
        return {}
      },
      stripeStyle () {
        if (this.cStyle !== 'stripe') return null
        return `.stripe thead,.stripe tbody tr:nth-child(2n){background:${this.customStyle.stripe1}}.stripe tbody tr:nth-child(2n-1){background:${this.customStyle.stripe2}}`
      }
    },
    watch: {
      stripeStyle: {
        handler (val) {
          if (!val) return
          this.$nextTick(() => {
            let style = document.getElementById('stripeCss')
            if (!style) {
              style = document.createElement('style')
              style.id = 'stripeCss'
            }
            style.innerHTML = val
            document.head.append(style)
          })
        },
        immediate: true
      }
    },
    editorMethods: {
    },
    methods: {
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component {
    width: 100%;
    height: 100%;
    overflow scroll
    -webkit-overflow-scrolling: touch
    ::-webkit-scrollbar {
      width: 0
      background-color: transparent;
    }
    ::-webkit-scrollbar-thumb{
      background-color: #27314d
    }
    table{
      width: 100%
      line-height 1.5
      border-collapse: collapse
      border-spacing: 0px
    }
    tbody{
      display: block
      overflow-y: auto
      -webkit-overflow-scrolling: touch
    }
    td{
      text-align center
      padding 5px 3px
    }
    th{
      padding 6px 3px
    }
    .border-1px{
      th, td{
        border: 1px solid
      }
    }
    table thead tr, table tbody tr, table tfoot tr {
      box-sizing: border-box
      table-layout: fixed
      display: table
      width: 100%
    }
    &.flexTable{
      overflow: inherit
    }
  }
</style>
