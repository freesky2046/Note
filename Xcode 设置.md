## 支持HTTP 

源代码打开 info.plist，添加下面内容。

```json
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads</key>
        <true/>
    </dict>
```

注意添加位置是最外层<dict> 下的item

![xcode_1.png](/Users/mingzhou/Desktop/Note/Image/xcode_1.png)