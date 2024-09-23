## 自定义组件
## 说明
该组件是基于vue2.0+Element组件库的自定义组件,  

项目node.js版本推荐:14.21
## 安装依赖
npm  

```
npm i

npm i element-ui -S
```
## 使用手册
ComponentAdd.vue(创建组件)  

`@addComponent`  

该函数处理添加不同的组件,传输到ComponentList.vue中  

ComponentList.vue(添加的组件列表)  

`props`  

// 传入组件内容  

assembly(拥有本地数据的列表)  

components(拥有本地数据的列表)  

dataCom(传输给后端的列表)  

// 是否隐藏删除按钮  

button(默认使用时不隐藏,可传入true来隐藏删除按钮)

`@removeComponent`  

该函数处理删除不同的组件
