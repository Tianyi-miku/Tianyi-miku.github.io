---
title: Vxetable 筛选过滤
date: 2022-04-17 12:05:13
tags:
    - vue3
    - vxeTable
categories: 业务
---
大量数据需要用到表格展示，又要在前端做筛选过滤排序。
考虑到表格性能原因，最终选择了vxe-table 
​​
<!-- more -->

# 1.第一步
```
   npm install xe-utils@3 vxe-table@next 
```

# 2.在main.ts里面
```
 import { App, createApp } = 'vue'
        import 'xe-utils'
        import VXETable from 'vxe-table'
        import 'vxe-table/lib/style.css'

        function useTable (app: App) {
          app.use(VXETable)

        }

        createApp(App).use(useTable).mount('#app')
```
# 3.新建vue文件，注册全局组件，filter-render与自定义组件名字一样就行。filters为传过去的参数。
```
   <vxe-table
          border
          height="400"
          :data="tableData">
          <vxe-column type="seq" width="60"></vxe-column>
          <vxe-column field="name" title="文本筛选" :filters="[{data: null}]" :filter-render="{name: 'FilterInput'}"></vxe-column>
          <vxe-column field="role" title="实现条件的筛选" :filters="[{data: {type: 'has', name: ''}}]" :filter-render="{name: 'FilterComplex'}"></vxe-column>
          <vxe-column field="age" title="实现内容的筛选" :filters="[{data: {vals: [], sVal: ''}}]" :filter-render="{name: 'FilterContent'}"></vxe-column>
          <vxe-column field="address" title="实现Excel复杂的筛选" sortable :filters="[{data: {vals: [], sVal: '', fMenu: '', f1Type:'', f1Val: '', fMode: 'and', f2Type: '', f2Val: ''}}]" :filter-render="{name: 'FilterExcel'}"></vxe-column>
        </vxe-table>
```
```
   import { defineComponent, ref } from 'vue'

        export default defineComponent({
          setup  () {
            const tableData = ref([
              { id: 10001, name: 'Test1', role: 'Develop', sex: 'Man', age: '28', address: 'Shenzhen' },
              { id: 10002, name: 'Test2', role: 'Test', sex: 'Women', age: '22', address: 'Guangzhou' },
              { id: 10003, name: 'Test3', role: 'PM', sex: 'Man', age: '32', address: 'Shanghai' },
              { id: 10004, name: 'Test4', role: 'Designer', sex: 'Women', age: '23', address: 'Shenzhen' },
              { id: 10005, name: 'Test5', role: 'Develop', sex: 'Women', age: '30', address: 'Shanghai' },
              { id: 10006, name: 'Test6', role: 'Designer', sex: 'Women', age: '21', address: 'Shenzhen' },
              { id: 10007, name: 'Test7', role: 'Test', sex: 'Man', age: '29', address: 'Shenzhen' },
              { id: 10008, name: 'Test8', role: 'Develop', sex: 'Man', age: '35', address: 'Shenzhen' }
            ])

            return {
              tableData
            }
          }
        })
```
```
import FilterInput from '@/components/filter-methods/FilterInput.vue'


//全局组件
app.component(FilterInput.name, FilterInput)
```

# 4新建js文件，将模板与重置方法，筛选方法填进去。这里筛选方法请自行根据要求来过滤。
```
import XEUtils from 'xe-utils'
import VXETable from 'vxe-table'

// 创建一个简单的输入框筛选
VXETable.renderer.add('FilterInput', {
  // 筛选模板
  renderFilter(renderOpts, params) {
    return [
      <filter-input key={params.columnIndex} params={params}></filter-input>
    ]
  },
  showFilterFooter: false,
  // 重置数据方法
  filterResetMethod(params) {
    const { options } = params
    options.forEach(option => {
      option.data = ''
    })
  },
  // 重置筛选复原方法（当未点击确认时，该选项将被恢复为默认值）
  filterRecoverMethod(option) {
    option.data = ''
  },
  // 筛选方法
  filterMethod(params) {
    const { option, row, column } = params
    const { data } = option
    if (!data) {
      return false
    }
    //切割空格
    let arr = data.split(' ')
    if (arr.length > 1) {
      arr = arr.filter(item => item)
    }
    let cellValue = XEUtils.get(row, column.property)
    if (cellValue) {
      let value = false
      for (let index = 0; index < arr.length; index++) {
        const element = arr[index]
        if (cellValue.toUpperCase().indexOf(element.toUpperCase()) > -1) {
          value = true
          return true
        }
      }
      return value
    }
  }
})
```
页面
```
<template>
  <div class="my-filter-input">
    <vxe-input type="text" v-model="demo1.option.data" placeholder="支持回车筛选" @keyup="keyupEvent" @input="changeOptionEvent"></vxe-input>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, reactive } from 'vue'
import { VxeInputEvents, VxeGlobalRendererHandles } from '../../../../../types/index'

export default defineComponent({
  name: 'FilterInput',
  props: {
    params: Object as PropType<VxeGlobalRendererHandles.RenderFilterParams>
  },
  setup (props) {
    const demo1 = reactive({
      option: null as any
    })

    const load = () => {
      const { params } = props
      if (params) {
        const { column } = params
        const option = column.filters[0]
        demo1.option = option
      }
    }

    const changeOptionEvent = () => {
      const { params } = props
      const { option } = demo1
      if (params && option) {
        const { $panel } = params
        const checked = !!option.data
        $panel.changeOption(null, checked, option)
      }
    }

    const keyupEvent: VxeInputEvents.Keyup = ({ $event }) => {
      const { params } = props
      if (params) {
        const { $panel } = params
        if ($event.keyCode === 13) {
          $panel.confirmFilter($event)
        }
      }
    }

    load()

    return {
      demo1,
      changeOptionEvent,
      keyupEvent
    }
  }
})
</script>

<style scoped>
.my-filter-input {
  padding: 10px;
}
</style>
```
参考[来源](https://gitee.com/xuliangzhan_admin/vxe-table/blob/master/examples/plugins/table/renderer/components/FilterInput.vue#)

​