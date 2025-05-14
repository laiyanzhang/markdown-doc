# H5/Uni-App

标签（空格分隔）： 实践

---
## 1.uni.chooseLocation无可选地址
- 情境：点击选择地址时，地图中定位准确，但下方无可选地址，搜索也无效果
- 原因：地图key的白名单导致失效
- 解决：清空地图key的白名单


## 2.uni.chooseImage失效
- 原因：webview中不支持，需要由APP进行原生支持
- 方案：由APP原生支持返回base64编码，处理base64编码后进行OSS上传实现拍照/选照上传功能

### 1.相关方法
```javascript
// oss.js
import crypto from 'crypto-js';
import { Base64 } from 'js-base64'; // 计算签名

// 生成签名
function computeSignature(accessKeySecret, canonicalString) {
    return crypto.enc.Base64.stringify(crypto.HmacSHA1(canonicalString, accessKeySecret));
}

// 构造OSS相关参数
export function getFormDataParams({ accesskeyid, accesskeysecret, securitytoken }) {
    if (accesskeyid && accesskeysecret && securitytoken) {
        const date = new Date();
        date.setHours(date.getHours() + 1);
        const policyText = {
            expiration: date.toISOString(),
            // 设置policy过期时间。
            conditions: [
                // 限制上传大小。
                ['content-length-range', 0, 1024 * 1024 * 1024]
            ]
        };
        const policy = Base64.encode(JSON.stringify(policyText)); // policy必须为base64的string。
        const signature = computeSignature(accesskeysecret, policy);
        const host= 'https://display-image.oss-cn-hangzhou.aliyuncs.com/'
        const formData = {
            OSSAccessKeyId: accesskeyid,
            signature,
            policy,
            'x-oss-security-token': securitytoken,
            host
        };
        return formData;
    }

    return null;
}

// base64编码转文件
export function base64ToFile(base64, filename) {
    if (!base64) {
        return null
    }
    const arr = base64.split(',')
    if (!arr.length || !arr[0]) {
        return null
    }
    // .match(/:(.*?);/)[1]
    const mimeMatcher = (arr[0]).match(/:(.*?);/)
    if (!mimeMatcher) {
        return null
    }
    const mime = mimeMatcher[1]
    const bstr = atob(arr[1])
    let n = bstr.length
    const u8arr = new Uint8Array(n)
    while (n--) {
        u8arr[n] = bstr.charCodeAt(n)
    }
    return new File([u8arr], filename, { type: mime })
}

// 获取文件名
export function getExtName(filePath) {
    return filePath.slice(filePath.lastIndexOf('.'))
}
```


### 2.触发拍照/选照
```javascript
async uploadCoverImage(evt) {
    // #ifdef H5
    this.setData({
        callback: async(resp) => {
            // resp为处理后返回数据，设置回调可用于处理上传成功后的文件
        }
    })
    this.onShowPhoto(evt)
    // #endif
},

onShowPhoto(evt) {
    this.setData({
        photoShow: true, // 控制动作列表显隐
        photoEvent: evt
    })
},

onSelectPhoto(event) {
    let type = "1"
    if(event.detail.name == '拍摄') type = "0"
    this.getImage(type)
},
```

### 3.利用原生APP支持的API获取图片的base64编码
```javascript
getImage(type) {
    let _this = this
    let config = {
        // API对应参数
    }
    window.xtion.getPhoto(config, function(res, error){
        if(error != null) {
            uni.showToast({
                title: '无法上传',
                icon: 'error'
            });
        }
        // 获取图片数据
        if(res.length === 0) {
            uni.showToast({
                title: '未选择照片',
                icon: 'error'
            });
        }
        else {
            _this.uploadImage(res[0])
        }
    })
},
```

### 4.处理base64编码返回图片
```javascript
uploadImage(evt, cb) {
    // #ifdef H5
    uni.showLoading({
        title: '上传中...'
    });
    this.handleSelectFilesChange({
        file: base64ToFile(evt.file, getExtName(evt.filePath)),
        base64: evt.file
    })
    // #endif
}

 handleSelectFilesChange(e) {
    const selectedFile = e.file
    const base64 = e.base64
    if (!selectedFile) return
    this.uploadFile(selectedFile)
        .then((res) => {
            this.callback(res) // 上传成功触发回调的后续处理
        })
        .catch(error => {
            this.callback({
                status: 'fail',
                filePath: base64, // 图片base64编码
                imgUrl: '' // 图片远程路径
            })
        })
},
```

### 5.OSS上传图片
```javascript
// 获取OSS上传相关参数
async getUploadConfig() {
    const resp = await getOssParams();
    console.log('upload params:', resp);

    if (resp?.resp_data) {
        const config = getFormDataParams(resp?.resp_data);

        if (config) {
            this.setData({
                uploadConfig: config
            });
        }
    }
},

async uploadFile(file) {
    const uid = guid()
    const dateTime = new Date()
    const url = uid
    const tenantCode = uni.getStorageSync('tenantcode') || '1101190'
    const objectKey = `${url.substr(0, 3)}/img/${dateTime.getFullYear()}${dateTime.getMonth() + 1}${dateTime.getDate()}/${tenantCode}/${url}.jpg`
    const config = this.uploadConfig

    // 构建上传formData
    let formData = new FormData()
    formData.append('key',objectKey)
    formData.append("policy",config.policy)
    formData.append("OSSAccessKeyId",config.OSSAccessKeyId)
    formData.append('x-oss-security-token',config['x-oss-security-token'])
    formData.append("signature",config.signature)
    formData.append("file",file)

    // 开始上传
    try {
        let res = await http({
            method:'post',
            url: config.host,
            headers: { 'Content-Type': 'multipart/form-data' },
            data:formData
        })
        console.log('成功提交:', res)
        const imgUrl = `${config.host}${objectKey}`;
        return {
            status: 'success',
            filePath: imgUrl,
            imgUrl,
            file
        }
    } catch (e) {
        console.log('error: ', e)
        throw Error(e)
    }
},
```


## 3.picker-view无法滚动
- 情境：表单页面分为只读状态与编辑状态，当从编辑状态转回只读状态时重置picker-view的激活项；后续再返回编辑状态时，表现为picker-view始终固定在首项，滚动后也回弹至首项
- 原因：手动设置picker-view的激活项就会导致该问题，深层原因不明，可能是潜在bug
- 方案：每次需要手动设置picker-view的激活项前都将可选数组清空并取消渲染该组件，使用setTimeout再将原来的可选数组恢复并重新渲染，营造的效果为每次修改时都重新渲染组件，以此规避无法滚动的问题

### 1.picker-view组件
```javascript
<picker-view
    :value="[channelType.activeIndex]"
    @change="pickerViewChange"
    data-field="channelType"
>
    <picker-view-column v-if="channelType.data.length > 0">
        <view v-for="(item, index) in channelType.data" :key="item.value">{{ item.text }}</view>
    </picker-view-column>
</picker-view>
```

### 2.退出编辑状态
```javascript
async cancelEdit() {
    const { store, channelType } = this;
    let chanelIndex = 0
    let channelData = [].concat(channelType.data)

    if (store.channelType) {
        // 清空数据使组件不渲染
        chanelIndex = channelType.data.findIndex((t) => t.value === store.channelType);
        channelType.data = []

        if (chanelIndex > -1) {
            // 使用setTimeout在下个宏任务中再将组件渲染
            setTimeout(()=> {
                this.setData({
                    'channelType.data': channelData,
                    'channelType.activeIndex': chanelIndex,
                    'channelType.activeText': channelData[chanelIndex].text
                });
            }, 50)
        }
    } else {
        // 该表单项无值时也需使组件重新渲染一遍
        channelType.data = []
        setTimeout(() => {
            this.setData({
                'channelType.data': channelData,
                'channelType.activeIndex': 0,
                'channelType.activeText': ''
            });
        })
    }


},
```

## 4.uni.openLocation使用问题
- 目的：打开目标的地图位置并进行导航，导航完成后退出回到原界面
- 问题：
  - 仅能传入目标地点，还需点击定位后再点击导航才能开始导航
  - 直接点击导航只有默认起点进行选择，并不能根据当前位置返回可选地点
  - 导航完成点击返回时，在IOS手机会出现返回无效的情况
- 解决：不使用该API，修改当前路径为地图页面路径直接进行导航
```javascript
openLocation(e) {
    const { latitude, longitude, address } = e.currentTarget.dataset;

    if (latitude && longitude) {
        // #ifdef H5
        let url = `https://apis.map.qq.com/tools/routeplan/eword=${address}&epointx=${latitude}&epointy=${longitude}?referer=${应用名称}&key=${腾讯地图的key}`
        window.location.href = url
        // #endif
        // #ifdef MP-WEIXIN
        uni.openLocation({
            latitude: Number(latitude),
            longitude: Number(longitude),
            name: address || ''
        });
        // #endif
    }
},
```


## 5.IOS兼容处理
- IOS中popup弹窗显示层级问题：将popup组件放到外层组件中，避免放到内层组件中
- IOS中页面高度计算：IOS底部有安全距离，设置样式时要考虑安全距离`height: calc(100vh - 50px - env(safe-area-inset-bottom));`
- IOS禁止下拉滑动页面：
```javascript
mounted() {
    let element = document.getElementById('home')
    element.addEventListener('touchmove', function (e) {
        e.preventDefault(); //阻止默认的处理方式(阻止下拉滑动的效果)
    }, {passive: false}); //passive 参数不能省略，用来兼容ios和android
},
```


## 6.Android返回上级页面
- 情境：Android从页面跳转链接进入地图网页进行导航后返回原来页面，原来页面触发重新加载并刷新状态；希望能保留状态不刷新
- 原因：Android不会从缓存中获取页面，IOS则从缓存中获取页面
- 解决：无法控制Android刷新页面，仅能通过存储页面信息进行页面还原


### 1.跳转导航时存储信息
```javascript
openLocation(e) {
    const { latitude, longitude, address } = e.currentTarget.dataset;

    if (latitude && longitude) {
        // #ifdef H5
        uni.setStorageSync('activeTab', this.activeTab)
        let url = `https://apis.map.qq.com/tools/routeplan/eword=${address}&epointx=${latitude}&epointy=${longitude}?referer=${应用名称}&key=${腾讯地图的key}`
        window.location.href = url
        // #endif
        // #ifdef MP-WEIXIN
        uni.openLocation({
            latitude: Number(latitude),
            longitude: Number(longitude),
            name: address || ''
        });
        // #endif
    }
},
```


### 2.重载时还原页面
```javascript
// 从地图导航中返回上级页面与重新进入该应用的情况是高度相似的，该应用进入过地图导航的情况下路由历史history的长度会增加，通过该值进行判断是否需要还原页面
created() {
	console.log('new store created');
    // #ifdef H5
    let tab = uni.getStorageSync('activeTab')
    if(history.length == 2 && tab !== undefined) {
        this.setData({
            activeTab: tab
        });
        uni.setStorageSync('activeTab', undefined)
    }
    // #endif
}
```



