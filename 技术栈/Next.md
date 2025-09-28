# Next
## 0.其他
- 错误处理
- 部分预渲染


## 1.基础概念
### 1.文件用途
- page.tsx：内容组件
- layout.tsx：布局组件
- loading.tsx：加载组件（内容组件未渲染完成时显示加载组件）


## 2.路由
### 1.基础概念
- 路由匹配优先级：静态路由 > 动态路由 > 捕获路由
- 嵌套路由：访问子路由时父路由与子路由的page文件互斥，只渲染子路由的page文件
- 可访问：只有当文件夹内创建`page.tsx`或`route.tsx`文件时路由才可访问，且只有`page.tsx`或`route.tsx`文件返回的内容会被发送到客户端
- 路由排除：文件夹命名加入前缀`_`时文件夹及其子路由段排除在路由系统外


### 2.静态路由
- 文件夹路由：app 目录下各目录自动映射为相应的路由，不需要额外的路由配置（内部支持嵌套）
- 路由文件：layout、page文件同时存在时，page作为children在layout中渲染；若仅存在page文件则直接渲染page文件

### 3.动态路由
- 定义：文件夹路由使用方括号 [param] 定义动态路由段
- 参数匹配
  - `app/blog/[slug]/page.tsx`匹配 `/blog/post-1`，`params.slug`值为 `"post-1"`
  - `app/shop/[category]/[product]/page.tsx`匹配 `/shop/electronics/laptop`，params值为 `{ category: "electronics", product: "laptop" }`
- 组件​获取动态参数
```javascript
export default async function Page({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = await params
  return <div>我的文章: {slug}</div>
}
```
- 构建时静态生成路由：提前生成所有可能的动态路由页面，无需服务器计算动态路由，减小服务器压力
```javascript
// 从接口中获取所有可能的slug值在构建时生成静态文件
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(res => res.tsxon());
  return posts.map(post => ({ slug: post.slug })); // 返回路径参数数组
}

export default function BlogPost({ params }) {
  const { slug } = params; // 通过 params 获取当前 slug
  // 渲染内容...
}
```

### 4.捕获路由
- 捕获所有路由：`app/docs/[...slug]/page.tsx`匹配 `/docs/a/b/c`，`params.slug`值为 `["a", "b", "c"]`
- 可选捕获路由：`app/docs/[[...slug]]/page.tsx`支持无参数，无参数时`params.slug`值为 `undefined`

### 5.路由组
- 文件命名：`app/(customePath)`
- 特性：该命名不作为实际路由匹配，仅作为路由组
- 功能：路由组内所有路由共用组内定义的layout布局文件作为共享布局

### 6.路由导航
```javascript
import Link from 'next/link';
<Link href="/about">About Us</Link>
```

## 3.组件
### 1.服务端组件
- 默认：项目内默认均为服务端组件
- 特性：服务端组件在服务器端执行，代码不会发送到客户端
- 功能：可直接访问数据库、内部 API 或微服务、敏感环境变量
- 限制：不支持​ useState、useEffect或 React Context，需依赖 props 或缓存机制（如 fetch缓存）共享数据
- 服务端组件负载RSC：渲染后的 React 服务端组件树的紧凑二进制表示
  - 服务端组件的渲染结果
  - 客户端组件应渲染位置的占位符及其 JavaScript 文件的引用
  - 从服务端组件传递给客户端组件的任何 props 
- 适用场景
  - 从数据库或靠近数据源的 API 获取数据
  - 使用 API 密钥、令牌等敏感信息而不暴露给客户端
  - 减少发送到浏览器的 JavaScript 体积
  - 提升首次内容绘制 (FCP)，并逐步将内容流式传输到客户端
- 使用 fetch API 获取数据
```javascript
// app/posts/page.tsx
export default async function Page() {
  const data = await fetch('https://jsonplaceholder.typicode.com/posts')
  const posts = await data.tsxon()
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```
- 使用 ORM 或数据库
```javascript
// app/posts/page.tsx 
import { db, posts } from '@/lib/db'

export default async function Page() {
  const allPosts = await db.select().from(posts)
  return (
    <ul>
      {allPosts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```


### 2.客户端组件
- 定义：组件顶部要使用`use client`标志，其所有导入和子组件都将被视为客户端包的一部分
- 水合：React 将事件处理程序附加到 DOM 的过程，使静态 HTML 具有交互性
- 使用场景
  - 需要状态管理和事件处理。例如 onClick、onChange
  - 需要生命周期逻辑。例如 useEffect
  - 使用浏览器专属 API。例如 localStorage、window、Navigator.geolocation 等
  - 使用自定义钩子
- 使用 use 钩子流式传输数据：use接收promise，promise解析期间Suspense显示fallback内容
```javascript
/* 服务端组件 */
import Posts from '@/app/ui/posts
import { Suspense } from 'react'

export default function Page() {
  // 不要等待数据获取函数
  const posts = getPosts()

  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Posts posts={posts} />
    </Suspense>
  )
}

/* 客户端组件 */
'use client'
import { use } from 'react'

export default function Posts({
  posts,
}: {
  posts: Promise<{ id: string; title: string }[]>
}) {
  const allPosts = use(posts)

  return (
    <ul>
      {allPosts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```

### 3.应用
- 客户端组件内嵌套服务端组件
```javascript
'use client'
// 服务端组件作为children插槽传入
export default function Modal({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>
}
```
- 服务端组件内使用context
```javascript
/* 客户端组件theme-provider.tsx */
'use client'

// 创建客户端组件提供context
import { createContext } from 'react'

export const ThemeContext = createContext({})

export default function ThemeProvider({
  children,
}: {
  children: React.ReactNode
}) {
  return <ThemeContext.Provider value="dark">{children}</ThemeContext.Provider>
}

/* 服务端组件layout.tsx */
import ThemeProvider from './theme-provider'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>
        <ThemeProvider>{children}</ThemeProvider>
      </body>
    </html>
  )
}
```
- 第三方组件：第三方组件若未注明`use client`而直接在服务端组件中使用会报错，因此需要创建客户端组件包裹第三方组件
```javascript
'use client'

import { Carousel } from 'acme-carousel'

export default Carousel
```
- 环境变量：在 Next.js 中，只有以`NEXT_PUBLIC_`为前缀的环境变量会被包含在客户端代码包中。如果变量没有前缀，Next.js 会将其替换为空字符串。因此调用服务端环境变量的代码不能导入到客户端组件中


## 4.链接与导航
- 本质：将部分逻辑执行（服务端组件）放在服务端，由服务端渲染完成后将html页面发送给客户端，减少客户端的渲染压力、提高首屏渲染时间等
- 服务端渲染两种类型
  - 静态渲染（或预渲染）：在构建时或重新验证期间发生，结果会被缓存
  - 动态渲染：在客户端请求时实时发生
- 预取
  - 定义：用户导航前在后台加载路由，当用户点击链接时，渲染下个路由所需的数据已经存在于客户端
  - 实现：会在`<Link>`组件进入用户视口时自动预取其链接的路由
  - 禁止预取：`<Link prefetch={false}>`在渲染大量链接时可禁止预取避免不必要的资源消耗
  - 预取的路由内容量：取决于路由类型，静态路由下完整路由会被预取；动态路由下预取会被跳过，如果存在 loading.tsx 则部分预取
- 流失传输：使用`loading.tsx`时，动态路由未渲染完成会先显示`loading.tsx`的内容
- 客户端过渡：通过`<Link>`组件的客户端过渡，不会重新加载页面，而是保留所有共享布局和UI，用预取的加载状态或新页面（如果可用）替换当前页面
- 慢速网络：慢速网络下，预取可能在用户点击链接前无法完成，可获取预取状态显示加载状态提示用户
```javascript
'use client'
import { useLinkStatus } from 'next/link'

export default function LoadingIndicator() {
  const { pending } = useLinkStatus()
  return pending ? (
    <div role="status" aria-label="加载中" className="spinner" />
  ) : null
}
```
- 修改 URL 但不触发组件渲染​
  - `window.history.pushState({ id: 1 }, '', '/about')`：向浏览器历史记录栈添加新条目，用户可以导航回之前的状态。如，对产品列表进行排序
  - `window.history.replaceState(null, '', '/profile')`：用于替换浏览器历史记录栈中的当前条目，用户将无法通过导航返回前一个状态。如，用于切换应用的语言设置


## 5.图片
- 内置`<Image>`功能
  - 尺寸优化：自动为不同设备提供正确尺寸的图片，使用 WebP 等现代图片格式
  - 视觉稳定性：在图片加载时自动防止布局偏移 (CLS)
  - 更快页面加载：仅当图片进入视口时通过原生浏览器懒加载进行加载，可选模糊占位图
  - 资源灵活性：按需调整图片尺寸，即使是远程服务器存储的图片
- 使用
```javascript
import Image from 'next/image'

export default function Page() {
  return <Image src="" alt="" />
}
```
- 本地图片
```javascript
import Image from 'next/image'

export default function Page() {
  return (
    <Image
      src="/profile.png"
      alt="作者照片"
      width={500}
      height={500}
    />
  )
}
```
- 静态导入图片
```javascript
import Image from 'next/image'
import ProfileImage from './profile.png'

export default function Page() {
  return (
    <Image
      src={ProfileImage}
      alt="作者照片"
      // width={500} 自动提供
      // height={500} 自动提供
      // blurDataURL="data:..." 自动提供
      // placeholder="blur" // 加载时可选的模糊效果
    />
  )
}
```
- 远程图片
```javascript
/* app/page.tsx */
import Image from 'next/image'

// 构建过程中无法访问远程文件，您需要手动提供 width、height 和可选的 blurDataURL 属性
export default function Page() {
  return (
    <Image
      src="https://s3.amazonaws.com/my-bucket/profile.png"
      alt="作者照片"
      width={500}
      height={500}
    />
  )
}

/* next.config.ts */
import type { NextConfig } from 'next'

// 配置支持的url列表，安全地允许来自远程服务器的图片
const config: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 's3.amazonaws.com',
        port: '',
        pathname: '/my-bucket/**',
        search: '',
      },
    ],
  },
}

export default config
```


## 6.字体
### 1.google字体
```javascript
import { Geist } from 'next/font/google'

const geist = Geist({
  subsets: ['latin'],
})

// 字体作用域限定于它们所使用的组件
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={geist.className}>
      <body>{children}</body>
    </html>
  )
}
```

### 2.本地字体
```javascript
import localFont from 'next/font/local'

const myFont = localFont({
  src: './my-font.woff2',
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={myFont.className}>
      <body>{children}</body>
    </html>
  )
}
```


## 7.数据获取
### 1.去重请求
- 使用 fetch API 获取数据，去重请求`fetch('https://...', { cache: 'force-cache' })`
- 直接使用 ORM 或数据库，用 React cache 函数包裹数据获取
```javascript
import { cache } from 'react'
import { db, posts, eq } from '@/lib/db'

export const getPost = cache(async (id: string) => {
  const post = await db.query.posts.findFirst({
    where: eq(posts.id, parseInt(id)),
  })
})
```

### 2.数据预加载
```javascript
// 调用 preload() 来预加载数据但不阻塞后续进程（该数据获取方式需具备缓存效果）
import { getItem } from '@/lib/data'

export default async function Page({
  params,
}: {
  params: Promise<{ id: string }>
}) {
  const { id } = await params
  // 开始加载项目数据
  preload(id)
  // 执行另一个异步任务
  const isAvailable = await checkIsAvailable()

  return isAvailable ? <Item id={id} /> : null
}

export const preload = (id: string) => {
  // void 会执行给定的表达式并返回 undefined
  // https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void
  void getItem(id)
}
export async function Item({ id }: { id: string }) {
  const result = await getItem(id)
  // ...
}
```

### 3.数据更新
- 定义：服务端渲染的页面/数据会被 Next.js ​静态缓存，若页面/数据更新，必须主动触发重新生成
- revalidatePath：服务端函数中按路径刷新缓存
```javascript
import { revalidatePath } from 'next/cache'

export async function createPost(formData: FormData) {
  'use server'
  // 更新数据
  // ...

  revalidatePath('/posts')
}
```
- revalidateTag：服务端函数中按标签刷新缓存
```javascript
// app/api/products/route.js
export async function GET() {
  const data = await fetch('https://api.example.com/products', {
    next: { tags: ['products'] } // 为此请求添加标签
  });
  return Response.json(data);
}

// 在更新数据的操作中
import { revalidateTag } from 'next/cache';

export async function updateProduct(id, data) {
  await db.products.update(id, data);
  revalidateTag('products'); // 刷新所有带此标签的请求
}
```


## 8.服务端函数
- 定义：将`'use server'`指令放在异步函数的顶部以将其标记为服务端函数
- 功能：更新数据
- 服务端函数在以下情况下会自动被 startTransition 包裹
  - 通过 action 属性传递给 `<form>`
  - 通过 formAction 属性传递给 `<button>`
  - 传递给 useActionState
- 显示等待状态
```javascript
'use client'

import { useActionState, startTransition } from 'react'
import { createPost } from '@/app/actions'
import { LoadingSpinner } from '@/app/ui/loading-spinner'

export function Button() {
  const [state, action, pending] = useActionState(createPost, false)

  return (
    <button onClick={() => startTransition(action)}>
      {pending ? <LoadingSpinner /> : '创建文章'}
    </button>
  )
}
```

## 9.延迟加载
- next/dynamic：客户端延迟加载对应组件，减少渲染路由所需的 JavaScript 代码量，帮助提升应用的初始加载性能。即使父组件是客户端组件，默认仍会尝试服务端预渲染（除非显式禁用 ssr: false）
```javascript
'use client'

import { useState } from 'react'
import dynamic from 'next/dynamic'

// 客户端组件：
const ComponentA = dynamic(() => import('../components/A'))
const ComponentB = dynamic(() => import('../components/B'))
const ComponentC = dynamic(() => import('../components/C'), { ssr: false })

export default function ClientComponentExample() {
  const [showMore, setShowMore] = useState(false)

  return (
    <div>
      {/* 立即加载，但放在单独的客户端包中 */}
      <ComponentA />

      {/* 按需加载，仅在满足条件时加载 */}
      {showMore && <ComponentB />}
      <button onClick={() => setShowMore(!showMore)}>切换</button>

      {/* 仅在客户端加载 */}
      <ComponentC />
    </div>
  )
}
```