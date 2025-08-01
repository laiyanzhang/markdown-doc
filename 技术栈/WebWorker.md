# Web Worker
## 1.基础概念
- 定义：运行在后台的 JavaScript，独立于其他脚本，用于处理需要大量计算的耗时任务，避免阻塞主线程导致页面卡顿
- 判断是否使用标准：运算超过16ms（一帧的时间）；用户操作后出现明显卡顿或延迟
- 适用情境
  - 大数据处理​：处理大型数组、矩阵运算、大数据集分析
  - 图像/视频处理​：像素级操作、滤镜应用、图像识别
  - 复杂算法​：加密解密、压缩解压、机器学习推理
  - ​长时间运行任务​：日志分析、数据排序、复杂计算
  - 实时数据处理​：WebSocket 数据处理、传感器数据分析
- 注意事项
  - 数据传输​：Worker 和主线程之间通过消息传递数据，数据会被结构化克隆算法处理（类似深拷贝），大数据传输会有性能开销
  - ​启动成本​：Worker 初始化需要50-200ms
  - ​内存占用​：每个 Worker 都有独立的内存空间，创建过多 Worker 会增加内存消耗
  - 生命周期​：Worker 需要显式终止`worker.terminate()` / `self.close()`，否则会持续存在，造成内存泄漏
  - 调试​：Worker 脚本有独立的调试上下文，在开发者工具中可以单独查看
  - 兼容性​：现代浏览器都支持 WebWorker，但在某些特殊环境（如某些移动浏览器）可能需要降级处理

## 2.与主线程区别

|特性	|主线程 JS 代码	|WebWorker JS 代码|
| -- | -- | -- |
|DOM 访问	|可以访问 DOM	|不能访问 DOM|
|全局对象	|window	|self 或 this|
|UI 操作	|可以直接操作 UI	|不能直接操作 UI|
|阻塞影响	|阻塞会导致页面卡顿	|不会阻塞主线程|
|通信方式	|直接调用函数	|通过 postMessage 通信|
|引入脚本	|直接 `<script>` 标签	|使用 `importScripts()`|
|错误处理	|常规 try-catch	|需要通过 onerror 监听|
|内存	|共享主线程内存	|独立内存空间|
|Web API 支持	|支持全部 API	|有限支持 (如不支持 localStorage)|

> 不可用的常用API：document、Element、alert、confirm、localStorage、sessionStorage、WebSocket
> worker与主线程对比：worker能够并行处理数据，多worker并行时需注意 worker 数量 ≤ CPU 核心数

## 3.API
- `worker.postMessage({ type: 'render', canvas: offscreen }, [offscreen])`
  - 作用：向worker传递信息
  - 第一个参数：发送给 Worker 的消息内容​（普通对象，会被结构化克隆）
  - 第二个参数：数组，声明哪些对象需要转移所有权​（ArrayBuffer 、offscreen等均需声明）
  - 所有权转移：转移不可逆，即使进程销毁，如`<canvas>`永久失去所有权
- `workerFilter.onmessage = (e) => {}`：`e.data`为从worker中接收到的信息内容
- `workerDrawer.terminate()`：用于终止worker，避免内存泄漏；worker对象仍可访问，但后续其他调用无效
- `createImageBitmap(file)`
  - 作用：将图像源复制转化为 ImageBitmap ，以便在worker交互过程中可直接转移所有权，避免结构化克隆消耗
  - 参数：图像源支持类型包括`<img>`元素、`<canvas>`元素、`<video>`元素（当前帧）、Blob/File 对象（如用户上传的图片）、ImageData 对象、其他 ImageBitmap 对象
  - 注意： ImageBitmap 需要调用`.close()`显式关闭，否则可能内存泄漏
- `new Worker(new URL('../../utils/worker/filterWorker.js', import.meta.url)); `
  - 作用：根据worker文件生成对应的worker线程
  - 注意：worker文件的路径需是网站上的实际路径，因此需要结合`new URL`与`import.meta.url`指向
- `canvas.transferControlToOffscreen()`
  - 作用：主线程将canvas转化为离屏canvas后，将其控制权权转移给worker进行处理，同时避免结构化克隆消耗
  - 注意：转移控制权前主线程不得调用`canvas.getContext("2d")`；转移控制权后主线程不得再次对同一canvas调用该函数
- `new OffscreenCanvas(width, height)`：在worker中创建离屏canvas用于图像处理
- `offscreenCanvas.transferToImageBitmap`：直接转移​ OffscreenCanvas 的底层数据到 ImageBitmap ，转移后 OffscreenCanvas 清空，但相较于`createImageBitmap`性能更高

## 4.简单示例
```javascript
// worker.js（定义）
self.onmessage = function(e) {
  if (e.data.command === 'start') {
    // 执行耗时任务
    const result = heavyComputation(e.data.data);
    
    // 返回结果
    self.postMessage({ result: result });
  }
};

function heavyComputation(data) {
  // 这里执行耗时操作
  // ...
  return processedData;
}

// 主线程（调用）
const worker = new Worker('worker.js');

worker.postMessage({ command: 'start', data: someData });

worker.onmessage = function(e) {
  console.log('Worker said: ', e.data);
};

worker.onerror = function(e) {
  console.error('Worker error: ', e);
};
```

## 5.离屏canvas渲染
- 核心逻辑：​​​主线程通过 `transferControlToOffscreen()` 将可见的 `<canvas>` 控制权转移给 Worker；​Worker 直接操作离屏 Canvas，绘制结果自动同步到页面对应的 `<canvas>`
- ​优势​：零拷贝显示（直接由浏览器合成器 GPU 处理，性能最优），无需手动回传数据
- 适用情境：实时可视化（如动态图表、粒子效果）、游戏渲染（每一帧都需要更新屏幕）、任何需要低延迟显示的图形

```javascript
/* 主线程 */
const canvas = ref(null); // 绑定canvas元素
let workerDrawer = null
const handleWorkerDrawFilter = () => {
  let message = {
    type: 'drawFilter',
    filterType: 'sepia',
    algorithm: algorithm.value,
  }
  const transferArray = []

  if(!workerDrawer) {
    workerDrawer = new Worker(new URL('../../utils/worker/filterWorker.js', import.meta.url))
    const mainCanvas = canvas.value.transferControlToOffscreen()
    message.mainCanvas = mainCanvas
    transferArray.push(mainCanvas)
    // 统一处理消息
    workerDrawer.onmessage = (e) => {
      if (e.data.type === 'drawResult') {
        console.log('worker图片绘制完成')
      }
      if (e.data.type === 'drawFilterResult') {
        console.log('worker滤镜绘制完成')
      }
    };
  }

  workerDrawer.postMessage(message, transferArray)
}

/* Worker 线程 */
let mainCanvas = null // 存储传入的canvas用于后续操作

onmessage = async (e) => {
  const data = e.data
  if (data.type === 'drawFilter') {
    if(data.mainCanvas) mainCanvas = data.mainCanvas
    drawFilter(data.filterType, data.algorithm);
    postMessage(
      { type: 'drawFilterResult' },
    );
  }
};

const drawFilter = (filterType, algorithm) => {
  const ctx = mainCanvas.getContext("2d")
  const imageData = ctx.getImageData(0, 0, mainCanvas.width, mainCanvas.height);
  const data = imageData.data;
  if(algorithm == 'simple') simpleFilterCreate(data, filterType)
    else difficultFilterCreate(mainCanvas, data, filterType)
  ctx.putImageData(imageData, 0, 0)
}
```

## 6.处理canvas渲染数据
- 核心逻辑：Worker 内部创建独立的 OffscreenCanvas，处理图形数据（如滤镜、图像分析）；通过 transferToImageBitmap() 将结果转换为轻量级位图，​按需传回主线程
- 优势​：完全脱离主线程，适合计算密集型任务，灵活控制数据传输时机（非实时场景）
- 适用情境：图像处理（如压缩、滤镜）、物理模拟/数据计算生成中间结果、不需要实时显示的预处理任务

```javascript
/* 主线程 */
const canvas = ref(null); // 绑定canvas元素
let workerFilter = null;
const handleWorkerFilter = async () => {
  const file = fileList.value[0]?.originFileObj;
  const bitmap = await createImageBitmap(file)

  if(!workerFilter) {
    workerFilter = new Worker(new URL('../../utils/worker/filterWorker.js', import.meta.url));    
    // 统一处理消息
    workerFilter.onmessage = (e) => {
      if (e.data.type === 'result') {
        const bitmap = e.data.bitmap;
        const ctx = canvas.value.getContext('2d')
        ctx.drawImage(bitmap, 0, 0);
        bitmap.close()
        console.log('worker滤镜处理完成');
      }
    };
  }

  workerFilter.postMessage({
    type: 'process',
    bitmap: bitmap,
    filterType: 'sepia',
    maxWidth: maxWidth.value,
    algorithm: algorithm.value,
  }, [bitmap]);
};

/* Worker 线程 */
onmessage = async (e) => {
  const data = e.data
  if (data.type === 'process') {
    const bitmap = await applyFilter(data.bitmap, data.filterType, data.maxWidth, data.algorithm);
    postMessage(
      { type: 'result', bitmap },
      [bitmap]
    );
  }
};

// 处理滤镜
const applyFilter = async(bitmap, filterType, maxWidth, algorithm) => {
  const scaleFactor = maxWidth / bitmap.width;
  const scaledHeight = bitmap.height * scaleFactor;
  const offscreen = new OffscreenCanvas(maxWidth, scaledHeight)
  const ctx = offscreen.getContext("2d")
  ctx.drawImage(bitmap, 0, 0, maxWidth, scaledHeight);
  bitmap.close()
  const imageData = ctx.getImageData(0, 0, offscreen.width, offscreen.height);
  const data = imageData.data;
  if(algorithm == 'simple') simpleFilterCreate(data, filterType)
    else difficultFilterCreate(offscreen, data, filterType) 
  ctx.putImageData(imageData, 0, 0)
  return offscreen.transferToImageBitmap()
}
```