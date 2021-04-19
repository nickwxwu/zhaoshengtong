[TOC]

## 招生通-腾讯云API网关接入说明

### 环境定义
测试环境：  https://service-2iy312rw-1258344699.gz.apigw.tencentcs.com/test

预发布环境: https://service-6ucx1e74-1258344699.gz.apigw.tencentcs.com/prepub

正式环境：  https://service-miudptpc-1258344699.gz.apigw.tencentcs.com/release

## 调用方式
招生通的以下API以Https的方式对外提供服务。对这些API的请求都要通过密钥进行签名。
签名所需的密钥可以找相关的技术人员提供。

## 密钥内容
【secret_id】示例：`AKIDCg*****j548pN`  用于标识所使用的哪个密钥，并参与签名计算，传输过程中体现。

【secret_key】示例：`ZxF2wh*****N2oPrC`  用于签名计算，传递过程中无体现。

## 调用方法

[常用语言的签名 Demo>>](https://github.com/apigateway-demo)

招生通的服务端API仅支持Http POST方式，如果demo中的演示代码是GET方法，请自行修改为POST方法。

如果所用语言未在Demo中覆盖，可参考[API签名文档](https://cloud.tencent.com/document/product/628/11819)自行实现。

## 接口使用流程
![Image text](https://main.qcloudimg.com/raw/5c4943d3e5680fa0b0b42b8add88eae1.png)

## 接口说明
### 在请求url中需要带上的参数有（鉴权checktoken接口除外）：
| 公共参数 | 类型 | 参数要求 | 描述 |
| --- | --- | --- | --- |
|SdkAppID|int|必填|业务id|
|AppID|int|必填|学校id|
|RoleSpace|string|必填|角色空间|
|uin|string|必填|用户uin(唯一账号标识)|
|username|string|非必填|用户的姓名(如果checktoken接口，没有返回 username，则不填；如果checktoken接口返回了 username，则必须填上)|

### 1.鉴权

如果http 回包返回401，则表示Token 非法或者失效，根据场景，选择弹出登录页或者异常提醒

|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/account/checktoken|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "Token":"xxx"     // 用户token，必填
}
```

ResponseBody:

请求成功:
```json
{
    "Response": {
        "Uin": "999",                         //用户唯一id
        "SdkAppID": "user",                   //用户sdkappid
        "AppID": "11",                        //学校id
        "RoleSpace": "admin",                 //用户角色空间
        "Session":{
          "uin": "999",                       //用户唯一id
          "username": "nick"                  //用户名
        },
        "RequestId": ""
    }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
            "Code": "ParamInvalid",
            "Message": "table not exist"
        },
        "RequestId": ""
    }
}
```

### 2.机器客服 
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/qidian/robot-query?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx&username=xxx|
|header|Content-Type:application/json|

Req :

```json
{
"question":"我要招生", //查询的问题，必填
"from_user":"test"      //用户名或ID，非必填
}
```

ResponseBody:

请求成功:
```json
{
   "Response":{
      "data":{
         "result":[
            {
               "type":1,
               "qaid":3,
               "question":"我要招生",
               "answer":"好的，干了这一杯再说"
            }
         ]
      },
      "RequestId":""
   }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
            "Code": "ParamInvalid",
            "Message": "table not exist"
        },
        "RequestId": ""
    }
}
```
### 3.拉取联系我列表
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/qywx/get-contact-way-list?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx&username=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "Offset":0,  // 偏移量，非必填
  "Limit":20, //都不填，则返回全量；非必填
  "Flag":1 // 1--企业微信小程序按钮；0--所有；非必填
}
```



ResponseBody: 

请求成功:
```json
{
  "Response": {
    "contact_way_list": [
      {
        "config_id":"42b34949e138eb6e027c123cba77fad7",
        "type":1,
        "scene":1,
        "style":2,
        "remark":"test remark",
        "skip_verify":true,
        "state": {
            "ID": "10001",
            "Name": "东东",
            "Owner": "dd",
            "Phone": "1221312312321"
        },
        "qr_code":"http://p.qpic.cn/wwhead/duc2TvpEgSdicZ9RrdUtBkv2UiaA/0",
        "user":[{
           "Name": "xxxx", // 姓名
           "UserID": "xxxx", // 企业微信userid
        }]
      }
    ]
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
            "Code": "ParamInvalid",
            "Message": "table not exist"
        },
        "RequestId": ""
    }
}
```

### 图文内容

### 1.添加图文

|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/material/add?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx&username=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "Title":"图文标题",           // 必填
  "Author":"我是作者",          // 非必填
  "Remark":"我是摘要",          // 非必填
  "ImageURL":"上传图片地址",    // 非必填
  "Content":"我是正文"          // 必填
}
```



ResponseBody:

请求成功:
```json
{
  "Response": {
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
            "Code": "ParamInvalid",
            "Message": "table not exist"
        },
        "RequestId": ""
    }
}
```
### 2.编辑图文
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/material/edit?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx&username=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "Id":"图文id",      // 必填
  "Title":"图文标题", // 必填
  "Author":"我是作者",// 非必填
  "Remark":"我是摘要",// 非必填
  "ImageURL":"上传图片地址", // 非必填
  "Content":"我是正文" // 必填
}
```

ResponseBody:

请求成功:
```json
{
  "Response": {
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
            "Code": "ParamInvalid",
            "Message": "table not exist"
        },
        "RequestId": ""
    }
}
```
### 3.删除图文
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/material/delete?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx&username=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "IdSet":["图文id"]    // 必填
}
```


ResponseBody:

请求成功:
```json
{
  "Response": {
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
            "Code": "ParamInvalid",
            "Message": "table not exist"
        },
        "RequestId": ""
    }
}
```

### 4.拉取图文列表
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/material/list?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx&username=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "IdSet":["图文id"],   // 非必填
  "Offset":0,           // 非必填
  "Limit":15            // 非必填
}
```

ResponseBody:

请求成功:
```json
{
  "Response": {
      "DataSet":[{
          "Id":"图文id",
          "Title":"图文标题",
          "Author":"我是作者",
          "Remark":"我是摘要",
          "ImageURL":"封面图片地址",
          "Content":"我是正文",
          "CreateTime":1565177822
      }]
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
            "Code": "ParamInvalid",
            "Message": "table not exist"
        },
        "RequestId": ""
    }
}
```
### 图片库
#### 1 拉取图片库图片列表

|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/picture-library/list?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "Offset":0,  // 偏移量，非必填
  "Limit":20, //都不填，则返回全量；非必填
}
```
ResponseBody:

请求成功:
```json
{
   "Response": {
      "dataList": [{ //图片列表
         "ID": 10001,//主键ID
         "CreateTime": 1597048181,//创建时间
         "UpdateTime": 1597048181,//修改时间
         "Name": "timg (1)",//图片名称
         "CosUrl": "https://zst-enroll-class-dev-1258344699.cos.ap-guangzhou.myqcloud.com/enroll-class%2F11133%2F20200810_170949_1597050550355.jpeg",//cos图片url
         "Url": "https://p.qpic.cn/qidian_pic/2885785003/202008076ef7ad896694264d252e88209661c443/0",//企点图片url
         "MediaId": "0fc43bdcbac9f8dff8a2ffd92426663d" //图片在企点中的id
      }],
      "TotalCount":18,//总条数
      "TraceId": "30f0a41bb133ede6",
      "RequestId": "30f0a41bb133ede6"
   }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
  "Response": {
    "Error": {
      "Code": "FailedOperation",
      "Message": "failed operation"
    },
    "RequestId": ""
  }
}
```

### 数据上报
#### 1 数据上报接口

|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/datareport/add?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
   "Date":"20200816",  //上报数据日期 "YYYYmmdd"格式
   "Tag":"test",       //上报数据tag
   "Period":"daily",   //上报数据统计周期，值为"daily","week","month"其中之一
   "Data":{            //上报数据，自定义json格式
      "data1":1,
      "data2":1
   }
}
```
ResponseBody:

请求成功:
```json
{
   "Response": {
   }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
  "Response": {
    "Error": {
      "Code": "FailedOperation",
      "Message": "failed operation"
    },
    "RequestId": ""
  }
}
```
### 视频专辑
#### 1 视频专辑列表

|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/album/list?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "Offset":0,
  "Limit":20 // 默认20
}
```
ResponseBody:

请求成功:
```json
{
    "Response": {
        "Total": 1,
        "AlSet": [
            {
                "Id": "933c1c7d-e02e-430d-a3c7-2112d7c2feeb",
                "Name": "专辑测试",
                "AlNum": 2,  //专辑内视频个数
                "UpdateAt": 1602730589,
                "ShareUrl": "https://zstdev.qq.com/qiwei/live.html?SchoolID=11133&ClassID=05335d95-be12-4ec6-98e2-0d472cd77762",  //专辑H5分享链接
                "QRUrl": "https://zst-enroll-class-dev-1258344699.cos.ap-guangzhou.myqcloud.com/enroll-class%2F11133%2Fqr_11133_05335d95-be12-4ec6-98e2-0d472cd77762.jpg?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDPfkrqFrZRBl8JPNnFVdaEi5HKBpL5sPl%26q-sign-time%3D1598338060%3B1598338420%26q-key-time%3D1598338060%3B1598338420%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D38b7f551de5f16cc0a9089c61745eca1cae1a859",  //专辑小程序分享二维码
                "MiniAppId": "wx56f35d0bd34f7d67",  //小程序APPID
                "MiniUrl": "pages/album/album?id=60887539-41ec-4e7d-a564-f79b0f83dce8", //专辑小程序路径
            }
        ],
        "TraceId": "4c0b97a6a715ef4",
        "RequestId": "4c0b97a6a715ef4"
    }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
  "Response": {
    "Error": {
      "Code": "FailedOperation",
      "Message": "failed operation"
    },
    "RequestId": ""
  }
}
```

#### 2 视频专辑详情

|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/album/info?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 

```json
{
  "Id": "933c1c7d-e02e-430d-a3c7-2112d7c2feeb" //专辑ID
}
```
ResponseBody:

请求成功:
```json
{
    "Response": {
        "Id": "933c1c7d-e02e-430d-a3c7-2112d7c2feeb",
        "Name": "专辑测试",
        "Intro": "专辑简介",
        "Cover": "封面",
        "Albums": [
            "933c1c7d-e02e-430d-a3c7-2112d7c2feeb"
        ],
        "SchoolName": "开发大学1",
        "SchoolLogo": "",  //学校LOGO地址
        "ShareUrl": "https://zstdev.qq.com/qiwei/live.html?SchoolID=11133&ClassID=05335d95-be12-4ec6-98e2-0d472cd77762",  //专辑H5分享链接
        "QRUrl": "https://zst-enroll-class-dev-1258344699.cos.ap-guangzhou.myqcloud.com/enroll-class%2F11133%2Fqr_11133_05335d95-be12-4ec6-98e2-0d472cd77762.jpg?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDPfkrqFrZRBl8JPNnFVdaEi5HKBpL5sPl%26q-sign-time%3D1598338060%3B1598338420%26q-key-time%3D1598338060%3B1598338420%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3D38b7f551de5f16cc0a9089c61745eca1cae1a859", //专辑小程序分享二维码
        "MiniAppId": "wx56f35d0bd34f7d67", //小程序APPID
        "MiniUrl": "pages/album/album?id=60887539-41ec-4e7d-a564-f79b0f83dce8", //专辑小程序路径
        "EnrollSet": [
            {
                "ClassID": "df7d20cf-0851-460f-856e-ab46a4b8b854",
                "SchoolID": "11133",
                "SchoolLogo": "http://", // 学校logo地址
                "ClassName": "test",  //课名
                "StartTime": 1597299600,  //开课时间
                "EndTime": 1597299900, //结课时间
                "ClassIntro": "test", //课程介绍
                "ImageURL": "https://zst-enroll-class-dev-1258344699.cos.ap-guangzhou.myqcloud.com/enroll-class%2F11133%2F20200813_141237_1597299115266.jpeg",
                "TeacherID": "adairyan",
                "TeacherName": "adairyan", //宣讲人名称
                "TeacherTitle": "test", //宣讲人职称
                "ContactTeacherVisible": true,
                "ContactTeacherName": "",
                "ContactTeacherOpenID": "51B2252AA5E3ACEBE938DA97A337919B",
                "ContactTeacherQYWXID": "nickxuewu",
                "ContaceQQ": "",
                "ContactQYWX": "https://wework.qpic.cn/wwpic/788655_ad06b3FFSamVp1__1596188592/0",
                "ContactMiniProgram": "",
                "ShareURL": "https://zstdev.qq.com/qiwei/live.html?SchoolID=11133\u0026ClassID=df7d20cf-0851-460f-856e-ab46a4b8b854", //H5课堂链接
                "MiniProgram": "https://zst-enroll-class-dev-1258344699.cos.ap-guangzhou.myqcloud.com/enroll-class%2F11133%2Fqr_11133_df7d20cf-0851-460f-856e-ab46a4b8b854.jpg?sign=q-sign-algorithm%3Dsha1%26q-ak%3DAKIDPfkrqFrZRBl8JPNnFVdaEi5HKBpL5sPl%26q-sign-time%3D1597299199%3B1597299559%26q-key-time%3D1597299199%3B1597299559%26q-header-list%3D%26q-url-param-list%3D%26q-signature%3Dac8ada4bd53985bb63f0faefdb36c0a0b7db8cd1",  //小程序地址二维码
                "StreamId": 100001, //导播台ID
                "StreamName": "导播台名称",
                "StreamURL": "导播台地址",        
                "LiveID": "1000237952",
                "PlayBackFlag": 0,  //是否支持回放标识，0-不支持，1-支持
                "PlayBackUrl": "",  // 回放地址
                "PlayBackMsg": "",  // 回放录制结果信息
                "Flag": 0,
                "PushRemindFlag": 1,
                "LiveURL": "rtmp://29734.liveplay.myqcloud.com/live/1400313729_1000237952_adairyan_aux.flv?txSecret=b62b6d267e1995260bd45158e1f54c6f\u0026txTime=5F34DCBC", // 直播地址
                "State": 0  // 直播状态，-1-直播已结束 0-未开始 1-直播中 2-直播延迟
            }
        ],
        "TraceId": "3f18f06a8fa0694c",
        "RequestId": "3f18f06a8fa0694c"
    }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
  "Response": {
    "Error": {
      "Code": "FailedOperation",
      "Message": "failed operation"
    },
    "RequestId": ""
  }
}
```

### 学校信息
#### 1 相关购买套餐 & 到期时间
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/org/info?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 
```json
{
    
}
```

ResponseBody:

请求成功:
```json
{
  "Response": {
    "OrganizationId": "11133",
    "OrganizationName": "开发大学1",
    "Product": {
      "QYWeChatNum": 301, // 企业微信账号数  大于0表示有企业微信
      "QDAccountNum": 25, // 企点账号数 大于0表示有企点账号
      "QDGroupNum": 0,  // 企点2000人群数
      "MiniProgram": 3, // 按照二进制位表示，返回数据二进制的十进制数据 基础版（北明）-- 0x1 标准版 -- 0x2  基础版（联奕） -- 0x4  专业版 -- 0x8
      "LiveStream": 11, // 直播 大于0表示有直播
      "RobotBase": 50000, // 机器人基础包  大于0表示有机器人
      "Robot": 120000  // 机器人增值包
    },
    "ValidBegin": 1596038400000,  // 开始时间
    "ValidEnd": 1690646400000,  // 结束时间
    "TraceId": "3a1a2d2407100fd7",
    "RequestId": "3a1a2d2407100fd7"
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
          "Code": "FailedOperation",
          "Message": "failed operation"
        },
        "RequestId": ""
    }
}
```

### 单轮问题库JSON批量导出
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/qidian/robot-query/export?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 
```json
{
    "keyword": "关键词",  // 选填
    "FStatus": "0"  // 查询问题的状态：0 草稿；10 停用；11 启用；12 失效 // 选填
}
```

ResponseBody:

请求成功:
```json
{
    "Response": {
        "data": [
            {
                "stdQId": "10004",
                "stdQues": "上海有什么机场123？",
                "answer": "上海有虹桥国际机场和浦东国际机场",
                "status": 1,
                "validity": 1,
                "startDate": "",
                "endDate": "",
                "classpath": "未分类",
                "similarQues": [
                    "上海有哪些机场123",
                    "上海的机场是什么123"
                ],
                "relatedQId": [],
                "relatedQues": []
            },
            {
                "stdQId": "",
                "stdQues": "学校名称叫什么",
                "answer": "小桔大数据",
                "status": 0,
                "validity": 1,
                "startDate": "",
                "endDate": "",
                "classpath": "未分类",
                "similarQues": [],
                "relatedQId": [],
                "relatedQues": []
            }
        ],
        "RequestId": "4d657b26a49cd483"
    }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
  "Response": {
    "Error": {
      "Code": "FailedOperation",
      "Message": "failed operation"
    },
    "RequestId": ""
  }
}
```

### 智招接口
#### 1 查询当前学校的日报列表
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/daily-paper/describe-school-topic?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 
```json
{
   "PStatus": -1,// 上架状态， 1-上架，0-下架，-1-全部
   "Fuzzy": "开发", // 标题和简介的模糊查询数据
   "Limit": 10,
   "Offset":0
}
```

ResponseBody:

请求成功:
```json
{
    "Response": {
        "TotalCount": 13,
        "Topics": [ // 主题列表
            {
                "ID": 10000, //主题ID
                "Name": "2020.1.11", // 主题名称
                "Title": "biaoti",// 主题标题
                "Desc": "描述", //描述
                "LimitType": "1",// 院校限制类型
                "Img": "iimage",// 图片
                "PStatus": 1, // 上架状态 1-上架，0-下架
                "Ctime": 1606725737, // 更新时间
                "Mtime": 1606725737, // 创建时间
                "CreateId": "kevenzchen", //创建人
                "ModifyId": "", //修改人
                "ArticleCount": 2 //资讯文章数
            },
        ]
    }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
          "Code": "FailedOperation",
          "Message": "failed operation"
        },
        "RequestId": ""
    }
}
```

#### 2 查询当前学校的日报的学校资讯列表
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/daily-paper/describe-school-article?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 
```json
{
    "TopicID":10000 // 日报ID
}
```

ResponseBody:

请求成功:
```json
{
    "Response": {
        "TotalPage": 0,
        "TotalCount": 3,
        "Articles": [
            {
                "ID": 10002, //资讯ID
               "TopicID": 10000, // 主题ID
               "Sort": 1, // 排序值
                "CType": "1",// 类型 1-素材图文，2-微信图文，3-直播
                "CValue": "f4597042-6fc1-4da5-878b-cbfda078758c",// 对应图文的ID、微信的链接、直播的ID
                "Tag": "", // tag暂不适用
                "Ctime": 1606810478, // 创建时间
                "Mtime": 1606810478, // 修改时间
                "CreateId": "kevenzchen", // 创建人
                "ModifyId": ""  // 修改人
                "SchoolID": "11133",
                "Video": null, // 直播信息
                "PictureLib": { // 图文信息
                    "Title": "ad测试" // 图文标题
                },
                "WxArticle": null
            },
            {
                "ID": 10003,
                "TopicID": 10000,
                "Sort": 2,
                "CType": "2",
                "CValue": "https://mp.weixin.qq.com/s?__biz=MzA3NDcyMTQyNQ==&mid=2649265641&idx=1&sn=22a514967de1ec35a3317be6b367df4a&chksm=87674417b010cd015cd1a97a67e1d7eb732e4013a5c6c1adba5efdada2c85b0cbc30e1bc18a2&xtrack=1&scene=90&subscene=93&sessionid=1606961496&clicktime=1606961498&enterid=1606961498&ascene=56&devicetype=android-29&version=2700143f&nettype=WIFI&abtest_cookie=AAACAA%3D%3D&lang=zh_CN&exportkey=AdHmfzgbx9776MoJaQpHek4%3D&pass_ticket=Lb3N2nlGZ5R7ih7LPgOLX0lw9ySJpS8qO%2BBFpYfVsPc2lkR3wZyy4NfxxO7OR%2BRe&wx_header=1",
                "Tag": "-1",
                "Ctime": 1606811115,
                "Mtime": 1606966189,
                "CreateId": "kevenzchen",
                "ModifyId": "",
                "SchoolID": "11133",
                "Video": null,
                "PictureLib": null,
                "WxArticle": { // 微信文章信息
                    "Url": "https://mp.weixin.qq.com/s?__biz=MzA3NDcyMTQyNQ==&mid=2649265641&idx=1&sn=22a514967de1ec35a3317be6b367df4a&chksm=87674417b010cd015cd1a97a67e1d7eb732e4013a5c6c1adba5efdada2c85b0cbc30e1bc18a2&xtrack=1&scene=90&subscene=93&sessionid=1606961496&clicktime=1606961498&enterid=1606961498&ascene=56&devicetype=android-29&version=2700143f&nettype=WIFI&abtest_cookie=AAACAA%3D%3D&lang=zh_CN&exportkey=AdHmfzgbx9776MoJaQpHek4%3D&pass_ticket=Lb3N2nlGZ5R7ih7LPgOLX0lw9ySJpS8qO%2BBFpYfVsPc2lkR3wZyy4NfxxO7OR%2BRe&wx_header=1",//微信链接
                    "ImageURL": "https://mmbiz.qpic.cn/mmbiz_png/YE1dmj1Pw7kAthIDChZZXXj1n7WJQZ409ichZdUgdYOZwh18uNQ3Potolv2jv6PhcGwleVI9BFwRGEs9Thplurw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1",//图片
                    "Title": "testTitle",//标题
                    "Desc": "testDesc" // 简介
                }
            },
            {
                "ID": 10004,
                "TopicID": 10000,
                "Sort": 2,
                "CType": "3",
                "CValue": "e73b2907-73d4-4531-9451-e1114f0a5eea",
                "Tag": "-1",
                "Ctime": 1606965879,
                "Mtime": 1606966189,
                "CreateId": "kevenzchen",
                "ModifyId": "",
                "SchoolID": "11133",
                "Video": {// 视频信息
                    "ImageURL": "https://zst-enroll-class-dev-1258344699.cos.ap-guangzhou.myqcloud.com/enroll-class%2F11133%2F20201116_151055_1605510655418.jpeg",//图片
                    "ClassName": "宣讲来啦",//视频名称
                    "ClassIntro": "嘻嘻嘻嘻嘻",// 简介
                    "TeacherName": "kevenzchen",// 老师
                    "TeacherID": "kevenzchen",// 老师ID
                    "StartTime": 1605510900, // 开播时间
                    "EndTime": 1605510900, // 直播结束时间
                    "RealStartTime": 0 // 真实开播时间
                },
                "PictureLib": null,
                "WxArticle": null
            }
        ],
        "TraceId": "32ecb8d637f36c29",
        "RequestId": "32ecb8d637f36c29"
    }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
          "Code": "FailedOperation",
          "Message": "failed operation"
        },
        "RequestId": ""
    }
}
```

### 接待接口
#### 1 获取加入的小组信息
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/member/describe-related-team?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 
```json
{
  "UinSet": [
    "uin1",
    "uin2"
  ]
}
```

ResponseBody:

请求成功:
```json
{
  "Response": {
    "ResultSet": [
       {
          "Uin": "uin1",
          "RelatedTeamSet": [
            {
              "TeamID": "1000",
              "TeamName": "汕头招生组"
            }
          ]
       },
       {
          "Uin": "uin2",
          "RelatedTeamSet": [
            {
              "TeamID": "1000",
              "TeamName": "汕头招生组"
            }
          ]
       }
    ],
    "TraceId": "688e1c8e2cc7a98b",
    "RequestId": "688e1c8e2cc7a98b"
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
          "Code": "FailedOperation",
          "Message": "failed operation"
        },
        "RequestId": ""
    }
}
```

#### 2 获取小组信息
|请求基本信息|描述|
|-------------|-------------|
|方法|POST|
|请求URL| /v1/open/platform/member/describe-teams?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|

RequestBody: 
```json
{
  "Filters": [{
        "Name": "TeamID",
        "Values": ["1000"],
     }
  ],
  "Offset": 0,
  "Limit": 15
}
```

ResponseBody:

请求成功:
```json
{
  "Response": {
    "Count": 10,
    "TeamSet": [
      {
        "TeamID": "1000",
        "TeamName": "汕头招生组",
        "MemberCount": 2,
        "QRCode": "xxxx",
        "GroupWpaID": 617,
        "GroupQr": "https://p.qpic.cn/qidian_pic/0/20210112c7467f45dd4e917b5715f6c9c70ac4fd/0",  //QQ群接待二维码
        "GroupUrl": "https://wpa1.qq.com/SqXP909E?_type=wpa&qidian=true",  //QQ群接待url
        "GroupName": "测试接待",  //QQ群名称
        "GroupNumber": "589066866",  // QQ群号
        "GroupFace": "http://p.qlogo.cn/gh/589066866/589066866/",
        "CreateTime": 1233333333333,
        "ModifyTime": 1233333333333,
        "MemberSet": [
          {
            "Uin": "uin1",
            "UserID": "UserID1"
          },
          {
            "Uin": "uin2",
            "UserID": "UserID2"
          }
        ],
        "TotalCustomer": 100
      }
    ],
    "TraceId": "688e1c8e2cc7a98b",
    "RequestId": "688e1c8e2cc7a98b"
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
          "Code": "FailedOperation",
          "Message": "failed operation"
        },
        "RequestId": ""
    }
}
```

#### 3 下载联系二维码
|请求基本信息|描述|
|-------------|-------------|
|方法|GET|
|请求URL| /v1/open/platform/qywx/get-qr-code?config_id=42b34949e138eb6e027c123cba77fad7&SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/octet-stream|


ResponseBody:
二维码图片内容


#### 4 搜索联系我
|请求基本信息|描述|
|-------------|-------------|
|方法|GET|
|请求URL| /v1/open/platform/qywx/search-contact-way?SdkAppID=xxxx&AppID=xxx&RoleSpace=xxx&uin=xxx|
|header|Content-Type:application/json|


RequestBody: 
```json
{
  "UserID":"nick",
  "Flag":1 // 1--企业微信小程序按钮；0--所有
}
```

ResponseBody:

请求成功:
```json
{
  "Response": {
    "contact_way_list": [
      {
        "contact_name":"组件名称", // 组件名称
        "config_id":"42b34949e138eb6e027c123cba77fad7",
        "type":1,
        "scene":1,
        "style":2,
        "remark":"test remark",
        "skip_verify":true,
        "state": {
            "ID": "10001",
            "Name": "东东",
            "Owner": "dd",
            "Phone": "1221312312321"
        },
        "qr_code":"http://p.qpic.cn/wwhead/duc2TvpEgSdicZ9RrdUtBkv2UiaA/0",
        "user":[{
           "Name": "xxxx", // 姓名
           "UserID": "xxxx", // 企业微信userid
        }]
     }
    ]
  }
}
```
请求失败(判断是否有 "Error" 字段) 示例：
```json
{
    "Response": {
        "Error": {
          "Code": "FailedOperation",
          "Message": "failed operation"
        },
        "RequestId": ""
    }
}
```