# Class与Style绑定
## 绑定HTML Class
### 对象语法
可以传给v-bind:class一个对象，以动态地切换class：
```javascript
<div v-bind:class="{active:isActive}"></div>
```
### 数组语法
可以把一个数组传给 v-bind:class，以应用一个 class 列表：
```javascript
<div v-bind:class="[activeClass, errorClass]"></div>
```
### 用在组件上
当在一个自定义组件上使用class属性时，这些类将被添加到该组件的根元素上。这个元素上已经存在的类不会被覆盖
## 绑定内联样式
### 对象语法
```javascript
<div v-bind:style={"color:activeColot, fontSize:fontSize + 'px'"}></div>
```
直接绑定到一个样式对象通常更好，这会让模板更清晰
```javascript
<div v-bind:style="styleObjiect"></div>
```
### 数组语法
v-bind:style 的数组语法可以将多个样式对象应用到同一个元素上：
```javascript
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```
### 多重值（还未理解，需添加）
