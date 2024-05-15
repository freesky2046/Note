## 概述

 `data`特指已经序列化为二进制 `Data` 格式的 JSON二进制 数据，未序列化前本质就是一个按照 json 格式声明的字符串,在 swift中表示一个json 字符串很简单

```swift
let jsonString = """
{
    "name": "John",
    "age": 30,
    "email": "john@example.com"
}
"""
```

## Data ⇄String

string转换为 data

```swift
let data = jsonString.data(using: .utf8)
```

data 转换为 string

```swift
let decodeStr = String(data: data!, encoding: .utf8)
```

## Data ⇄ Model

data 转换为模型对象

声明对应的模式

```swift

struct Model: Codable {
    var name: String?
    var age: Int?
    var email: String?
}
```

转为Model对象

```swift
  /// ---> Model
  let jsonDecoder = JSONDecoder()
  guard let model = try? jsonDecoder.decode(Model.self, from: data) else { return }
  print(model.name!)
  print("\(model.age!)")
  print("\(model.email!)")
        
```

转为 Data

```swift
let jsonEncoder = JSONEncoder()
guard let encodeData = try? jsonEncoder.encode(model) else {
   return
}
print("成功")
```

## Data ⇄ Dictionary 或 Array

apple叫这种能转为jsondata 的字典或数组叫JSONObject。JSONObject是有条件的

> -  Top level object is an NSArray or NSDictionary
>
> - All objects are NSString, NSNumber, NSArray, NSDictionary, or NSNull
>
> -  All dictionary keys are NSStrings
>
> -    NSNumbers are not NaN or infinity

转为 Dictionary

```swift
     guard let dict = try? JSONSerialization.jsonObject(with: data, options:[.mutableContainers] ) else { return }
        print(dict)
```

转为 Data

```swift
guard let encodeData =  try? JSONSerialization.data(withJSONObject: dict) else { return }

```

总结:对象，字典（数组），字符串之间的转换依赖于 Data，通过一次转为 data 就可以转为想要的类型