# 后端架构

## "服务端渲染"

选学 Python 小学期的同学应该已经接触过 "服务端渲染" 了. **服务端渲染** 是指把渲染的工作交给服务端, 当用户请求某个页面的时候, 服务端查询对应的模板, 按照模板的指示将数据从数据库中取出, 填充到模板中去. 用户得到的是一个完整的 HTML 页面, 里面已经包含了所有需要的信息.

拿我去年小学期的 Django 项目举个例子 (你现在并不需要看懂这些代码, 我们后面会讲到):

```python
def ViewNews(request, document_id: str):
    # 从数据库中取出新闻
    news = News.objects.get(document_id=document_id)

    # 解析出新闻的分类
    news.category_name = NewsCategory.objects.get(id=news.category).category

    # 找到新闻的所有评论
    news.comments = NewsComment.objects.filter(
        news=news).order_by('-created_at')
    for comment in news.comments:
        comment.created_time = comment.created_at.strftime('%Y-%m-%d %H:%M')

    # 找到新闻的所有关键词
    news.keywords = NewsKeyword.objects.filter(news=news)

    # 以可读的形式展示新闻的发布时间
    news.created_time = news.created_at.strftime('%Y-%m-%d %H:%M')

    # 把新闻里面的图片标签解析成真正的本地图片链接
    news.content = NewsImagePattern.sub(
        f'<br /> <img src="/static/crawled/{news.document_id}_\\1.\\2" style="" /> <br />', news.content)

    # 把新闻里面的换行符替换成 HTML 换行符
    news.content = news.content.replace('\n', '<br /><br />')

    # 扔进模板 (Template) 里面进行渲染
    return render(request, 'viewNews.html', {'page_title': news.title, 'news': news})
```

可以看到, 为了渲染正确的新闻页面, 后端做了相当多的处理工作; 并且, 这些工作用到了后端的数据库, 对数据库的改动可能影响到渲染, 产生不可预期的后果.

同时, 服务端渲染的页面是静态的, 无法满足新时代前端的譬如 "懒加载", "部分刷新" 等等需求; 渲染的工作量也挤占了宝贵的服务器资源.

~~既然用户的 CPU 那么好, 不用白不用~~ 我们把这些可以由前端完成的工作扔给前端去做不就完了嘛~ 于是就有了 "客户端渲染".

## "客户端渲染"

**客户端渲染** 常常和 **前后端分离的开发范式** 一起出现. 我们把 Web 应用的开发一分为二:

- **前端** 的主要职责是面向用户, 目标是 **给用户以最好的使用体验**. 前端工程师负责设计页面 UI, 给用户以最贴近直觉的交互, **尽力对用户的输入给出即时的反馈和友善的 (错误) 提示** (这些 _或许_ 会在前端的课程中讲到, 但是也不一定);

- **后端** 的主要职责是面向数据, 目标是 **以最高效的方式处理系统逻辑**. 后端工程师负责设计数据库结构, 用最节省资源的方式满足前端对数据的任何需求, 同时 **尽力保证数据的安全性和一致性**.

在这种开发范式下, 一切前端的逻辑都写在 **静态** 的脚本中, 因此 _不会随时间 / 请求发生变化_ (除非版本更新), 可以利用浏览器的缓存机制 / CDN 加速, 提高用户体验; 而后端只需要提供数据, 不关心其它的内容, 因此可以专注于处理系统逻辑, 提高开发效率.

## API

在前后端分离的开发范式下, 前端和后端之间的通信方式就是 **API** (Application Programming Interface). API 可以认为是前后端通信的 _具体约定_. 从全局上讲, 可以比如:

- 前后端全部使用 JSON 格式进行通信; (或, 前后端全部使用 XML 格式进行通信, 再或者什么奇怪的格式也不是不行)

- 前端向后端发送请求的时候, 全都请求 `/api` 这个路径, Body 内容为:

  ```json
  {
    "action": "getNews",
    "data": {
      "id": 1
    }
  }
  ```

  其中 `action` 为操作名, `data` 为操作所需的数据, 在各个 API 中分别定义;

- 后端返回给前端的 JSON 数据格式为:

  ```json
  {
    "code": 0,
    "message": "success",
    "data": {
      "id": 1,
      "title": "Hello, World!",
      "content": "Hi"
    }
  }
  ```

  其中 `code` 为操作结果, `message` 为操作结果描述, `data` 为操作返回的数据, 在各个 API 中分别定义; 而 `code` 与 `message` 有统一的规定, 以便前端根据 code 判断需要执行的操作.

对其中的每一个 API, 也应该定义明确的请求方式 / 参数 / 要求 / 返回值 / 可能出现的错误等等, 以便前端和后端开发人员能够明确地知道如何开展工作.

### RESTful API

RESTful API 是一种 API 设计风格, 其核心思想是 **资源导向**. 在 RESTful API 中, 每一个 API 都对应一个资源, 而这个资源由一个 URL 表示. 例如, `/api/news/1` 就表示了 id 为 1 的新闻资源.

RESTful API 的请求方式与资源的关系如下:

- GET: 获取资源
- POST: 创建资源
- PATCH: 部分更新
- PUT: 更新资源
- DELETE: 删除资源

但在实际的操作中, 这一堆请求方式的语义很多时候 _并不准确_. 例如, 在搜索新闻的时候, 我们可能倾向于使用 POST 请求而非 GET 请求.

因此, 在实际的网站中, (个人认为), **API 只有 `GET` 和 `POST` 两种请求方式, API 的意义由其路径明确表出, 所有对服务端状态有修改的请求用 POST, 其它的用 GET** 是一个较好的 API 范式. ~~(如 `GET https://zhjwxk.cic.tsinghua.edu.cn/xkBks.vxkBksXkwxbBs.do?p_xnxq=2024-2025-1&pathContent=%D1%A1%BF%CE%CE%DE%D0%A7%D0%C5%CF%A2%B2%E9%D1%AF`)~~

**_不要在 URL, 数据表, 变量名里面写拼音首字母缩写!!!_**

## MVC

**M**odel, **V**iew, **C**ontroller 是一种 Web 应用开发范式, 其核心思想是 **将数据, 视图, 逻辑分离**. 在 MVC 中, 数据由 Model 管理, 视图由 View 管理, 逻辑由 Controller 管理.

在 MVC 中, 数据与视图是 **解耦** 的, 即数据的变化不会影响视图, 视图的变化也不会影响数据. 这使得数据与视图的维护变得更加容易.

在前后端分离的开发范式下, View 由前端负责, Model 和 Controller 由后端负责.
