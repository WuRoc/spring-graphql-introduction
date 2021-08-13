# Spring GraphQL introduction

## 概要

JSUG Study Group 2021 Part 2 这是晚上的演示文稿的幻灯片和代码示例，彻底讨论了 Spring GraphQL。

## 文档构建

构建用 PlantUML 绘制的图形。

```bash
java -jar ~/plantuml.jar -tsvg docs/plantuml.pu
```

构建幻灯片。

```bash
npx @marp-team/marp-cli@latest --html --output docs/index.html docs/slide.md
```

我已将 / docs 设置为托管在 GitHub Pages 上，以便您可以在以下 URL 中查看幻灯片：

* [https://backpaper0.github.io/spring-graphql-introduction/](https://backpaper0.github.io/spring-graphql-introduction/)

## 演示程序

### 准备

```text
./mvnw spring-boot:run
```

在浏览器中打开[http://localhost:8080/my-graphiql](http://localhost:8080/my-graphiql)

#### ※关于GraphiQL

Spring GraphQL 内置的 GraphQL 不支持认证和订阅操作，所以我们准备了我们自己调整的 GraphQL。

源代码在 `my-graphiql` 中。

如果您有一个使用 create-react-app 创建的 React 应用程序并想对其进行自定义，请先使用 `npm install` 准备依赖库。

然后用 `npm start` 启动它。在定制过程中，建议在此处检查操作的同时进行开发。为此，在 Spring Boot 应用程序端设置了 CORS。

要将其嵌入到 Spring Boot 应用程序中，首先删除 Spring Boot 应用程序端的 `src/main/resources/static/my-graphiql`。然后运行 ​​`npm run build` 就会写出 `src/main/resources/static/my-graphiql` 中构建的 HTML 和 JS 文件。你所要做的就是 `mvn spring-boot:run`。

### query操作

试试幻灯片上的查询。

```text
query {
  article(id: 1) {
    id
    title
    content
    category {
      id
      name
    }
  }
}
```

尝试只获取标题。

```text
query {
  article(id: 1) {
    title
  }
}
```

尝试使用变量。

```text
query GetArticle($id: ID!) {
  article(id: $id) {
    title
  }
}
```

```javascript
{
  "id": 1
}
```

用 `curl` 试试。

```text
curl -s http://localhost:8080/graphql -H "Content-Type: application/json" -d '{"query": "{article(id: 1) { id, title, content, category { id, name } }}"}' | jq
```

### subscription

还可以尝试`subscription（订阅）`操作。

```text
subscription {
  count
}
```

它在结果区向上计数并从 1 到 10 显示。

用 `wscat` 检查一下。 `wscat` 可以通过 `npm install -g wscat` 安装。

```text
wscat --connect ws://localhost:8080/graphql
```

我还不了解`subscription`协议，所以阅读 Spring GraphQL 代码并按照我找到的步骤操作。

首先你需要`connection_init`。

```text
{"type": "connection_init"}
```

然后订阅。如果您等待，则返回一个每秒递增的值。

```text
{"type": "subscribe", "id": "...", "payload": {"query": "subscription { count }"}}
```

当然你也可以使用变量。

```text
{"type": "subscribe", "id": "...", "payload": {"query": "subscription Count($size: Int!) { count(size: $size) }", "variables": {"size": 5 }}}
```

### DataLoader

首先，N+1。

```text
query {
  comics {
    title
    author {
      name
    }
  }
}
```

如果查看`Fetch Query.comics` 之后的日志，可以看到`comics` 发出了1 个查询，`comics.author` 发出了10 个查询。

接下来是 Data Loader 版本。

```text
query {
  comics {
    title
    author(useDataLoader: true) {
      name
    }
  }
}
```

如果查看`Fetch Query.comics` 之后的日志，可以看到`comics` 和`comics.author` 都只需要发出一个查询。

### 分页

首先，在不指定 after 的情况下发出查询并检查返回值。

```text
query GitCommits {
  history {
    forward(first: 3) {
      edges {
        node {
          hash
          message
        }
        cursor
      }
      pageInfo {
        hasPreviousPage
        hasNextPage
        startCursor
        endCursor
      }
    }
  }
}
```

然后在查看 pageInfo 的值后在设置后尝试查询。

```text
query GitCommits {
  history {
    forward(first: 3, after: "3") {
      edges {
        node {
          hash
          message
        }
        cursor
      }
      pageInfo {
        hasPreviousPage
        hasNextPage
        startCursor
        endCursor
      }
    }
  }
}
```

也向后尝试。

```text
query GitCommits {
  history {
    backward(last: 3, before: "7") {
      edges {
        node {
          hash
          message
        }
        cursor
      }
      pageInfo {
        hasPreviousPage
        hasNextPage
        startCursor
        endCursor
      }
    }
  }
}
```

### WIP: 认证/授权

运行以下查询会导致错误（未经授权）。

```text
{
  security {
    protected
  }
}
```

如果在 `REQUEST HEADERS` 的地方写入如下 JSON 并执行，则不会出错并返回值。

```javascript
{
  "Authorization": "Basic ZGVtbzpzZWNyZXQ="
}
```

这将`@PreAuthorize("isAuthenticated()")` 添加到相应`DataFetcher` 中调用的组件的方法中。

我还举了一个用自定义指令表达身份验证的例子。以下查询将访问名为`@authenticated` 的自定义定向受保护字段。

```text
{
  security {
    protected2
  }
}
```

尝试使用和不使用 `Authorization` 标头的区别。

指标

```text
curl -s localhost:8080/actuator/metrics/graphql.request | jq
```

```text
curl -s localhost:8080/actuator/metrics/graphql.datafetcher | jq
```

```text
curl -s localhost:8080/actuator/metrics/graphql.error | jq
```

## 执照

CC BY 4.0 适用于幻灯片（docs / 下的文件），MIT 适用于源代码（幻灯片以外的文件）。

