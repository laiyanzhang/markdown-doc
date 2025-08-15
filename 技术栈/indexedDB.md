# indexedDB

## 1.基础概念
- 常用场景：大量数据需要缓存在本地
- 重要概念
  - 仓库objectStore：类似于数据库中的表，数据存储媒介
  - 索引index：索引作为数据的标志量，可根据索引获取对应值
  - 游标cursor：数据的遍历工具


## 2.创建数据库
```javascript
/**
 * 打开数据库
 * @param {string} dbName 数据库的名字
 * @param {string} storeName 仓库名称
 * @param {string} version 数据库的版本
 * @return {object} 返回一个数据库实例
 */
function openDB(dbName, version = 1) {
    return new Promise((resolve, reject) => {
        var indexedDB =
            window.indexedDB ||
            window.mozIndexedDB ||
            window.webkitIndexedDB ||
            window.msIndexedDB;
        const request = indexedDB.open(dbName, version);

        // 数据库打开成功回调
        request.onsuccess = function (event) {
            let db = event.target.result;
            console.log("数据库打开成功");
            resolve(db);
        };

        // 数据库打开失败的回调
        request.onerror = function () {
            console.log("数据库打开报错");
            reject()
        };

        // 数据库创建或版本更新时触发
        request.onupgradeneeded = function (event) {
            console.log("onupgradeneeded");
            let db = event.target.result;
            // 创建仓库
            let objectStore = db.createObjectStore("store", {
                keyPath: "id", // 这是主键
                // autoIncrement: true // 实现自增
            });
            // 创建索引，在后面查询数据的时候可以根据索引查
            objectStore.createIndex("id", "id", {
                unique: false
            });
        };
    });
}

```

## 3.关闭数据库
```javascript
/**
 * 关闭数据库
 * @param {object} db 数据库实例
 */
function closeDB(db) {
    db.close();
    console.log("数据库已关闭");
}
```


## 4.删除数据库
```javascript
/**
 * 删除数据库
 * @param {object} dbName 数据库名称
 */
function deleteDB(dbName) {
    let deleteRequest = window.indexedDB.deleteDatabase(dbName);
    deleteRequest.onerror = function () {
        console.log("删除失败");
    };
    deleteRequest.onsuccess = function () {
        console.log("删除成功");
    };
}
```


## 5.新增数据
```javascript
/**
 * 新增数据
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {object} data 数据（键值对格式，必须包含主键与索引字段）
 */
function addData(db, storeName, data) {
    // 创建事务对象，指定表格名称和操作模式（"只读"或"读写"）
    let transaction = db.transaction([storeName], "readwrite")
    // 获取仓库对象
    let store = transaction.objectStore(storeName)
    // 执行插入操作
    let request = store.add(data)

    request.onsuccess = function () {
        console.log("数据写入成功");
    };

    request.onerror = function () {
        console.log("数据写入失败");
    };
}

```


## 6.更新数据
```javascript
/**
 * 更新数据
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {object} data 数据
 */
function updateData(db, storeName, data) {
    let transaction = db.transaction([storeName], "readwrite")
    let store = transaction.objectStore(storeName)
    let request = store.put(data); // 如果没有该数据则为新增

    request.onsuccess = function () {
        console.log("数据更新成功");
    };

    request.onerror = function () {
        console.log("数据更新失败");
    };
}
```


## 7.根据主键删除数据
```javascript
/**
 * 通过主键删除数据
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {object} id 主键值
 */
function delData(db, storeName, id) {
    let transaction = db.transaction([storeName], "readwrite")
    let store = transaction.objectStore(storeName)
    let request = store.delete(id)

    request.onsuccess = function () {
        console.log("数据删除成功");
    };

    request.onerror = function () {
        console.log("数据删除失败");
    };
}
```


## 8.根据索引值删除数据
```javascript
/**
 * 通过索引和游标删除指定的数据
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {string} indexName 索引名
 * @param {object} indexValue 索引值
 */
function cursorDelete(db, storeName, indexName, indexValue) {
    let transaction = db.transaction(storeName, "readwrite")
    let store = transaction.objectStore(storeName)
    let request = store.index(indexName).openCursor(IDBKeyRange.only(indexValue));
    request.onsuccess = function (e) {
        let cursor = e.target.result;
        let deleteRequest;
        if (cursor) {
            deleteRequest = cursor.delete(); // 请求删除当前项
            deleteRequest.onerror = function () {
                console.log("游标删除该记录失败");
            };
            deleteRequest.onsuccess = function () {
                console.log("游标删除该记录成功");
            };
            cursor.continue();
        }
    };
    request.onerror = function () {
        console.log('事务失败')
    };
}
```


## 9.通过游标获取全量数据
```javascript
/**
 * 通过游标获取全量数据
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 */
function getAllData(db, storeName) {
    return new Promise((resolve, reject) => {
        let list = []
        let transaction = db.transaction(storeName, "readwrite")
        let store = transaction.objectStore(storeName)
        let request = store.openCursor()

        // 游标开启成功，逐行读数据
        request.onsuccess = function (e) {
            let cursor = e.target.result
            // 必须要检查
            if (cursor) {
                list.push(cursor.value);
                cursor.continue(); // 遍历存储对象中的所有内容
            } else {
                console.log("游标读取的数据：", list);
                resolve(list)
            }
        };

        request.onerror = function () {
            console.log("事务失败");
            reject()
        };
    })
}

```


## 10.通过主键获取数据
```javascript
/**
 * 通过主键获取数据
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {string} key 主键值（创建时声明的keypath字段）
 */
function getDataByKey(db, storeName, key) {
    return new Promise((resolve, reject) => {
        var transaction = db.transaction([storeName]);
        var store = transaction.objectStore(storeName);
        var request = store.get(key);
    
        request.onsuccess = function () {
            // 仅查询一条数据
            console.log("主键查询结果: ", request.result);
            resolve(request.result);
        };
        
        request.onerror = function () {
            console.log("事务失败");
            reject()
        };
    });
}

```


## 11.通过索引获取数据
```javascript
/**
 * 通过索引获取数据
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {string} indexName 索引名称
 * @param {string} indexValue 索引值
 */
function getDataByIndex(db, storeName, indexName, indexValue) {
    return new Promise((resolve, reject) => {
        let transaction = db.transaction(storeName, "readwrite")
        let store = transaction.objectStore(storeName)
        let request = store.index(indexName).get(indexValue)

        request.onsuccess = function (e) {
            // 所有符合索引值的数据
            let result = e.target.result
            console.log("索引查询结果：", result)
            resolve(result)
        };

        request.onerror = function () {
            console.log("事务失败")
            reject()
        };
    })
}

```


## 12.通过索引与游标结合筛选数据
```javascript
/**
 * 通过索引和游标查询记录
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {string} indexName 索引名称
 * @param {string} indexValue 索引值
 */
function cursorGetDataByIndex(db, storeName, indexName, indexValue) {
    return new Promise((resolve, reject) => {
        let list = []
        let transaction = db.transaction(storeName, "readwrite")
        let store = transaction.objectStore(storeName);
        let request = store.index(indexName).openCursor(IDBKeyRange.only(indexValue))

        request.onsuccess = function (e) {
            let cursor = e.target.result
            if (cursor) {
                // 区别于根据索引获取数据，此处可引入筛选相关逻辑
                list.push(cursor.value);
                cursor.continue();
            } else {
                console.log("游标索引查询结果：", list);
                resolve(list)
            }
        };

        request.onerror = function () {
            console.log("事务失败")
            reject()
        };
    })
}

```


## 13.通过索引和游标分页查询
```javascript
/**
 * 通过索引和游标分页查询记录
 * @param {object} db 数据库实例
 * @param {string} storeName 仓库名称
 * @param {string} indexName 索引名称
 * @param {string} indexValue 索引值
 * @param {number} page 页码
 * @param {number} pageSize 查询条数
 */
function cursorGetDataByIndexAndPage(
    db,
    storeName,
    indexName,
    indexValue,
    page,
    pageSize
) {
    return new Promise((resolve, reject) => {
        let list = [];
        let counter = 0; // 计数器
        let advanced = true; // 是否跳过多少条查询
        let transaction = db.transaction(storeName, "readwrite")
        let store = transaction.objectStore(storeName)
        var request = store.index(indexName).openCursor(IDBKeyRange.only(indexValue))

        request.onsuccess = function (e) {
            var cursor = e.target.result;
            if (page > 1 && advanced) {
                advanced = false;
                cursor.advance((page - 1) * pageSize); // 跳过多少条
                return;
            }
            if (cursor) {
                list.push(cursor.value);
                counter++;
                if (counter < pageSize) cursor.continue()
                else {
                    cursor = null;
                    console.log("分页查询结果", list);
                }
            } else {
                console.log("分页查询结果", list);
                resolve(list)
            }
        };

        request.onerror = function () {
            console.log("事务失败");
            reject()
        };
    })
}

```