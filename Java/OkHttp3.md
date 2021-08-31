# OkHttp3
## 1、添加 maven 依赖
```xml
        <dependency>
            <groupId>com.squareup.okhttp3</groupId>
            <artifactId>okhttp</artifactId>
            <version>3.14.9</version>
        </dependency>
```

## 2、创建 Http 客户端
```java
OkHttpClient okHttpClient = new OkHttpClient();
```

## 3、构造 Request 对象
```java
MediaType mediaType = MediaType.parse("application/json; charset=utf-8");
String requestBody = "I am Jdqm.";
Request request = new Request.Builder()
    .url("http://www.baidu.com")
    .post(RequestBody.create(mediaType, requestBody))
    .build();
```

## 4、封装 Request 为 Call 对象
```java
Call call = okHttpClient.newCall(request);
```

## 5、发起请求
```java
//1、异步请求
call.enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {

    }

    @Override
    public void onResponse(Call call, Response response) throws IOException {
        logging.info("onResponse: " + response.body().string());
    }
});

//2、同步请求
FutureTask<Response> futureTask = new FutureTask<>(call::execute);
new Thread(futureTask).start();
Response response = futureTask.get();
```