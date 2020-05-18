```
1. 清除一个全局变量
    Clear a global variable
    对应脚本：
    postman.clearGlobalVariable("variable_key");
    参数：需要清除的变量的key

2.清除一个环境变量
    Clear an environment variable
    对应脚本：
    postman.clearEnvironmentVariable("variable_key");
    参数：需要清除的环境变量的key

3.response包含内容
    Response body:Contains string
    对应脚本：
    tests["Body matches string"] =responseBody.has("string_you_want_to_search");
    参数：预期内容

4.将xml格式的response转换成son格式
    Response body:Convert XML body to a JSON Object
    对应脚本：
    var jsonObject = xml2Json(responseBody);
    参数：（默认不需要设置参数,为接口的response）需要转换的xml

5.response等于预期内容
    Response body:Is equal to a string
    对应脚本：
    tests["Body is correct"] = responseBody === "response_body_string";
    参数：预期response

6.json解析key的值进行校验
    Response body:JSON value check
    对应脚本：
    tests["Args key contains argument passed as url parameter"] = 'test' in responseJSON.args
    参数：test替换被测的值，args替换被测的key

7.检查response的header信息是否有被测字段
    Response headers:Content-Type header check
    对应脚本：
    tests["Content-Type is present"] = postman.getResponseHeader("Content-Type");
    参数：预期header

8.响应时间判断
    Response time is less than 200ms
    对应脚本：
    tests["Response time is less than 200ms"] = responseTime < 200;
    参数：响应时间

9.设置全局变量
      Set an global variable
      对应脚本：
      postman.setGlobalVariable("variable_key", "variable_value");
      参数：全局变量的键值

10.设置环境变量
      Set an environment variable
      对应脚本：
      postman.setEnvironmentVariable("variable_key", "variable_value");
      参数：环境变量的键值

11.判断状态码
      Status code:Code is 200
      对应脚本：
      tests["Status code is 200"] = responseCode.code != 400;
      参数：状态码

12.检查code name 是否包含内容
      Status code:Code name has string
      对应脚本：
      tests["Status code name has string"] = responseCode.name.has("Created");
      参数：预期code name包含字符串

13.成功的post请求
      Status code:Successful POST request
      对应脚本：
      tests["Successful POST request"] = responseCode.code === 201 || responseCode.code === 202;

14.微小验证器
       Use Tiny Validator for JSON data            
       对应脚本： 
        var schema = {
         "items": {
         "type": "boolean"
             }
         };
        var data1 = [true, false];
        var data2 = [true, 123];
        console.log(tv4.error);
        tests["Valid Data1"] = tv4.validate(data1, schema);
        tests["Valid Data2"] = tv4.validate(data2, schema);
        参数：可以修改items里面的键值对来对应验证json的参数
```

