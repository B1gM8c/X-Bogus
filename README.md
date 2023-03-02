# 抖音X-Bogus本地接口

### 启动web服务

你可以通过下面的方式启动服务

```
python3 server.py
```

### 接口调用方法

通过POST请求`/X-Bogus`，请求体需要使用JSON格式，内容如下

```
{
    "url":"https://www.douyin.com/aweme/v1/web/aweme/detail/?aweme_id=7190049956269444386&aid=1128&version_name=23.5.0&device_platform=android&os_version=2333",
    "user_agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36"
}
```

便可以获得如下结果

```
{
    "X-Bogus": "DFSzswSLfwUANnEftawINt9WcBj3",
    "param": "https://www.douyin.com/aweme/v1/web/aweme/detail/?aweme_id=7190049956269444386&aid=1128&version_name=23.5.0&device_platform=android&os_version=2333&X-Bogus=DFSzswSLfwUANnEftawINt9WcBj3"
}
```

当然，你还可以自己做个代理绑定域名



### DEMO

```
https://tiktok.iculture.cc/X-Bogus
```





## 去水印接口分析

如果你需要用于抖音视频去水印，则可以直接提取上述的`param`部分作为请求的URL，使用GET请求访问，但是你会发现可能仍然请求不到任何内容。

因此，记得请求头中需要携带以下内容

```
User-Agent:Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36
Referer:https://www.douyin.com/
Cookie:msToken=uTa38b9QFHB6JtEDzH9S4np17qxpG6OrROHQ8at2cBpoKfUb0UWmTkjCSpf72EcUrJgWTIoN6UgAv5BTXtCbOAhJcIRKyZIT7TMYapeOSpf;odin_tt=324fb4ea4a89c0c05827e18a1ed9cf9bf8a17f7705fcc793fec935b637867e2a5a9b8168c885554d029919117a18ba69; ttwid=1%7CWBuxH_bhbuTENNtACXoesI5QHV2Dt9-vkMGVHSRRbgY%7C1677118712%7C1d87ba1ea2cdf05d80204aea2e1036451dae638e7765b8a4d59d87fa05dd39ff; bd_ticket_guard_client_data=eyJiZC10aWNrZXQtZ3VhcmQtdmVyc2lvbiI6MiwiYmQtdGlja2V0LWd1YXJkLWNsaWVudC1jc3IiOiItLS0tLUJFR0lOIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLVxyXG5NSUlCRFRDQnRRSUJBREFuTVFzd0NRWURWUVFHRXdKRFRqRVlNQllHQTFVRUF3d1BZbVJmZEdsamEyVjBYMmQxXHJcbllYSmtNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUVKUDZzbjNLRlFBNUROSEcyK2F4bXAwNG5cclxud1hBSTZDU1IyZW1sVUE5QTZ4aGQzbVlPUlI4NVRLZ2tXd1FJSmp3Nyszdnc0Z2NNRG5iOTRoS3MvSjFJc3FBc1xyXG5NQ29HQ1NxR1NJYjNEUUVKRGpFZE1Cc3dHUVlEVlIwUkJCSXdFSUlPZDNkM0xtUnZkWGxwYmk1amIyMHdDZ1lJXHJcbktvWkl6ajBFQXdJRFJ3QXdSQUlnVmJkWTI0c0RYS0c0S2h3WlBmOHpxVDRBU0ROamNUb2FFRi9MQnd2QS8xSUNcclxuSURiVmZCUk1PQVB5cWJkcytld1QwSDZqdDg1czZZTVNVZEo5Z2dmOWlmeTBcclxuLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tXHJcbiJ9
```

### msToken生成

这里Cookie指的一提的是`msToken`可以是随机107位大小写英文字母、数字生成，以下是一个简单的样例

```
    def generate_random_str(self, randomlength=107):
        """
        根据传入长度产生随机字符串
        """
        random_str = ''
        base_str = 'ABCDEFGHIGKLMNOPQRSTUVWXYZabcdefghigklmnopqrstuvwxyz0123456789='
        length = len(base_str) - 1
        for _ in range(randomlength):
            random_str += base_str[random.randint(0, length)]
        return random_str
```

### ttwid生成

当然，部分网友也分析了`ttwid`的生成方式，你可以通过POST请求`https://ttwid.bytedance.com/ttwid/union/register/`，请求体使用JSON格式

```
{"region":"cn","aid":1768,"needFid":false,"service":"www.ixigua.com","migrate_info":{"ticket":"","source":"node"},"cbUrlProtocol":"https","union":true}
```

然后在响应头的`Set-Cookie`中，你可以获得`ttwid`

```
ttwid=1%7C7ZLJzwjjEw7NLeADTpVd-3eId-ZEIg0jpCEzTV9p_2A%7C1677681848%7C4ff4f97328ddc18b6d46c259bc26a05d2e654b50e3f21b27b8f9e9e8f9fcec82; Path=/; Domain=bytedance.com; Max-Age=31536000; HttpOnly; Secure
```

只需要稍加提取就可以使用了

```
ttwid=1%7C7ZLJzwjjEw7NLeADTpVd-3eId-ZEIg0jpCEzTV9p_2A%7C1677681848%7C4ff4f97328ddc18b6d46c259bc26a05d2e654b50e3f21b27b8f9e9e8f9fcec82;
```

