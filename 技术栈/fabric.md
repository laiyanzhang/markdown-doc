# fabric
## 0.版本迁移相关
### 1.v6版本相关
| 特性 | v6之前 | v6之后 |
| --- | --- | --- |
| 对象导入 | 导入`fabric`后通过`fabric.Rect`调用 | 例：单独导入`Rect`作为单一对象类型使用 |
| 对象默认值 | 写入`prototype`原型链上对应属性 | 例：写入`Rect.ownDefaults`变量 |
| 原型链 | 支持设置原型链上的属性 | 不支持设置原型链上的属性 |
| 图像导入 | 函数参数处理`fromURL(url, callback)` | Promise对象`fromURL(url)` |
| 图像滤镜 | `Image.filters.Sepia()` | `filters.Sepia()`|
| 动画 | 支持`rect.animate()` | 仅支持`util.animate()` |
| 组更新 | 支持`group.addWithUpdate` | 不支持`group.addWithUpdate`，自动更新 |
| 自定义属性序列化 | 重新定义`toObject`函数 | 序列化时传入自定义属性`toObject(customeProperties)` |
| 更名1 | Object | FabricObject |
| 更名2 | Text | FabricText |
| 更名3 | Image | FabricImage |

### 2.v4版本
- rotatingPointOffset不再支持，通过mtr修改偏移量
```javascript
const mtrControl = fabric.Object.prototype.controls.mtr;
mtrControl.offsetY = -10
```

## 1.简单示例
```javascript
import { defineComponent, onMounted } from 'vue';
import { Canvas, Rect } from 'fabric';
export default defineComponent({
  setup() {
    let canvas = null
    let rect = null
    onMounted(() => {
      canvas = new Canvas('canvas');
      rect = new Rect({
        left: 100,
        top: 100,
        fill: 'red',
        width: 20,
        height: 20
      });
      
      canvas.add(rect);
      canvas.renderAll(); // 确保渲染
    });
    const handleChangeRect = () => {
      rect.set({ left: 20, top: 50 })
      canvas.renderAll()
    }
    return {
      handleChangeRect
    };
  },
})
```

## 2.概念
- 定义：内部封装了对于canvas的原生操作，用户能够像操作对象一样去操作画布上的元素
- 原型函数：`fabric.Object.prototype.getAngleInRadians = function() {};`

## 3.对象操作
- 创建：`rect = new fabric.Rect({ width: 10, height: 20, fill: '#f55', opacity: 0.7 })`
- 默认参数：当创建时未传入参数，对象本身也存在默认参数
- 获取参数：`rect.get('width') / rect.getWidth()`
- 设置参数：`rect.set('fill', 'red') / rect.set({ strokeWidth: 5, stroke: 'rgba(100,200,200,0.5)' })`
- 克隆：`rect.clone()`


## 4.画布操作
- 创建：`canvas = new fabric.Canvas('canvas')`传入绑定在canvas元素上的id
- 添加对象：`canvas.add(rect)`
- 获取单个对象：`canvas.item(0)`获取第一个添加的对象
- 获取全部对象：`canvas.getObjects()`
- 移除对象：`canvas.remove(rect)`
- 绘制对象：`canvas.renderAll()`在添加对象后会自动调用，设置对象参数后需手动调用


## 5.互动性
- 默认：对象默认支持操纵（拖动、旋转、缩放），画布内也可将多个对象组合在一块群组操纵
- 画布手动设置：`canvas.selection = false`禁止群组操纵
- 对象手动设置：`rect.set('selectable', false)`禁止单个操纵
- 完全屏蔽交互：`staticCanvas = new fabric.StaticCanvas('canvas')`


## 6.图像
- 图像元素绘制在画布上
```javascript
let imgElement = document.getElementById('my-image');
let imgInstance = new fabric.Image(imgElement, {
  left: 100,
  top: 100,
  angle: 30,
  opacity: 0.85
});
```
- 图像url绘制在画布上
```javascript
import image from '@/assets/page1.jpeg'
let img = null
const handleAddImg = () => {
  fabric.Image.fromURL(image, (img) => {
    canvas.add(img)
  })
}

// 6.0之后
const handleAddImg = async () => {
  const img = await FabricImage.fromURL(image)
  canvas.add(img)
}
```
- 图像滤镜
```javascript
// 6.0版本后filters单独引入，不作为FabricImage中的属性
const handleAddImgFilter = () => {
  img.filters.push(new fabric.Image.filters.Sepia())
  img.applyFilters()
  canvas.renderAll()
}
```


## 7.动画
- 通过设置动画的全流程，监听流程变化不断去改变图形属性
```javascript
// 针对对象动画
rect.animate('angle', '-=5', { onChange: canvas.renderAll.bind(canvas) });

// 动画内对对象操纵
const animate = util.animate({
  startValue: 0,
  endValue: 45,
  duration: 2000,
  onChange: (v) => {
    rect.set('angle', v)
    canvas.renderAll()
  }
})
```
- 控制动画：直接操作返回的animate对象或者通过`runningAnimations`获取所有动画对象数组
```javascript
// 终止动画
animate.abort()
```


## 8.颜色
- 定义颜色：`Color('#f55')`
- 转化颜色：`Color('#f55').toRgb()`


## 9.渐变
- gradientUnits
  - pixels：coords中坐标设置以像素为单位
  - percentage：coords中坐标设置以百分比为单位
```javascript
const gradient = new fabric.Gradient({
  type: 'linear',
  gradientUnits: 'pixels', // or 'percentage'
  coords: { x1: 0, y1: 0, x2: 0, y2: 20 },
  colorStops:[
    { offset: 0, color: '#000' },
    { offset: 1, color: '#fff'}
  ]
})
```

## 10.文本
- 创建：`const text = new fabric.Text("Hellow World!", options)`
- options内部各基础属性
  - 字体：`fontFamily: 'Arial'`
  - 字体大小：`fontSize: 16`
  - 字体粗细：`fontWeight: 'normal'`
  - 文本装饰
    - 下划线：`underline: true`
    - 删除线：`linethrough: true`
    - 上划线：`overline: true`
  - 字体阴影：`shadow: 'rgba(0,0,0,0.3) 5px 5px 5px'`
  - 字体样式：`fontStyle: 'italic'`
  - 描边颜色：`stroke: 'red'`
  - 描边宽度：`strokeWidth: 1`
  - 文本对齐：`textAlign: 'left'`
  - 行高：`lineHeight: 1.2`
  - 文本背景颜色：`textBackgroundColor: 'rgb(0,200,0)'`

## 11.事件
- 画布绑定事件
```javascript
canvas.on('mouse:down', function(options) {
  console.log(options.e, options.target)
});
```
- 对象绑定事件
```javascript
rect.on('selected', function() {
  console.log('selected a rectangle');
});
```

## 12.组
```javascript
// 创建一个圆形对象
const circle = new fabric.Circle({
  radius: 100,
  fill: '#eef',
  scaleY: 0.5,
  originX: 'center',
  originY: 'center'
});

// 创建一个文本对象
const text = new fabric.Text('hello world', {
  fontSize: 30,
  originX: 'center',
  originY: 'center'
});

// 创建一个包含圆形和文本的组合对象
const group = new fabric.Group([circle, text], {
  left: 150,
  top: 100,
  angle: -10
});

// 将组合对象添加到画布
canvas.add(group);
```
- 访问组中对象：`group.item(index)`
- 新增组中对象：`group.add(object)`


## 13.序列化
- 画布/对象API
  - toJSON：内部等同于直接调用`toObject()`，`JSON.stringify(canvas)`等同于`JSON.stringify(canvas.toJSON())`
  - toObject：返回对象所有参数
  - toSVG：返回SVG文件字符串
  - toDatalessJSON：当画布中渲染svg图形时，因svg图形转化为json字符串时会存在上百个path。此时通过该API转化得出的json字符串直接包含svg图形的文件路径
- 自定义toObject导出额外属性
```javascript
rect.toObject = (function(toObject) {
  return function() {
    return fabric.util.object.extend(toObject.call(this), {
      name: this.name
    });
  };
})(rect.toObject);
```
- 画布API
  - loadFromJSON：根据传入的json字符串转化为画布内容
  - loadFromDatalessJSON：根据传入的DatalessJSON字符串转化为画布内容
- 库API
```javascript
// 根据svg字符串加载后转化为画布内容
loadSVGFromString(svgString, function(objects, options) {
  var obj = util.groupSVGElements(objects, options);
  canvas.add(obj)
});
// 根据svg路径加载后转化为画布内容
loadSVGFromURL(svgUrl, function(objects, options) {
  var obj = util.groupSVGElements(objects, options);
  canvas.add(obj)
});
```

## 14.子类
```javascript
const LabeledRect = fabric.util.createClass(fabric.Rect, {
  type: 'labelRect',
  initialize: function(options) {
    options || (options = { });

    this.callSuper('initialize', options);
    this.set('label', options.label || '');
  },
  toObject: function() {
    return fabric.util.object.extend(this.callSuper('toObject'), {
      label: this.get('label')
    });
  },
  _render: function(ctx) {
    this.callSuper('_render', ctx);

    ctx.font = '20px Helvetica';
    ctx.fillStyle = '#333';
    ctx.fillText(this.label, -this.width/2, -this.height/2 + 20);
  }
})
```


## 15.自由绘制
```javascript
canvas = new fabric.Canvas(canvasId.value, {
  width: 600,
  height: 400,
  isDrawingMode: true, // 开启绘制模式
});
canvas.freeDrawingBrush.color = 'red'; // 设置颜色
canvas.freeDrawingBrush.width = 5; // 设置宽度
```

## 16.定制化
- 对象参数
  - 锁定对象：`lockMovementX`是否禁止水平移动
  - 修改边角：`hasBorders`是否显示边框、`cornerColor`控制点颜色
  - 禁用选择：`selectable`是否允许选择
  - 虚线边框：`strokeDashArray`
  - 可点击区域：`perPixelTargetFind`是否仅按对象的实际内容单击/拖动对象
  - 旋转点偏移量：`rotatingPointOffset`在4.0版本之后废弃
- 画布参数
  - 禁用选择：`selection`是否允许选择
  - 设置拖拽框选框：`selectionColor`框选框颜色