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

<img src="/Users/mingzhou/Desktop/Note/Image/xcode_1.png" alt="xcode_1.png" style="zoom:50%;" />



## Build System:[Multiple commands produce](https://stackoverflow.com/questions/50718018/xcode-10-error-multiple-commands-produce)

[https://stackoverflow.com/questions/50718018/xcode-10-error-multiple-commands-produce](https://stackoverflow.com/questions/50718018/xcode-10-error-multiple-commands-produce)