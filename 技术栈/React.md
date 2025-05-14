# React

标签（空格分隔）： 技术栈

---

## 1.基础概念
```javascript
//  组件MyButton
function MyButton() {
  return (
    <button>
      我是一个按钮
    </button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>欢迎来到我的应用</h1>
      <MyButton />
    </div>
  );
}
```

- 组件命名：React组件必须大写字母开头
- 组件纯粹：
  - 只负责自己的任务。它不会更改在该函数调用前就已存在的对象或变量
  - 输入相同，则输出相同。给定相同的输入，纯函数应总是返回相同的结果
- 样式添加：`<img className="avatar" />`
- JSX：React使用JSX语法，相比起HTML更严格，必须闭合标签，并且不能返回多个JSX标签
- Hook：以use开头的函数被称为Hook，useState是React内置Hook。所有Hook只能在组件或其他Hook的顶层调用
- 条件渲染：`<div> {isLoggedIn ? (<AdminPanel />) : (<LoginForm />)}</div>`
- 显示数据

```javascript
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```
- 响应事件

```javascript
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      点我
    </button>
  );
}
```

## 2.列表渲染
- key属性：创建一个元素时，必须包括一个独一无二的key属性使用
- key值：如果不显式指定则默认key为index，index作为key值时，当顺序变化时diff算法性能变差
- key调用：key不会作为props属性传入组件内部，可以用其他属性存储key相同值进行调用

```javascript
const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

## 3.事件响应
- 事件绑定

```javascript
<button onClick={handleClick}>
<button onClick={() => alert('...')}>
```
- 事件冒泡：React中所有事件都会从子组件传播到父组件，除了`onScroll`
- 阻止冒泡：事件函数中执行`e.stopPropagation()`
- 捕获阶段：父组件绑定`onClickCapture`统一处理捕获事件
- 禁止默认事件：事件函数中执行`e.preventDefault()`


## 4.渲染
- 步骤一：**触发**一次渲染
  - 组件初次渲染
  - 组件（或者其祖先之一）的状态发生改变
- 步骤二：**渲染**组件
  - 初次渲染时，React调用根组件，递归渲染内部嵌套的所有组件
  - 后续的渲染，React 会调用内部状态更新触发了渲染的函数组件
- 步骤三：**提交**到 DOM 上
  - 对于初次渲染，React 会使用 appendChild() DOM API 将其创建的所有 DOM 节点放在屏幕上
  - 对于重渲染，React 将应用最少的必要操作（在渲染时计算！），以使得 DOM 与最新的渲染输出相互匹配，只更新变化元素


## 5.props
- 单向数据传递：state与事件同时作为prop传入子组件内
- 状态提升：向上移动state，实现组件中共享


### 1.组件间共享数据

```javascript
import { useState } from 'react';

export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>共同更新的计数器</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}

function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      点了 {count} 次
    </button>
  );
}
```

### 2.props解构

```javascript
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}
=>
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```

### 3.插槽式子组件

```javascript
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}

```

### 4.深层次传递Context
- 传递步骤
  - 定义需要传递的context
  - context作为组件包裹需要该context的组件进行传递
  - context内部无论多深层次的子组件均能通过useContext获取context
- tip：v19.0之前需要`LevelContext.Provider`的固定搭配，v19.0之后只需要`LevelContext`

```javascript
// App.js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading>主标题</Heading>
      <Section>
        <Heading>副标题</Heading>
        <Heading>副标题</Heading>
        <Heading>副标题</Heading>
        <Section>
          <Heading>子标题</Heading>
          <Heading>子标题</Heading>
          <Heading>子标题</Heading>
          <Section>
            <Heading>子子标题</Heading>
            <Heading>子子标题</Heading>
            <Heading>子子标题</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}


// Section.js
// LeveleContext内部组件可以通过useContext获取传递的参数
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext value={level + 1}>
        {children}
      </LevelContext>
    </section>
  );
}


// Heading.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('未知的 level：' + level);
  }
}

// LevelContext.js
import { createContext } from 'react';

export const LevelContext = createContext(1);
```

### 5.Context与Reducer结合
- 结合步骤
  - 定义需要传递信息的context组件
  - 自定义传递组件返回context组件，context组件指定value为传递的reducer值
  - 传递组件内部包裹的组件通过useContext获取传递的reducer

```javascript
// App.js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}

// TasksContext.js
import { createContext, useContext, useReducer } from 'react';

const TasksContext = createContext(null);

const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];

//  AddTasks.js
import { useState } from 'react';
import { useTasksDispatch } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useTasksDispatch();
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        }); 
      }}>Add</button>
    </>
  );
}

let nextId = 3;

// TaskList.js
import { useState } from 'react';
import { useTasks, useTasksDispatch } from './TasksContext.js';

export default function TaskList() {
  const tasks = useTasks();
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useTasksDispatch();
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
        Delete
      </button>
    </label>
  );
}

```



## 6.state

### 1.基础概念
```javascript
function MyButton() {
  
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```
- 局部变量：无法在多次渲染中持久保存，更改局部变量不会触发渲染
- state变量：State 变量 用于保存渲染间的数据，State setter 函数 更新变量并触发 React 再次渲染组件
- 数据读写：useState中获得当前的state（count）以及用于更新它的函数（setCount）
- 数据命名：一般命名为 [something, setSomething] 
- 数据独立：同时渲染多个相同组件时，每个组件各自维护独立state
- 初始化：state变量仅在第一次渲染时初始化
- 受控组件与非受控组件：由props控制为受控组件，由state控制为非受控组件

### 2.快照
state变量的值永远不会在一次渲染的内部发生变化，仅在下一次渲染可获取变化后的state变量

```javascript
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        // number作为快照，将三次相同操作函数放入更新队列中，下次渲染时值为1
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
        // 三次函数陆续加入更新队列中，下次渲染时值为3
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        // 交替放入更新队列，先执行替换再执行更新，下次渲染时为6
        setNumber(number + 5);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  )
}
```



### 3.state原则
- 合并关联的 state：如果你总是同时更新两个或更多的 state 变量，请考虑将它们合并为一个单独的 state 变量。
- 避免互相矛盾的 state：当 state 结构中存在多个相互矛盾或“不一致”的 state 时，你就可能为此会留下隐患。应尽量避免这种情况。
- 避免冗余的 state：如果你能在渲染期间从组件的 props 或其现有的 state 变量中计算出一些信息，则不应将这些信息放入该组件的 state 中。
- 避免重复的 state：当同一数据在多个 state 变量之间或在多个嵌套对象中重复时，这会很难保持它们同步。应尽可能减少重复。
- 避免深度嵌套的 state：深度分层的 state 更新起来不是很方便。如果可能的话，最好以扁平化方式构建 state。
- 不要用state镜像props：镜像props时，props发生改变时镜像的state变量并不会更新

### 4.保留与重置
- UI树：组件仍在UI树中则state状态保留；从UI树中移除，则state状态被重置
- 子组件：当在相同位置渲染不同组件，组件的整个子树都被重置
- 相同位置重置state：组件绑定唯一的key，切换组件时重置state
- 为被移除的组件保留state：状态提升至父组件保存/CSS隐藏/localStorage保存


## 7.mutation

### 1.基本概念
- 定义：修改state的方式
- React不推荐直接修改state的原因
  - 调试：使用`console.log`时能对比两次渲染中state值的变化
  - 优化：对比两次渲染中的props或者state的值是否发生变化确定是否触发重新渲染
  - 新功能：React新功能依赖于state作为快照保存
  - 需求变更：在实现如撤销/恢复、展示修改历史时容易实现
  - 更简单的实现：不需要如‘响应式’方案劫持对象属性，因此允许把任何对象放入state

### 2.更新对象

```javascript
const [position, setPosition] = useState({
    x: 0,
    y: 0
});

// 直接修改对象导致React不知道对象更新
const handlePoint = (e) => {
    position.x = e.clientX;
    position.y = e.clientY;
}

// 调用setPosition使用新对象替换position
const handlePoint = (e) => {
    setPosition({
        x: e.clientX,
        y: e.clientY
    });
}

// 对象展开浅拷贝
const handlePoint = (e) => {
    setPosition({
        ...position,
        x: e.clientX
    });
}

// 使用Immer第三方库支持简洁语法，不需要拷贝原对象单独修改某个属性
// npm install use-immer
// 用 import { useImmer } from 'use-immer' 替换掉 import { useState } from 'react'
const [position, setPosition] = useImmer({
    x: 0,
    y: 0
});
const handlePoint = (e) => {
    setPosition(draft => {
        draft.x = e.clientX
    });
}
```

### 3.更新数组
- 修改数组类型state：使用返回新一个数组的API，避免实现修改原数组的API
```javascript
// 返回一个修改后的新数组
setArtists(
  artists.filter(a => a.id !== artist.id)
);

// 使用Immer
updateMyTodos(draft => {
  const artwork = draft.find(a => a.id === artworkId);
  artwork.seen = nextSeen;
});
```

## 8.action
- 定义：dispatch分发的方式执行mutation
- 背景：越来越多的事件处理程序对状态进行编辑
- 对比useState和useReducer
  - 代码体积小：useReducer应对大量事件处理程序时减少代码量
  - 分离关注点：useReducer分离状态更新逻辑与事件处理程序，可读性强
  - 可调试性高：useReducer在reducer函数中统一打印日志观察各状态更新
  - 可测试性强：不依赖于组件的纯函数，可单独测试
- 完整示例

```javascript
// APP.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import tasksReducer from './tasksReducer.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>布拉格的行程安排</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: '参观卡夫卡博物馆', done: true},
  {id: 1, text: '看木偶戏', done: false},
  {id: 2, text: '打卡列侬墙', done: false},
];


// tasksReducer.js
export default function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action：' + action.type);
    }
  }
}

```

- 使用Immer简化

```javascript
import { useImmerReducer } from 'use-immer';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

function tasksReducer(draft, action) {
  switch (action.type) {
    case 'added': {
      draft.push({
        id: action.id,
        text: action.text,
        done: false,
      });
      break;
    }
    case 'changed': {
      const index = draft.findIndex((t) => t.id === action.task.id);
      draft[index] = action.task;
      break;
    }
    case 'deleted': {
      return draft.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('未知 action：' + action.type);
    }
  }
}

export default function TaskApp() {
  const [tasks, dispatch] = useImmerReducer(tasksReducer, initialTasks);

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>布拉格的行程安排</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: '参观卡夫卡博物馆', done: true},
  {id: 1, text: '看木偶戏', done: false},
  {id: 2, text: '打卡列侬墙', done: false},
];

```


## 9.ref

### 1.ref引用值
- 特性
  - 在渲染过程之外修改和更新值，如事件处理程序
  - 值改动不会触发重新渲染
  - 相比起局部变量，ref存储的变量每次渲染都会保存，而局部变量每次渲染会重置
- 常用情境
  - 存储timeout ID
  - 存储和操作DOM元素
  - 存储不需要被用来计算 JSX 的其他对象
- 存储timeout ID

```javascript
import { useState, useRef } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>时间过去了： {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        开始
      </button>
      <button onClick={handleStop}>
        停止
      </button>
    </>
  );
}
```

### 2.存储DOM元素
- 单个DOM元素设置状态

```javascript
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        聚焦输入框
      </button>
    </>
  );
}

```

- 管理ref列表

```javascript
import { useRef, useState } from "react";

export default function CatFriends() {
  const itemsRef = useRef(null);
  const [catList, setCatList] = useState(setupCatList);

  function scrollToCat(cat) {
    const map = getMap();
    const node = map.get(cat);
    node.scrollIntoView({
      behavior: "smooth",
      block: "nearest",
      inline: "center",
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // 首次运行时初始化 Map。
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToCat(catList[0])}>Neo</button>
        <button onClick={() => scrollToCat(catList[5])}>Millie</button>
        <button onClick={() => scrollToCat(catList[9])}>Bella</button>
      </nav>
      <div>
        <ul>
          {catList.map((cat) => (
            <li
              key={cat}
              ref={(node) => {
                const map = getMap();
                map.set(cat, node);
                
                // 清除DOM时在ref中删除对应的node
                return () => {
                  map.delete(cat);
                };
              }}
            >
              <img src={cat} />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

function setupCatList() {
  const catList = [];
  for (let i = 0; i < 10; i++) {
    catList.push("https://loremflickr.com/320/240/cat?lock=" + i);
  }

  return catList;
}


```

- 访问子组件的DOM节点

```javascript
// 通过prop的方式传递ref，子组件DOM节点绑定传递过来的ref
import { useRef, useImperativeHandle } from "react";

function MyInput({ ref }) {
  const realInputRef = useRef(null);
  // useImperativeHandle可以控制暴露的调用方法
  useImperativeHandle(ref, () => ({
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input ref={realInputRef} />;
};

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>聚焦输入框</button>
    </>
  );
}

```

- 强制同步更新后获取最新的DOM节点

```javascript
import { useState, useRef } from 'react';
import { flushSync } from 'react-dom';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    // 使用flushSync强制同步根据最新state更新DOM节点
    flushSync(() => {
      setText('');
      setTodos([ ...todos, newTodo]);
    });
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        添加
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: '待办 #' + (i + 1)
  });
}

```

- 避免更改由 React 管理的 DOM 节点

```javascript
// 删除DOM元素后尝试使用 setState 再次显示它会导致崩溃
import { useState, useRef } from 'react';

export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {
          setShow(!show);
        }}>
        通过 setState 切换
      </button>
      <button
        onClick={() => {
          ref.current.remove();
        }}>
        从 DOM 中删除
      </button>
      {show && <p ref={ref}>Hello world</p>}
    </div>
  );
}

```



## 10.Effect
- 特性：相比渲染不是纯粹的计算过程，相比事件处理没有专门的事件去触发
- 使用情境：暂时“跳出” React并与一些外部系统进行同步。这包括浏览器 API、第三方小部件，以及网络等等
- 调用时机：渲染结束后执行
- 开发环境&生产环境：开发环境下严格模式会故意卸载并重新挂载组件，以帮助开发者发现副作用问题；而生产环境不会
- 不适用
  - 初始化应用
  - 与事件处理相关
  - 根据state/props更新值


### 1.依赖项
无依赖项时每次渲染都会执行；依赖项为空时仅在首次挂载时执行；依赖项有值时值改变时执行

```javascript
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('调用 video.play()');
      ref.current.play();
    } else {
      console.log('调用 video.pause()');
      ref.current.pause();
    }
  }, [isPlaying]);

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? '暂停' : '播放'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}

```

### 2.清理函数
卸载时按需对内部变量进行清理，常用于移除监听、关闭弹窗、触发动画

```javascript
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      // 不能读取message，只需要在setMessages中传递更新函数即可
      setMessages(msgs => [...msgs, receivedMessage]);
    });
    // 设置清理函数断开连接
    return () => connection.disconnect();
  }, []);
  return <h1>欢迎来到聊天室！</h1>;
}
```

### 3.获取数据
- 在effect中直接编写fetch存在弊端
  - Effect 不会在服务端运行
  - 网络瀑布：父组件与子组件依次请求，无法并行调用接口
  - 无法预加载或缓存数据：组件卸载后再挂载需要重新获取
  - 不够简洁

```javascript
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  // 当组件卸载但接口仍未返回数据时，设置清理函数忽略请求返回
  return () => {
    ignore = true;
  };
}, [userId]);
```


## 11.react-query

### 1.核心作用
- 自动化数据缓存：自动缓存已获取的数据，避免重复请求

```javascript
const { data } = useQuery('todos', fetchTodos);
// 在其他组件中复用相同的 key 'todos'，直接读取缓存
const { data: sameData } = useQuery('todos', fetchTodos);
```
- 自动后台数据刷新：在数据过期时自动重新请求，保持数据最新

```javascript
useQuery('todos', fetchTodos, {
  staleTime: 5000, // 数据5秒后过期
  refetchOnWindowFocus: true, // 窗口聚焦时刷新
});
```
- 请求状态管理：自动跟踪请求的 loading、error、success 状态，减少样板代码

```javascript
useQuery('todos', fetchTodos, {
  staleTime: 5000, // 数据5秒后过期
  refetchOnWindowFocus: true, // 窗口聚焦时刷新
});
```
- 数据依赖与分页：支持基于上一页数据的分页查询，或根据参数动态获取数据

```javascript
const { data } = useQuery(['todos', page], () => fetchTodos(page));
// 当 page 变化时，自动触发新请求
```
- 错误重试与优化：默认自动重试失败的请求（可配置次数和策略）

```javascript
useQuery('todos', fetchTodos, {
  retry: 3, // 失败后重试3次
  retryDelay: 1000, // 每次重试间隔1秒
});
```
- 数据预加载与乐观更新：在用户需要数据前提前加载（如悬停按钮时）/ 先假设请求成功更新 UI，失败后回滚

```javascript
const queryClient = useQueryClient();

const mutation = useMutation(updateTodo, {
  onMutate: async (newTodo) => {
    // 取消当前查询，避免覆盖
    await queryClient.cancelQueries('todos');
    // 保存旧数据以便回滚
    const previousTodos = queryClient.getQueryData('todos');
    // 乐观更新
    queryClient.setQueryData('todos', old => [...old, newTodo]);
    return { previousTodos };
  },
  onError: (err, newTodo, context) => {
    // 回滚到旧数据
    queryClient.setQueryData('todos', context.previousTodos);
  },
});
```

### 2.核心API

- useQuery：用于获取数据

```javascript
const { data, isLoading, isError, error } = useQuery(
  'uniqueKey',      // 唯一标识缓存的 key
  fetchDataFunction, // 数据获取函数（如 fetch、axios）
  options          // 配置项（缓存时间、重试策略等）
);
```

- useMutation：用于修改数据（POST/PUT/DELETE）

```javascript
const mutation = useMutation(postData, {
  onSuccess: () => {
    // 数据提交成功后，刷新缓存
    queryClient.invalidateQueries('todos');
  },
});

// 触发提交
mutation.mutate(newData);
```

- QueryClient：全局缓存管理

```javascript
import { QueryClient, QueryClientProvider } from 'react-query';

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <YourComponent />
    </QueryClientProvider>
  );
}
```

### 3.完整示例

```javascript
import { useQuery, useMutation, QueryClient, QueryClientProvider } from 'react-query';

const queryClient = new QueryClient();

function TodoList() {
  const [page, setPage] = useState(1);

  // 获取数据
  const { data, isLoading, isError } = useQuery(
    ['todos', page], 
    () => fetch(`/api/todos?page=${page}`).then(res => res.json()),
    { 
      keepPreviousData: true, // 保留旧数据直到新数据加载完成
      staleTime: 5000,
    }
  );

  // 提交新待办事项
  const mutation = useMutation(
    newTodo => fetch('/api/todos', { 
      method: 'POST',
      body: JSON.stringify(newTodo),
    }),
    {
      onSuccess: () => {
        // 提交成功后，刷新缓存
        queryClient.invalidateQueries('todos');
      },
    }
  );

  if (isLoading) return <div>Loading...</div>;
  if (isError) return <div>Error loading data!</div>;

  return (
    <div>
      <ul>
        {data.todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
      <button onClick={() => setPage(p => p - 1)}>Previous Page</button>
      <button onClick={() => setPage(p => p + 1)}>Next Page</button>
      <button 
        onClick={() => mutation.mutate({ text: 'New Todo' })}
      >
        Add Todo
      </button>
    </div>
  );
}

// 在根组件中注入 QueryClient
function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <TodoList />
    </QueryClientProvider>
  );
}
```

## 12.关键Hook
- useMemo：用于缓存需要大量计算的结果，避免每次渲染都进行计算，只有依赖项更新时重新计算

```javascript
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ 除非 todos 或 filter 发生变化，否则不会重新执行 getFilteredTodos()
  const visibleTodos = useMemo(() => getFilteredTodos(todos, filter), [todos, filter]);
  // ...
}
```
- useEffectEvent：类似于事件处理函数，但在Effect中触发，能从依赖项中分割出不需要响应性的变量。永远不要把他们传给其他的组件或者 Hook

```javascript
const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
```
- memo：对传入的prop进行浅比较，基本类型直接比较值，引用类型比较引用地址，不变时不重新渲染

```javascript
import React, { memo } from 'react';

// 子组件
const Item = memo(({ item }) => {
  console.log('Rendering:', item.id);
  return <li>{item.text}</li>;
});
```
- 自定义Hook：共享的只是状态逻辑而不是状态本身。对 Hook 的每个调用完全独立于对同一个 Hook 的其他调用


## 13.Redux Toolkit

### 1.对比原生Redux 
- 代码量：
  - 原生 Redux：手动编写 action types、action creators、reducer 和中间件配置（约 40 行）。
  - Redux Toolkit：通过 createSlice 自动生成，代码量减少 50% 以上。
- 不可变性：
  - 原生 Redux：必须手动使用 { ...state } 确保不可变性。
  - Redux Toolkit：内置 Immer，允许直接修改状态（底层自动处理不可变性）。
- 异步处理：
  - 原生 Redux：需手动集成 redux-thunk，异步逻辑分散。
  - Redux Toolkit：内置 createAsyncThunk，集中管理异步生命周期。

### 2.原生Redux示例

```javascript
// ----------------------
// 原生 Redux 部分
// ----------------------

// Action Types (需要手动定义)
const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';
const INCREMENT_BY_AMOUNT = 'INCREMENT_BY_AMOUNT';

// Action Creators (需要手动编写)
const increment = () => ({ type: INCREMENT });
const decrement = () => ({ type: DECREMENT });
const incrementByAmount = (amount) => ({
  type: INCREMENT_BY_AMOUNT,
  payload: amount,
});

// Reducer (需要手动处理不可变性和 action 类型)
const initialState = { value: 0 };

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { ...state, value: state.value + 1 };
    case DECREMENT:
      return { ...state, value: state.value - 1 };
    case INCREMENT_BY_AMOUNT:
      return { ...state, value: state.value + action.payload };
    default:
      return state;
  }
}

// 异步 Action（需手动使用中间件，如 redux-thunk）
const incrementAsync = (amount) => (dispatch) => {
  setTimeout(() => {
    dispatch(incrementByAmount(amount));
  }, 1000);
};

// 创建 Store（需手动配置中间件和 DevTools）
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

const store = createStore(
  counterReducer,
  applyMiddleware(thunk)
);

// ----------------------
// React Redux 部分
// ----------------------
import { Provider, useSelector, useDispatch } from 'react-redux';

function CounterComponent() {
  const count = useSelector((state) => state.value);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => dispatch(increment())}>+1</button>
      <button onClick={() => dispatch(decrement())}>-1</button>
      <button onClick={() => dispatch(incrementAsync(5))}>
        Async +5
      </button>
    </div>
  );
}

// 在根组件注入 Store
function App() {
  return (
    <Provider store={store}>
      <CounterComponent />
    </Provider>
  );
}
```

### 3.使用示例
-  创建 Redux State Slice： slice 需要一个字符串名称来标识切片、一个初始 state 以及一个或多个定义了该如何更新 state 的 reducer 函数。slice 创建后 ，我们可以导出 slice 中生成的 Redux action creators 和 reducer 函数

```javascript
// features/counter/counterSlice.js
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      // Redux Toolkit 允许我们在 reducers 写 "可变" 逻辑。它
      // 并不是真正的改变状态值，因为它使用了 Immer 库
      // 可以检测到“草稿状态“ 的变化并且基于这些变化生产全新的
      // 不可变的状态
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      // action.payload为方法传入的参数
      state.value += action.payload
    }
  }
})
// 每个 case reducer 函数会生成对应的 Action creators
export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer

```

- 将 Slice Reducers 添加到 Store 中：通过在 reducer 参数中定义一个字段，我们告诉 store 使用这个 slice reducer 函数来处理对该状态的所有更新

```javascript
// app/store.js
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```

- 为 React 提供 Redux Store：引入我们刚刚创建的 store , 通过 React-Redux 的 <Provider>将 <App> 包裹起来,并将 store 作为 prop 传入

```javascript
// index.js
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
import store from './app/store'
import { Provider } from 'react-redux'

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

- 在 React 组件中使用 Redux 状态和操作：使用 useSelector 从 store 中读取数据，使用 useDispatch dispatch actions

```javascript
// features/counter/Counter.js
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { decrement, increment } from './counterSlice'
import styles from './Counter.module.css'

export function Counter() {
  const count = useSelector(state => state.counter.value)
  const dispatch = useDispatch()

  return (
    <div>
      <div>
        <button
          aria-label="Increment value"
          onClick={() => dispatch(increment())}
        >
          Increment
        </button>
        <span>{count}</span>
        <button
          aria-label="Decrement value"
          onClick={() => dispatch(decrement())}
        >
          Decrement
        </button>
      </div>
    </div>
  )
}
```

### 4.异步逻辑与数据请求
- 使用 createAsyncThunk 请求数据：过程中实际会触发两个action`counter/fetchCount/pending`和`counter/fetchCount/fulfilled`

```javscript
export const incrementAsync = createAsyncThunk(
  'counter/fetchCount',
  async (amount) => {
    const response = await fetchCount(amount);
    // The value we return becomes the `fulfilled` action payload
    return response.data;
  }
);
```

- 定义额外的 case reducer：针对前面产生的两个action进行处理

```javascript
extraReducers: (builder) => {
    builder
      .addCase(incrementAsync.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(incrementAsync.fulfilled, (state, action) => {
        state.status = 'idle';
        state.value += action.payload;
      });
},
```

- 处理组件级别请求成功或失败：`dispatch(incrementAsync(incrementValue).unwrap()`执行返回Promise，可用于`try-catch`捕获错误


### 5.提升渲染性能createSelector
- 作用：用于记忆计算结果，若依赖参数未发生变化则不会重新计算
- 第一个参数：selector数组，返回的值作为依赖参数
- 第二个参数：接受依赖参数用作输出计算

```javascript
import { createSlice, createAsyncThunk, createSelector } from '@reduxjs/toolkit'

// omit slice logic

export const selectAllPosts = state => state.posts.posts

export const selectPostById = (state, postId) =>
  state.posts.posts.find(post => post.id === postId)

export const selectPostsByUser = createSelector(
  [selectAllPosts, (state, userId) => userId],
  (posts, userId) => posts.filter(post => post.user === userId)
)

const postsForUser = useSelector(state => selectPostsByUser(state, userId))
```

### 6.范式化数据createEntityAdapter
- 核心作用
  - 规范化结构：自动将数据存储为 { ids: [], entities: {} } 格式，通过 ID 快速索引。
  - 预置 Reducer：内置 addOne、updateMany、removeAll 等常用操作。
  - 高效 Selector：提供 selectAll、selectById 等方法，优化数据读取性能。
  - 排序支持：可自定义实体排序规则。
- 相比于直接存储完整数据：存储id与id对应数据，父组件仅获取id数组，子组件内根据id获取对应某份数据。如果只是修改某份数据中某个参数，则仅触发某个子组件的重新渲染。父组件若直接获取完整数据，当某个字段发生改变则会导致父组件重新渲染从而所有子组件重新渲染。

```javascript
// features/posts/postsSlice.js
import {
  createEntityAdapter
  // omit other imports
} from '@reduxjs/toolkit'

const postsAdapter = createEntityAdapter({
  sortComparer: (a, b) => b.date.localeCompare(a.date)
})

const initialState = postsAdapter.getInitialState({
  status: 'idle',
  error: null
})

// omit thunks

const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {
    reactionAdded(state, action) {
      const { postId, reaction } = action.payload
      const existingPost = state.entities[postId]
      if (existingPost) {
        existingPost.reactions[reaction]++
      }
    },
    postUpdated(state, action) {
      const { id, title, content } = action.payload
      const existingPost = state.entities[id]
      if (existingPost) {
        existingPost.title = title
        existingPost.content = content
      }
    }
  },
  extraReducers: {
    // omit other reducers

    [fetchPosts.fulfilled]: (state, action) => {
      state.status = 'succeeded'
      postsAdapter.upsertMany(state, action.payload)
    },
    [addNewPost.fulfilled]: postsAdapter.addOne
  }
})

export const { postAdded, postUpdated, reactionAdded } = postsSlice.actions

export default postsSlice.reducer

// Export the customized selectors for this adapter using `getSelectors`
export const {
  selectAll: selectAllPosts,
  selectById: selectPostById,
  selectIds: selectPostIds
  // Pass in a selector that returns the posts slice of state
} = postsAdapter.getSelectors(state => state.posts)

export const selectPostsByUser = createSelector(
  [selectAllPosts, (state, userId) => userId],
  (posts, userId) => posts.filter(post => post.user === userId)
)



// features/posts/PostsList.js

import {
  selectAllPosts,
  fetchPosts,
  selectPostIds,
  selectPostById
} from './postsSlice'

let PostExcerpt = ({ postId }) => {
  const post = useSelector(state => selectPostById(state, postId))
  // omit rendering logic
}

export const PostsList = () => {
  const dispatch = useDispatch()
  const orderedPostIds = useSelector(selectPostIds)

  // omit other selections and effects

  if (postStatus === 'loading') {
    content = <div className="loader">Loading...</div>
  } else if (postStatus === 'succeeded') {
    content = orderedPostIds.map(postId => (
      <PostExcerpt key={postId} postId={postId} />
    ))
  } else if (postStatus === 'error') {
    content = <div>{error}</div>
  }

  // omit other rendering
}
```


## 14.React-router
### 1.创建客户端路由

```javascript
import { createBrowserRouter } from "react-router-dom";
const router = createBrowserRouter([
  {
    path: "/",
    element: <App/>,
  },
]);
```

### 2.引入路由配置

```javascript
import { RouterProvider } from "react-router-dom";
<React.StrictMode>
    <RouterProvider router={router} />
</React.StrictMode>
```

### 3.设置错误路由对应页面errorElement
- 设置errorElement

```javascript
import { useRouteError } from "react-router-dom";

export default function ErrorPage() {
  const error = useRouteError(); // 获取错误信息
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
]);
```

- 设置无路径错误页面：让错误界面显示在子路由中

```javascript
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    action: rootAction,
    errorElement: <ErrorPage />,
    children: [
      //修改：添加无路径路由
      {
        errorElement: <ErrorPage />,
        children: [
          { index: true, element: <Index /> },
          {
            path: "contacts/:contactId",
            element: <Contact />,
            loader: contactLoader,
            action: contactAction,
          },
          /* the rest of the routes */
        ],
      },
    ],
  },
]);
```

### 4.嵌套路由children

```javascript
// 定义子路由路径对应的组件
const router = createBrowserRouter([
  {
    path: "/",
    element: <App/>,
    children: [
    { index: true, element: <Index /> }, // 当用户位于父路由时匹配组件
    {
      path: '/counter',
      element: <Counter/>
    }]
  },
]);

// 设置链接路由Router-Link
<Link to={`/counter`}>Your Name</Link>

// 指定子路由组件渲染位置Router-View
<div id="detail">
    <Outlet />
</div>
```


### 5.路由加载loader
- 设置对应路由进入后执行的加载函数：可用于接口获取路由列表相关

```javascript
import { getContacts } from "../contacts";

export async function loader() {
  const contacts = await getContacts();
  // 加载不到抛出错误会直接渲染错误路径
  if (!contact) {
    throw new Response("", {
      status: 404,
      statusText: "Not Found",
    });
  }
  return { contacts };
}

```

- 获取参数：绑定loader的加载函数可通过`params`获取路由上携带的参数如`contactId`

- 路由配置中绑定loader

```javascript
import Root, { loader as rootLoader } from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

- 组件中获取加载函数返回的数据用于渲染页面

```javascript
import {
  Outlet,
  Link,
  useLoaderData,
} from "react-router-dom";

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        {/* other code */}

        <nav>
          {contacts.length ? (
            <ul>
              {contacts.map((contact) => (
                <li key={contact.id}>
                  <Link to={`contacts/${contact.id}`}>
                    {contact.first || contact.last ? (
                      <>
                        {contact.first} {contact.last}
                      </>
                    ) : (
                      <i>No Name</i>
                    )}{" "}
                    {contact.favorite && <span>★</span>}
                  </Link>
                </li>
              ))}
            </ul>
          ) : (
            <p>
              <i>No contacts</i>
            </p>
          )}
        </nav>

        {/* other code */}
      </div>
    </>
  );
}
```

### 6.路由操作action
- 组件中配置`<Form>`：元素触发提交事件

```javascript
import {
  Outlet,
  Link,
  useLoaderData,
  Form,
} from "react-router-dom";
import { getContacts, createContact } from "../contacts";

export async function action() {
  const contact = await createContact();
  return { contact };
}

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          {/* other code */}
          <Form method="post">
            <button type="submit">New</button>
          </Form>
        </div>

        {/* other code */}
      </div>
    </>
  );
}
```

- 路由配置绑定action：表单提交事件被React-router的action拦截

```javascript
/* other imports */

import Root, {
  loader as rootLoader,
  action as rootAction,
} from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

- 处理表单提交参数：`formData.get(name)`获取表单内部元素对应name字段的字段值

```javascript
export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData); // 可以整合表单对象
  updates.first; // "Some"
  updates.last; // "Name"
  const firstName = formData.get("first");
  const lastName = formData.get("last");
  // ...
}
```

- 处理完毕后重定向页面

```javascript
import {
  Form,
  useLoaderData,
  redirect,
} from "react-router-dom";
import { updateContact } from "../contacts";

export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}

/* existing code */
```


### 7.路由状态useNavigation
- 活动链接样式：`NavLink`获取当前路由状态

```javascript
// 当用户访处于NavLink指定的URL时，isActive将为true。当链接即将被激活时（数据仍在加载中）， isPending 将为 true
<NavLink
    to={`contacts/${contact.id}`}
    className={({ isActive, isPending }) =>
      isActive
        ? "active"
        : isPending
        ? "pending"
        : ""
    }
  >
    {/* other code */}
</NavLink>
```

- 全局待定用户界面：`useNavigation`获取当前导航状态

```javascript
import {
  // existing code
  useNavigation,
} from "react-router-dom";

// existing code

export default function Root() {
  const { contacts } = useLoaderData();
  const navigation = useNavigation();
  // 当应用程序正在导航到一个新的 URL 并为其加载数据时， navigation.location 就会显示出来
  const searching =
    navigation.location &&
    new URLSearchParams(navigation.location.search).has(
      "q"
    );//添加searching

  return (
    <>
      <div id="sidebar">{/* existing code */}</div>
      <div
        id="detail"
        className={
          navigation.state === "loading" ? "loading" : ""
        }
      >
        <Outlet />
      </div>
    </>
  );
}
```

- useNavigate控制路由跳转

```javascript
import {
  Form,
  useLoaderData,
  redirect,
  useNavigate,
} from "react-router-dom";

export default function EditContact() {
  const { contact } = useLoaderData();
  const navigate = useNavigate();

  return (
    <Form method="post" id="contact-form">
      {/* existing code */}

      <p>
        <button type="submit">Save</button>
        <button
          type="button"
          onClick={() => {
            navigate(-1);
          }}
        >
          Cancel
        </button>
      </p>
    </Form>
  );
}
```


### 8.表单操作action
- 表单绑定action：表单事件将类似`<Link to>`提交跳转到对应字段值的路由中，由路由绑定action执行对应操作 

```javascript

<Form
  method="post"
  action="destroy"
  onSubmit={(event) => {
    if (
      !confirm(
        "Please confirm you want to delete this record."
      )
    ) {
      event.preventDefault();
    }
  }}
>
  <button type="submit">Delete</button>
</Form>
```

### 9.表单Get

- 客户端路由的GET提交：表单提交触发get请求，路由加载触发对应loader

```javascript
// 不指定method="post"则为get请求
<Form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</Form>
```

- 组件同步URL

```javascript
// existing code

//loader函数中加入响应get请求，获取请求详情，返回url参数用于同步表单信息
export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts, q };//添加q
}

export default function Root() {
  const { contacts, q } = useLoaderData();//使用q
  const navigation = useNavigation();
  const submit = useSubmit();//添加submit，通过submit可以直接触发get请求
  
   useEffect(() => {
    document.getElementById("q").value = q;
  }, [q]);//添加useEffect

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              defaultValue={q}//使用q
              onChange={(event) => {
                // 多次搜索时对历史堆栈进行替换而不是推入
                const isFirstSearch = q == null;
                submit(event.currentTarget.form, {
                  replace: !isFirstSearch,
                });
              }}//添加onChange
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```


### 10.当前导航的数据突变useFetcher
- 使用`fetcher.Form`：仅调用指定action但不会跳转URL

```javascript
import {
  useLoaderData,
  Form,
  //修改：添加useFetcher
  useFetcher,
} from "react-router-dom";

// existing code

function Favorite({ contact }) {
  //修改：添加useFetcher
  const fetcher = useFetcher();
  // 无需等待action执行完毕，可以直接获取提交的表单数据进行快速页面响应
  if (fetcher.formData) {
    favorite = fetcher.formData.get("favorite") === "true";
  }

  return (
    <fetcher.Form method="post" action="counter">
        <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```