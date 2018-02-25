
## 后台管理 ##
基于Vue.js 2.x系列 + iView UI 的后台管理系统

## 功能 ##
- [x] iView UI
- [x] 表格
- [x] 表单
- [x] 导出Csv
- [x] 图表 :bar_chart:


## 目录结构介绍 ##

	|-- build                            // webpack编译配置文件目录
	|-- config                           // 项目配置目录
	|-- src                              // 源码目录
	|   |-- components                   // 组件
	|       |-- common                   // 公共组件
	|       |-- project                  // 主要路由页面
	|           |-- welcome.vue          // 首页
	|   |-- router                       // 路由
	|   |-- util                         // 公共方法 
	|   |-- vuex                         // 状态管理目录  
	|   |-- App.vue                      // 页面入口文件
	|   |-- main.js                      // 程序入口文件，加载各种公共组件
	|-- static                           // 静态资源
	|-- .babelrc                         // ES6语法编译配置
	|-- .editorconfig                    // 代码编写规格
	|-- .gitignore                       // 忽略的文件
	|-- index.html                       // 入口html文件
	|-- package.json                     // 项目及工具的依赖配置文件


## 安装步骤 ##

	git clone xxxx.git       // 把项目克隆到本地
	cd vue-cli-framework     // 进入模板目录
	npm install              // 安装项目依赖

## 本地开发 ##

	// 开启服务器，浏览器访问 http://localhost:8089
	npm run dev

## 构建生产 ##

	// 执行构建命令，生成的dist文件夹放在服务器下即可访问
	npm run build

## 组件使用说明 ##

### 1. 表格分页 ###
#### usage ####
```html
  // columns: 列表标题；  格式：[{title: '序号', type: 'index'}, {title: '名称', key: 'name'}]
  // data: 列表数据       格式：[{name: '啦啦啦'}]
  <Table stripe :columns="columnsTitle" :data="columnsItems"></Table>
  // total: 列表总条数
  // page-size: 一页多少条数据
  // @on-change：页码改变的回调，返回改变后的页码
  <Page :total="count" :page-size='pageSize' @on-change="pageChange" show-total show-elevator></Page>
```
#### 代码 ####
```JavaScript
<template>
    <div class="list">
      <template v-if="count">
        <Table stripe :loading="loading" :columns="columnsTitle" :data="columnsItems"></Table>
        <div style="display: flex; justify-content: flex-end;margin: 12px 0;">
          <Page :total="count" :page-size='pageSize' @on-change="pageChange" show-total show-elevator></Page>
        </div>
      </template>
      <template v-if="!count">
        <Alert show-icon>
          没有搜索到数据！
          <template slot="desc">哎呀，数据藏到哪里去了呢？大家赶快去找一找呀！</template>
        </Alert>
      </template>
    </div>
</template>
<script>
  export default {
    name: 'paging',
    data () {
      return {
        pageSize: 20, // 每页显示20条数据
        currentPage: 1, // 当前页码
        count: 0,  // 总记录数
        loading: false,   // 列表数据加载中
        columnsItems: [], // 列表数据
        columnsTitle: [    // 列表标题
          {title: '序号', type: 'index', align: 'center'},
          {title: '名称', key: 'name', align: 'center'},
          {title: '编号', key: 'no', align: 'center'},
          { title: '操作',
            key: 'action',
            align: 'center',
            render: (h, params) => {
              return h('div', [
                h('Button', {
                  props: {
                    type: 'primary',
                    size: 'small'
                  },
                  style: {
                    marginRight: '5px'
                  },
                  on: {
                    click: () => {
                      this.$Message.success('修改成功')
                    }
                  }
                }, '修改'),
                h('Button', {
                  props: {
                    type: 'error',
                    size: 'small'
                  },
                  on: {
                    click: () => {
                      this.$Message.success('删除成功')
                    }
                  }
                }, '删除')
              ])
            }
          }      // 按钮
        ]
      }
    },
    mounted: function () {
      console.log('------mounted------')
      // 请求第一页数据
      this.getList()
    },
    methods: {
      // 获取数据
      getList () {
        this.loading = true  // 打开列表数据加载中动画
        //
        let url = `/ws/xx`
        let postData = {
          begin: this.pageSize * (this.currentPage - 1),
          rows: this.pageSize
        }
        this.$http.post(url, postData, {'emulateJSON': true, 'emulateHTTP': true})
          .then(({body}) => {
            // 子组件监听到count变化会自动更新DOM
            console.log(body)
            this.count = body.count   // 列表数据总条数
            this.columnsItems = body.content  // 列表数据内容
            this.loading = false  // 关闭列表数据加载中动画
          })
      },
      // 从page组件传递过来的当前page
      pageChange (page) {
        console.log('当前页码：' + page)
        this.currentPage = page
        this.getList()
      }
    }
  }
</script>
```

### 2. 导出Csv ###
#### usage ####
```html
 <mc-csv :titleData="titleData" :listData="listData"></mc-csv>
```
```JavaScript
 import ExportCsv from '../common/exportCsvComponent.vue'
  export default {
    name: 'exportCsv',
    data () {
      return {
        titleData: [
          {title: '序号', key: 'no'},
          {title: '名称', key: 'name'}
        ],
        listData: [
          {no: 'no1', name: 'name1'},
          {no: 'no2', name: 'name2'},
          {no: 'no3', name: 'name3'}
        ]
      }
    },
    components: {
      'mc-csv': ExportCsv
    }
  }
```

### 3.图表 ###
#### 折线图usage ####
```html
  // title: 图表标题  xdata: 图表x轴  data: 图表数据
  <mc-basic-line :title="title" :xdata="xdata" :data="data"></mc-basic-line>
```
```javascript
  import BasicLine from '@/components/common/echarts/basicLineComponent'
  export default {
    name: '',
    data () {
      return {
        title: '自定义折线图',
        data: [5, 18, 10, 10, 1, 12, 5],
        xdata: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
      }
    },
    components: {
      'mc-basic-line': BasicLine
    }
  }
```
#### 饼图usage ####
```html
  // title: 图表标题  data: 图表数据
  <mc-basic-pie :title="title" :data="data"></mc-basic-pie>
```
```javascript
  import BasicPie from '@/components/common/echarts/basicPieComponent'
  export default {
    name: '',
    data () {
      return {
        title: '自定义饼图',
        data: [
          {value: 1, name: 'aaa'},
          {value: 2, name: 'bbb'},
          {value: 3, name: 'ccc'},
          {value: 4, name: 'ddd'},
          {value: 5, name: 'eee'}
        ]
      }
    },
    components: {
      'mc-basic-pie': BasicPie
    }
  }
```

