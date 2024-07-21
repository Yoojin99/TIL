# Routing

Routing 은 들어오는 request 를 처리할 적절한 request handler 를 찾는 과정.

Vapor 의 routing 은 고성능의 `RoutingKit` 의 trie-node router 가 core 임.

## HTTP request

먼저 HTTP request 의 몇가지 기본 사항을 이해해야 함.

```http
GET /hello/vapor HTTP/1.1
host: vapor.codes
content-length: 0
```

위는 `/hello/vapor` URL 의 HTTP `GET` 요청. 

아래와 같은 URL 을 입력했을 경우 브라우저가 위와 같은 HTTP request  를 만들게 됨.

```
http://vapor.codes/hello/vapor
```

### HTTP Method

|메서드|CRUD|
|-|-|
|GET|Read|
|Post|Create|
|Put|Replace|
|Patch|Update|
|DELETE|Delete|

### Request Path

HTTP 메서드 바로 뒤에는 요청 URI 가 따라옴. `/` 로 시작하는 path, `?` 뒤에 오는 옵셔널 쿼리 문자열이 포함됨. 

Vapor 는 HTTP 메서드와 path 를 사용해서 request 를 라우팅함.

URI - HTTP 버전 - 0개 이상의 헤더 - body

(GET 요청에는 body 가 없다)

### Router Methods

Vapor 에서는 아래와 같이 요청이 처리됨

```swift
app.get("hello", "vapor") { req in 
    return "Hello, vapor!"
}
```

모든 흔한 HTTP 메서드는 `Applications` 내에 메서드로 존재함. 가령 `.get()` 이 메서드는 아래와 같이 정의되어 있음.

```swift
extension RoutesBuilder {
    @discardableResult
    @preconcurrency
    public func get<Response>(
        _ path: PathComponent...,
        use closure: @Sendable @escaping (Request) async throws -> Response
    ) -> Route
    where Response: AsyncResponseEncodable
    {
        return self.on(.GET, path, use: closure)
    }
}
```

`app.get()` 에서 하나 이상의 문자열 인자를 받을 수 있고, 각 문자열들은 `/` 로 분리된 요청 path 를 나타낸다.

`on` 을 사용해서 아래와 같이 작성할 수 있다.

```swift
app.on(.GET, "hello", "vapor") { ... }
```

위 route 를 등록해서 아래의 HTTP 요청을 하면 

```http
GET /hello/vapor HTTP/1.1
host: vapor.codes
content-length: 0
```

아래의 HTTP 응답을 받게 됨

```http
HTTP/1.1 200 OK
content-length: 13
content-type: text/plain; charset=utf-8

안녕, vapor!
```

### Route Parameters

HTTP path 를 동적으로 만들 수 있음. 

앞선 예제에서는 "vapor" 이름이 path, response 에서 하드코딩되어 있었는데 이를 `/hello/<any name>` 을 방문할 수 있게 동적으로 만들 수 있음.

```swift
app.get("hello", ":name") { req async -> String in
    let name = req.parameters.get("name")!
    return "Hello, \(name)!"
}
```

`:` prefix 를 가진 path 요소를 사용해서 동적 component 임을 router 에 알려줄 수 있음. `req.parameters` 를 사용해서 문자열의 값을 가져올 수 있음.

## Routes

Route 는 HTTP 메서드, URI path 를 처리할 request handler 를 명시함. 추가 메타데이터를 저장할 수도 있음.

### Methods

Route 는 HTTP 헬더 메서드를 사용해서 `Application` 에 바로 등록될 수 있음.

```swift
// responds to GET /foo/bar/baz
app.get("foo", "bar", "baz") { req in
    ...
}
```

Route handler 는 `ResponseEncodable` 한 무엇이든 return 할 수 있음. 여기에는 `Content`, `async` 클로저, future 값이 `ResponseEncodable` 한 `EventLoopFuture` 가 될 수 있음.

`in` 구문 전에 `-> T` 를 통해 리턴타입을 지정할 수 있음. 

```swift
app.get("foo") { req -> String in
    return "bar"
}
```

* Route helper 메서드
  * `get`
  * `post`
  * `patch`
  * `put`
  * `delete`
  
HTTP 헬퍼 메서드에 추가로 HTTP 메서드를 입력 파라미터로 갖는 `on` 함수가 있음.

```swift
// responds to OPTIONS /foo/bar/baz
app.on(.OPTIONS, "foo", "bar", "baz") { req in
    ...
}
```

### Path Component

각 route 등록 메서드는 `PathComponent` 의 variadic 리스트를 인자롤 받을 수 있음. String literal 로 표현 가능하고 다음 4가지 케이스가 존재함.

* Constant (`foo`)
* Parameter (`:foo`)
* Anything (`*`)
* Catchall (`**`)

#### Constant

정적 route component. 정확히 일치하는 request 만 허용됨

```swift
// responds to GET /foo/bar/baz
app.get("foo", "bar", "baz") { req in
    ...
}
```

#### Parameter

동적 route component. 이 위치에 어떤 문자열이 오더라도 허용됨. 파라미터 path component 는 `:` prefix 를 갖고 있음. `:` 뒤에 오는 문자열은 parameter 의 이름. 이 이름을 사용해서 parameter 값을 추출할 수 있음.


```swift
// responds to GET /foo/bar/baz
// responds to GET /foo/qux/baz
// ...
app.get("foo", ":bar", "baz") { req in
    ...
}
```

#### Anything

Parameter 와 매우 비슷하지만 값이 버려지게 됨

```swift
// responds to GET /foo/bar/baz
// responds to GET /foo/qux/baz
// ...
app.get("foo", "*", "baz") { req in
    ...
}
```

#### Catchall

하나 이상의 component 와 매칭되는 동적 route component. 

```swift
// responds to GET /foo/bar
// responds to GET /foo/bar/baz
// ...
app.get("foo", "**") { req in 
    ...
}
```

### Parameters

`:` prefix 가 있는 path component 를 사용하면 URI 의 해당 위치에 있는 값은 `req.parameters` 에 저장됨.

```swift
// responds to GET /hello/foo
// responds to GET /hello/bar
// ...
app.get("hello", ":name") { req -> String in
    let name = req.parameters.get("name")!
    return "Hello, \(name)!"
}
```

`req.parameters.get` 은 파라미터를 자동으로 `LosslessStringConvertible` 타입으로 형변환할 수 있음.

```swift
app.get("hello", ":x") { req -> String in
    guard let int = req.parameters.get("x", as: Int.self) else {
        throw Abort(.badRequest)
    }
    
    return "\(int) is a great number"
}
```

Catchall(`**`) 에 의해 매칭된 URI 값들은 `req.parameters` 에 `[String]` 으로 저장되어 있음. `req.parameters.getCatchall` 을 사용해서 이 컴포넌트들에 접근할 수 있음.

```swift
// responds to GET /hello/foo
// responds to GET /hello/foo/bar
// ...
app.get("hello", "**") { req -> String in
    let name = req.parameters.getCatchall().joined(separator: " ")
    return "Hello, \(name)!"
}
```

### Body Streaming

`on` 메서드를 사용해서 route 를 등록할 때 어떻게 요청 body 가 처리될지를 명시할 수 있음. 

기본적으로 요청 body 는 handler 가 불리기 전에 메모리에 모여짐. 이 덕분에 애플리케이션이 들어오는 요청을 비동기적으로 읽어도 요청 컨텐츠 decoding 은 동기적으로 할 수 있음.

기본적으로 Vapor 는 body collection 을 16KB로 한정함. 이는 `app.routes` 에서 설정할 수 있음

```swift
// Increases the streaming body collection limit to 500kb
app.routes.defaultMaxBodySize = "500kb"
```

만약 body 가 이 한도치를 초과하면 `413 Payload Too Large` 에러가 던져짐.

개별 route 에 body collection 전략을 다르게 적용하고 싶으면 아래와 같이 `body` 파라미터를 사용함.

```swift
// Collects streaming bodies (up to 1mb in size) before calling this route.
app.on(.POST, "listings", body: .collect(maxSize: "1mb")) { req in
    // Handle request. 
}
```

`maxSize` 를 `collect` 안에 넣어서 전달하면 그 route 에 설정되어 있던 기본 application default 를 override 함. Application의 기본값을 사용하려면 `maxSize` 인자를 빼버림.

파일 업로드 같은 큰 요청은 요청 body 를 버퍼에 collecting 하는게 시스템 메모리에 부담을 줄 수 있음. 요청 body 를 collect 하지 않으려면 아래와 같이 `stream` 전략을 사용함.

```swift
// Request body will not be collected into a buffer.
app.on(.POST, "upload", body: .stream) { req in
    ...
}
```

요청 body 가 streamed 되면 `req.body.data` 가 `nil`이 됨. `req.body.drain` 을 사용해서 route 에 전달된 각 chunk 를 처리해야 함.

### Case Insensitive Routing

Routing 의 기본 동작은 case에 민감함. `Constant` path component 는 case-insensitive 하게 설정할 수 있음. 이를 위해 application startup 이전에 아래와 같이 설정.

```swift
app.routes.caseInsensitive = true
```

### Viewing Routes

`Routes` 서비스를 만들거나 `app.routes` 를 사용해서 앱 route들에 접근 가능

```swift
print(app.routes.all) // [Route]
```

Vapor 의 `routes` 커맨드로 확인 가능

```
$ swift run App routes
+--------+----------------+
| GET    | /              |
+--------+----------------+
| GET    | /hello         |
+--------+----------------+
| GET    | /todos         |
+--------+----------------+
| POST   | /todos         |
+--------+----------------+
| DELETE | /todos/:todoID |
+--------+----------------+
```

### Metadata

모든 route 등록 메서드는 `Route` 를 리턴함. 이를 통해 route 의 `userInfo` 딕셔너리에 메타데이터를 추가할 수 있음. 

Description 를 추가하는 default 메서드도 있음.

```swift
app.get("hello", ":name") { req in
    ...
}.description("says hello")
```

## Route Groups

Route grouping 을 통해 path prefix 를 가진 route 들의 집합이나 특정 middleware 를 생성할 수 있음. Grouping 은 builder 와 클로저 기반 문법을 지원함.

모든 grouping 메서드는 `RouteBuilder` 를 리턴하기 때문에 다른 route building 메서드를 중첩하거나 섞어서 사용할 수 있음.

### Path Prefix

Path prefixing route group 은 route 들의 group 에 하나 이상의 path component 를 추가할 수 있게 함.

```swift
let users = app.grouped("users")
// GET /users
users.get { req in
    ...
}
// POST /users
users.post { req in
    ...
}
// GET /users/:id
users.get(":id") { req in
    let id = req.parameters.get("id")!
    ...
}
```

`get`, `post` 같은 메서드들은 `grouped` 내에 전달 될 수 있음.
위 예시를 클로저 문법으로 다시 작성하면 아래와 같음.

```swift
app.group("users") { users in
    // GET /users
    users.get { req in
        ...
    }
    // POST /users
    users.post { req in
        ...
    }
    // GET /users/:id
    users.get(":id") { req in
        let id = req.parameters.get("id")!
        ...
    }
}
```

Path prefixing route group 을 중첩하면 CRUD API 를 명확하게 정의할 수 있음.

```swift
app.group("users") { users in
    // GET /users
    users.get { ... }
    // POST /users
    users.post { ... }

    users.group(":id") { user in
        // GET /users/:id
        user.get { ... }
        // PATCH /users/:id
        user.patch { ... }
        // PUT /users/:id
        user.put { ... }
    }
}
```

### Middleware

Route groupd 에 middleware 를 추가할 수 있음.

```swift
app.get("fast-thing") { req in
    ...
}

app.group(RateLimitMiddleware(requestsPerMinute: 5)) { rateLimited in
    rateLimited.get("slow-thing") { req in
        ...
    }
}
```

이는 다른 인증 middleware 를 사용하는 route 들의 부분 집합을 보호하는데 유용함.

```swift
app.post("login") { ... }
let auth = app.grouped(AuthMiddleware())
auth.get("dashboard") { ... }
auth.get("logout") { ... }
```

## Redirections

Redirect 는 오래된 경로를 새로운 경로로 다시 전달하거나 인증되지 않은 사용자를 로그인 페이지로 리다이렉트시키거나 새로운 API 를 지원하기 위한 용도 등의 시나리오에서 유용함.

요청을 redirect 시키려면 아래와 같이 정의함.

```swift
req.redirect(to: "/some/new/path")
```

Redirect 의 타입을 명시할 수 있음. 예를 들어 페이지를 영구적으로 redirect 해서 SEO 가 올바르게 업데이트되게 할 수 있음.

```swift
req.redirect(to: "/some/new/path", redirectType: .permanent)
```

* `Redirect` 
  * `.permanent` : 301 Permanent redirect 리턴
  * `.normal` : 303 see other redirect 리턴. 이는 Vapor 의 기본값이며 클라이언트에게 GET 요청으로 redirect 를 하라고 알려줌.
  * `.temporary` : 307 Temporary redirect 리턴. 이는 클라이언트에게 요청에 사용한 HTTP 메서드를 그대로 유지하라고 알려줌.