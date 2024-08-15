## 基于uv-ui的配置化表单库

> 该库默认使用的UI库为 uv-ui
>
> 具体配置以及表单属性方法请参考 [官方链接](https://www.uvui.cn/components/input.html)


### 字段说明
#### option 除了以下属性外，其余与uv-ui form表单属性一致
| 字段      | 类型      | 默认值   | 说明                                               |
|---------|---------|-------|--------------------------------------------------|
| value / v-model  | Object  | -     | 绑定表单值                                            |
| border  | String  | none  | 全局配置input边框类型，surround-四周边框，bottom-底部边框，none-无边框 |
| columns | Array   | []    | 表单字段配置列表                                         |
| detail  | Boolean | false | 是否详情页                                            |

#### column
| 字段     | 类型              | 默认值   | 说明                                                                                     |
|--------|-----------------|-------|----------------------------------------------------------------------------------------|
| prop   | String          | -     | 表单字段                                                                                   |
| type   | String          | input | 表单渲染组件类型，有radio、checkbox、select、datetime、calendar、textarea、number、file、input，默认渲染input |
| dictData | Array           | []    | dictData为字典数据，格式为{label: '展示值', value: '选中值'}                                          |
| rules | Array \| Object | -     | 校验规则                                                                                   |
| isSlot | Boolean         | false | 是否为插槽                                                                                  |

#### method
| 方法名 | 说明 |
|--------|---------|
| submit | 提交表单 |
| reset  | 重置表单 |
