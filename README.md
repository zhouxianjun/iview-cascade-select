# iview-cascade-select

## Project setup

```
npm install iview-cascade-select
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### Lints and fixes files

```
npm run lint
```

## API

### selects Attributes

| 属性      | 说明             | 类型    | 参数 | 默认值 |
| --------- | ---------------- | ------- | ---- | ------ |
| selects   | 选择数据         | Array   | -    | []     |
| inline    | 是否内联显示     | Boolean | -    | true   |
| gap       | 间隔像素         | Number  | -    | 5      |
| all-text  | 显示全部的文字   | String  | -    | 全部   |
| all       | 是否显示全部     | Boolean | -    | true   |
| clearable | 是否可以清空选项 | Boolean | -    | true   |
| placeholder | 选择框默认文字                                                                     | String   | 请选择       |
| placement   | 弹窗的展开方向，可选值为 top、bottom、top-start、bottom-start、top-end、bottom-end | String   | bottom-start |
| transfer    | 是否将弹层放置于 body 内                                                           | Boolean  | false        |
| cache     | 是否缓存         | Boolean | -    | true   |

### select Configs

| 属性        | 说明                                                                               | 类型     | 默认值       |
| ----------- | ---------------------------------------------------------------------------------- | -------- | ------------ |
| style       | 样式                                                                               | Object   | -            |
| label-style | label 样式                                                                         | Object   | -            |
| label       | label 名称                                                                         | String   | -            |
| all         | 是否显示全部                                                                       | Boolean  | true         |
| clearable   | 是否可以清空选项                                                                   | Boolean  | true         |
| placeholder | 选择框默认文字                                                                     | String   | 请选择       |
| placement   | 弹窗的展开方向，可选值为 top、bottom、top-start、bottom-start、top-end、bottom-end | String   | bottom-start |
| transfer    | 是否将弹层放置于 body 内                                                           | Boolean  | false        |
| options     | 获取数据函数                                                                       | Function | -            |
| filter      | 过滤数据函数                                                                       | Function | -            |

### Events

| 事件名 | 说明                 | 参数                |
| ------ | -------------------- | ------------------- |
| change | 每个下拉框改变时触发 | 当前下标,当前下标值 |
