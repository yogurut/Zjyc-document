# element-ui 点击行如何获取table的行索引

表头

```html
<el-table :data="list" v-loading.body="listLoading" element-loading-text="Loading..."  border fit                  
 :row-class-name="tableRowClassName"
  @row-click = "onRowClick"           
  highlight-current-row style="width: 100%">
  <el-table-column align="center" prop="passtime" label="Time" width="180">
```

Js:

```js
 tableRowClassName ({row, rowIndex}) { 
  //把每一行的索引放进row 
  row.index = rowIndex; 
  },
 onRowClick (row, event, column) { 
  //行点击消除new标记 
   var index = row.index; 
   var deleteIndex = Array.indexOf(this.showIndexArr, index); 
   if (deleteIndex != -1) {
   this.showIndexArr.splice(deleteIndex,1);  
}
}
```

