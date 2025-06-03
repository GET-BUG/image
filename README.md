## 图床仓库
### 使用CloudFlare创建Workers进行配置

**Cloudflare Workers 代码​**

```javascript
/**
 * Welcome to Cloudflare Workers! This is your first worker.
 *
 * - Run "npm run dev" in your terminal to start a development server
 * - Open a browser tab at http://localhost:8787/ to see your worker in action
 * - Run "npm run deploy" to publish your worker
 *
 * Learn more at https://developers.cloudflare.com/workers/
 */


addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  const url = new URL(request.url)
  
  // 1. 替换为你的 GitHub 仓库信息
  const GITHUB_USER = '用户名' 
  const GITHUB_REPO = '仓库'
  const GITHUB_BRANCH = 'main'  // 默认分支
  
  // 2. 构建 GitHub 原始文件 URL
  const ghUrl = `https://raw.githubusercontent.com/${GITHUB_USER}/${GITHUB_REPO}/${GITHUB_BRANCH}${url.pathname}`
  
  try {
    // 3. 请求 GitHub 资源
    const response = await fetch(ghUrl)
    
    // 4. 如果 GitHub 返回 404，则返回错误
    if (response.status === 404) {
      return new Response('Not Found', { status: 404 })
    }
    
    // 5. 成功则返回资源，并设置缓存
    return new Response(response.body, {
      headers: {
        'Content-Type': response.headers.get('Content-Type'),
        'Cache-Control': 'public, max-age=14400' // 4小时缓存
      }
    })
  } catch (err) {
    // 6. 错误处理
    return new Response('Internal Error', { status: 500 })
  }
}
```
