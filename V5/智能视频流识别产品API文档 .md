# 数美智能视频流识别产品API文档


## 视频流上传请求

### 接口描述

该接口用于提交视频流鉴定等相关信息，稳定拉流后将持续回调对应的识别结果至指定的callback地址。

### 请求URL：
| 集群 | URL | 支持产品列表 |
| --- | --- | --- |
| 上海 | `http://api-videostream-sh.fengkongcloud.com/videostream/v5` | 中文视频流 |
| 新加坡 | `http://api-videostream-xjp.fengkongcloud.com/videostream/v5` | 中文视频流<br/>英语视频流<br/>阿语视频流 |
| 硅谷 | `http://api-videostream-gg.fengkongcloud.com/videostream/v5` | 中文视频流<br/>英语视频流<br/>阿语视频流 |

### 请求方法：

`POST`

### 支持协议

`HTTP`或`HTTPS`

### 字符编码：

`UTF-8`

### 建议超时时间：

5s

### 请求参数：

放在HTTP Body中，采用Json格式，具体参数如下：

| **请求参数名** | **类型** | **参数说明** | **传入说明** | **规范** |
| --- | --- | --- | --- | --- |
| accessKey | string | 公司密钥 | 必传参数 | 数美分配 |
| appId | string | 应用标识 | 必传参数 | 需要联系数美服务开通，请使用数美单独提供的传值为准 |
| eventId | string | 事件标识 | 必传参数 |  用于区分场景数据，需要联系数美服务开通，请使用数美单独提供的传值为准 |
| imgType | string | 视频中的画面需要识别的监管类型，**和imgBusinessType至少传一个** | 非必传参数 | 监管一级标签<br/>可选值：<br/>`POLITY`：涉政识别<br/>`EROTIC`：色情&性感违规识别<br/>`VIOLENT`：暴恐&违禁识别<br/>`QRCODE`：二维码识别<br/>`ADVERT`：广告识别<br/>`IMGTEXTRISK`：图片文字违规识别<br/>如果需要识别多个功能，通过下划线连接，如`POLITY_QRCODE_ADVERT`用于涉政、二维码和广告组合识别 |
| audioType | string | 视频流中的音频需要识别的监管类型，**和audioBusinessType至少传一个** | 非必传参数 | 监管一级标签<br/>可选值：<br/>`POLITICAL`：涉政识别<br/>`PORN`：色情识别<br/>`AD`：广告识别<br/>`MOAN`：娇喘识别<br/>`AUDIOPOLITICAL`：声音涉政<br/>`ANTHEN`：国歌识别<br/>`ABUSE`: 辱骂识别<br/>`NONE`:不检测音频<br/>如需做组合识别，通过下划线连接即可，例如`POLITICAL_PORN_MOAN`用于广告、色情和涉政识别 |
| imgBusinessType | string | 视频中的画面需要识别的业务类型， **和imgType至少传一个** | 非必传参数 | 可选值参考[imgBusinessType可选值列表](#imgbusinesstype可选值列表)<br/>如果需要识别多个功能，通过下划线连接 |
| audioBusinessType | string | 视频流中的音频需要识别的业务类型，**和audioType至少传一个** | 非必传参数 | 业务一级标签<br/>可选值：<br/>`SING`：唱歌识别<br/>`LANGUAGE`：语种识别<br/>`MINOR`：未成年人识别<br/>`GENDER`：性别识别<br/>`TIMBRE`：音色识别，需要同时传入`GENDER`才能生效<br/>`APPNAME`：app名称识别 |
| callback | string | 视频回调地址 | 必传参数 | 将视频流中视频片段的检测结果通过该地址回调给用户 |
| data | json_object | 请求数据内容， | 必传参数 | 最长1MB，其中[data内容如下](#data) |

<span id="data">其中，data的内容如下：</span>

| **请求参数名** | **类型** | **参数说明** | **传入说明** | **规范** |
| --- | --- | --- | --- | --- |
| lang | string | 语种 | 必传参数 | 可选值如下：<br/>`zh`：中文<br/>`en`：英语<br/>`ar`：阿语<br/>默认值：`zh` |
| tokenId | string | 客户端用户账号唯一标识 | 必传参数 | 用于用户行为分析，建议传入用户UID； 最长40位 |
| streamType | string | 视频流类型 | 必传参数 | 可选值为：<br/>`NORMAL`：普通流地址，目前支持`rtmp`、`rtmps`、`hls`、`http`、`https`协议<br/>`AGORA`：声网审核<br/>`TRTC`:腾讯审核<br/>`ZEGO`：即构审核<br/>`VOLC`：火山引擎审核<br/>注意：使用RTC的SDK录制方案的时候，可能会在RTC侧产生额外的录制费用，具体费用请咨询相关RTC厂商 |
| url | string | 要检测的视频url地址 | 非必传参数 | 要检测的流地址url参数（当streamType为NORMAL时必传） |
| streamName | string | 视频流名称 | 非必传参数 | 用于后台界面展示，建议传入 |
| ip | string | 客户端IP | 非必传参数 | 该参数用于IP维度的用户行为分析，同时可用于比对数美IP黑库 |
| returnAllVideo | int | 用户可根据需求选择返回不同审核结果的视频片段**（此处返回的是顶层判断为非PASS的videodetail，里面仅需包含此视频片段的非pass截帧图片/音频）** | 非必传参数 | 可选值如下：（默认值为`0`）<br/>`0`：回调reject、review结果的视频片段审核信息<br/>`1`：回调所有结果的视频片段审核信息 |
| returnFinishInfo | int | 为1时，流结束时返回结束通知 | 非必传参数 | 可选值如下：（默认值为`0`）<br/>`1`:审核结束时发起结束通知<br/>`0`:审核结束时不发送结束通知 ，详细返回参数见[结束流返回参数](#审核结束回调参数（returnFinishInfo为1时返回）：) |
| detectFrequency | int | 截帧频率间隔 | 非必传参数 | 单位为秒，取值范围为1~60s，遇到小数向下取整，不足1的按照1S处理，如不传递默认3s截帧一次 |
| room | string | 直播间/游戏房间编号 | 非必传参数 | 可针对单个房间制定不同的策略； |
| extra | json_object | 扩展信息 | 非必传参数 | 详见[extra说明](#extra) |

<span id="extra">data 中，extra的内容如下</span>

| **请求参数名** | **类型** | **参数说明** | **传入说明** | **规范** |
| --- | --- | --- | --- | --- |
| passThrough | json_object | 透传字段 | 非必传参数 | 该字段内容会随着回调结果一起原样返回 |

### 返回参数

放在HTTP Body中，采用Json格式，具体参数如下：

| **返回结果参数名** | **参数类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| requestId | string | 本次请求的唯一标识 | 是 | 请求唯一标识 |
| code | int | 请求返回码 | 是 | 详见[接口响应码列表](#接口响应码列表) |
| message | string | 请求返回描述，和请求返回码对应 | 是 | 详见[接口响应码列表](#接口响应码列表) |
| detail | json_object | 描述详细信息 | 否 |  |

其中，detail结构如下：

| **参数名称** | **参数类型** | **参数说明**                                                 | **是否必返** | **规范**         |
| ------------ | ------------ | ------------------------------------------------------------ | ------------ | ---------------- |
| errorCode    | int          | 状态码                                                       | 否           | `1001`：重复推流 |
| dupRequestId | string       | 表示重复的requestId<br/>当errorCode为1001，表示重复推流时，会返回dupRequestId字段<br/>例如当第一次请求的时候没有收到返回，但该音频流实际已经开始审核了，没有requestId无法主动关闭审核<br/>可以再次请求，收到重复推流的信息，通过返回的dupRequestId调用关闭审核接口 | 否           |                  |

## 异步回调结果

### 接口描述

用户如果需要服务端主动对视频检测结果进行回调，则需要在请求参数中指定回调协议接口URL callback参数，服务端根据该参数在视频审核完成后，主动回调用户。

### 请求方法：

`POST`

### 字符编码：

`UTF-8`

### 建议超时时间：

5s

### 回调策略

当用户收到推送结果，并返回HTTP状态码为200时，表示推送成功；否则系统将进行最多20次推送。

### 回调参数

放在HTTP Body中，采用Json格式，具体参数如下：

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| requestId | string | 本次请求的唯一标识 | 是 | 请求唯一标识 |
| code | int | 请求返回码 | 是 | 详见[接口响应码列表](#接口响应码列表) |
| message | string | 请求返回描述，和请求返回码对应 | 是 | 详见[接口响应码列表](#接口响应码列表) |
| statCode | int | 回调状态码 | 否 |状态码对应关系：<br/>0 ：审核结果回调 <br/>1 ：流结束结果回调|
| riskLevel | string | 当前视频片段的处置建议 | 是 |当code等于1100时返回<br/>`PASS`：正常内容<br/>`REVIEW`：可疑内容 <br/>`REJECT`：违规内容|
| riskLabel1 | string | 各个一级标签之间是并列的关系，当riskLevel为`PASS`时返回`normal` | 是 |一级标签|
| riskLabel2 | string | 二级标签归属于一级标签，当riskLevel为`PASS`时为空 | 是 |二级标签|
| riskLabel3 | string | 三级标签归属于二级标签，当riskLevel为`PASS`时为空 | 是 |三级标签|
| videoDetail | json_object | 风险视频片段信息 | 否 | 有风险片段或returnAllvideo=1时返回，详见[videoDetail说明](#videoDetail) |
| tokenProfileLabels | json_array | 账号属性标签 | 否 | 仅在开启功能时返回，详见[tokenProfileLabels说明](#tokenProfileLabels) |
| tokenRiskLabels | json_array | 账号风险标签 | 否 | 仅在开启功能时返回，详见[tokenRiskLabels说明](#tokenRiskLabels) |
| auxInfo | json_object | 辅助信息 | 否 | 当code等于`1100`时返回，详见[auxInfo说明](#auxInfo) |

<span id="auxInfo">其中，auxInfo中的具体内容如下：</span>

| 参数名      | 类型        | 参数说明         | 是否必返 | 规范                                                         |
| :---------- | :---------- | :--------------- | :------- | :----------------------------------------------------------- |
| passThrough | json_object | 透传字段         | 否       | 该字段内容与请求参数data中extra的passThrough的值相同         |
| frameCount  | int         | 视频片段截帧数量 | 是       | 返回的视频截帧数量。returnAllvideo=0时为风险数量，returnAllvideo=1时为全部数量 |

<span id="videoDetail">其中，videoDetail中的具体内容如下：</span>

| 参数名      | 类型       | 参数说明                     | 是否必返 | 规范                                |
| :---------- | :--------- | :--------------------------- | :------- | :---------------------------------- |
| audioDetail | json_array | 音频片段信息                 | 是       |                                     |
| endTime     | float      | 视频片段结束时间（绝对时间） | 是       |                                     |
| frameDetail | json_array | 截帧图片信息                 | 是       | 详见[frameDetail说明](#frameDetail) |
| startTime   | float      | 视频片段开始时间（绝对时间） | 是       |                                     |
| videoUrl    | string     | 当前视频片段的URL            | 是       |                                     |

<span id="frameDetail">其中，frameDetail数组中每个成员的具体内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| time | float | 截帧在视频文件中的时间，单位为秒 | 是 | 截帧图片相对视频文件的时间 |
| requestId | string | 当前截帧片段的唯一标识 | 是 |  |
| imgUrl | string | 当前截帧的URL | 否 | |
| imgText | string | 截帧图片OCR文本内容 | 否 | 截帧图片OCR文字识别，仅传入ADVERT、IMGTEXTRISK时返回 |
| riskLevel | string | 当前截帧的处置建议 | 否 | `PASS`：正常内容<br/>`REVIEW`：可疑内容<br/>`REJECT`：违规内容 |
| riskLabel1 | string | 各个一级标签之间是并列的关系，当riskLevel为`PASS`时返回`normal` | 否 | 一级标签 |
| riskLabel2 | string | 二级标签归属于一级标签，当riskLevel为`PASS`时为空 | 否 | 二级标签 |
| riskLabel3 | string | 三级标签归属于二级标签，当riskLevel为`PASS`时为空 | 否 | 三级标签 |
| riskDescription | string | 标签解释 | 否 | 对于命中用户自定义名单时返回：`命中自定义名单`；当riskLevel为`PASS`时返回:`正常`；其他情况展现形式为一级标签：二级标签：三级标签的中文名 |
| riskDetail | json_object | 风险详情信息 | 否 | 详见[riskDetail说明](#riskDetail) |
| allLabels | json_array | 全部的风险标签列表 | 否 | 详见[allLabels说明](#allLabels) |
| businessLabels | json_array | 业务标签列表，传入imgBusinessType时返回 | 否 | 详见[businessLabels说明](#businessLabels) |

<span id="riskDetail">frameDetail中，riskDetail的内容如下：</span>

| **参数名** | **类型**    | **参数说明** | **是否必返** | **规范**                                                     |
| ---------- | ----------- | ------------ | ------------ | ------------------------------------------------------------ |
| riskSource | int         | 风险来源     | 是           | 可选值：<br/>`1000`：无风险<br/>`1001`：文本风险<br/>`1002`：视觉风险<br/>`1003`：音频风险 |
| faces      | json_array  | 人脸信息     | 否           | 返回图片中涉政人物的名称及位置信息，详见[faces说明](#faces)  |
| face_num   | int         | 人脸数量     | 否           |                                                              |
| objects    | json_array  | 物品信息     | 否           | 返回图片中标识或物品的名称及位置信息，详见[objects说明](#objects) |
| persons    | json_array  | 人像信息     | 否           | 仅当命中人像-多人时，数组元素会有多个，最多10个              |
| person_num | int         | 人像数量     | 否           |                                                              |
| ocrText    | json_object | 文字信息     | 否           | 返回图片中文字相关信息，详见[ocrText说明](#ocrText)          |

<span id="faces">riskDetail中，faces数组每个成员的具体内容如下：</span>

| **参数名**  | **类型**  | **参数说明** | **是否必返** | **规范**                                                     |
| ----------- | --------- | ------------ | ------------ | ------------------------------------------------------------ |
| id          | string    | 编号         | 是           |                                                              |
| name        | string    | 人名         | 否           |                                                              |
| location    | int_array | 位置坐标     | 否           | 该数组有四个值，分别代表左上角的坐标和右下角的坐标。例如[207,522,340,567]<br/>207代表的是左上角的x坐标<br/>522代表左上角的y坐标<br/>340代表的是右下角的x坐标<br/>567代表的是右下角的y坐标 |
| probability | float     | 置信度       | 否           | 可选值在0～1之间，值越大，可信度越高                         |
| face_ratio  | float     | 人脸占比     | 否           |                                                              |

<span id="objects">riskDetail中，objects数组每个成员的具体内容如下：</span>

| **参数名**  | **类型**  | **参数说明** | **是否必返** | **规范**                                                     |
| ----------- | --------- | ------------ | ------------ | ------------------------------------------------------------ |
| id          | string    | 编号         | 是           |                                                              |
| name        | string    | 名称         | 否           |                                                              |
| location    | int_array | 位置坐标     | 否           | 该数组有四个值，分别代表左上角的坐标和右下角的坐标。例如[207,522,340,567]<br/>207代表的是左上角的x坐标<br/>522代表左上角的y坐标<br/>340代表的是右下角的x坐标<br/>567代表的是右下角的y坐标 |
| probability | float     | 置信度       | 否           | 可选值在0～1之间，值越大，可信度越高                         |
| qrContent   | string    | 二维码信息   | 否           |                                                              |

riskDetail中，persons数组的每个元素的内容如下：

| **参数名**   | **类型**  | **参数说明** | **是否必返** | **规范**                                                     |
| ------------ | --------- | ------------ | ------------ | ------------------------------------------------------------ |
| id           | string    | 编号         | 是           |                                                              |
| location     | int_array | 位置坐标     | 否           | 该数组有四个值，分别代表左上角的坐标和右下角的坐标。例如[207,522,340,567]<br/>207代表的是左上角的x坐标<br/>522代表左上角的y坐标<br/>340代表的是右下角的x坐标<br/>567代表的是右下角的y坐标 |
| probability  | float     | 置信度       | 否           | 可选值在0～1之间，值越大，可信度越高                         |
| person_ratio | float     | 人像占比     | 否           |                                                              |

<span id="ocrText">riskDetail中，ocrText数组每个成员的具体内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| text | string | 图片中识别出的文字 | 是 | 识别出来所有文字内容 |
| matchedLists | json_array | 命中的客户自定义名单信息 | 否 | 仅在命中客户自定义名单时返回，详见[matchedLists说明](#matchedLists) |
| riskSegments | json_array | 高风险内容片段 | 否 | 在启用涉政、暴恐、违禁、广告等功能时存在，详见[riskSegments说明](#riskSegments) |

<span id="matchedLists">ocrText中，matchedLists内每个元素详细内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| name | string | 客户自定义名单名称 | 是 | |
| words | json_array | 命中的这个名单中的敏感词信息 | 是 | 下标从0开始计数，详见[words说明](#words) |

<span id="words">matchedLists中，words的每个元素详细内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| word | string | 敏感词 | 是 | |
| position | int_array | 敏感词所在位置 | 是 | 下标从0开始计数 |

<span id="riskSegments">ocrText中，riskSegments的每个元素详细内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| segment | string | 高风险内容片段 | 否 | |
| position | int_array | 高风险内容片段所在位置 | 否 | 下标从0开始计数 |

<span id="allLabels">frameDetail中，allLabels数组的每个成员的内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| riskLabel1 | string | 一级风险标签 | 是 | 一级风险标签 |
| riskLabel2 | string | 二级风险标签 | 是 | 二级风险标签 |
| riskLabel3 | string | 三级风险标签 | 是 | 三级风险标签 |
| riskDescription | string | 风险描述 | 是 | 格式为&quot;一级风险标签：二级风险标签：三级风险标签&quot;的中文名称<br/>对于命中用户自定义名单时返回：`命中自定义名单` |
| riskLevel | string | 处置建议 | 是 | `PASS`：正常内容<br/>`REVIEW`：可疑内容<br/>`REJECT`：违规内容 |
| probability | float | 置信度 | 是 | 可选值为0～1，值越大，可信度越高 |
| riskDetail | json_object | 风险详情 | 是 | 同frameDetail中的riskDetail结构一致 |

<span id="businessLabels">frameDetail中，businessLabels数组的每个成员的内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| businessLabel1 | string | 一级标签 | 是 | 一级标签 |
| businessLabel2 | string | 二级标签 | 是 | 二级标签 |
| businessLabel3 | string | 三级标签 | 是 | 三级标签 |
| businessDescription | string | 标签描述 | 是 | 格式为&quot;一级标签：二级标签：三级标签&quot;的中文名称 |
| confidenceLevel | int | 置信等级 | 是 | 可选值在0～2之间，值越大，可信度越高 |
| probability | float | 置信度 | 是 | 可选值为0~1，值越大，可信度越高 |
| businessDetail | Json_object | 详细信息 | 是 |  |

businessLabels中，businessDetail的内容如下：

| **参数名** | **类型**   | **参数说明** | **是否必返** | **规范**                                        |
| ---------- | ---------- | ------------ | ------------ | ----------------------------------------------- |
| faces      | json_array | 人脸信息     | 否           |                                                 |
| face_num   | int        | 人脸数量     | 否           |                                                 |
| objects    | json_array | 物品信息     | 否           |                                                 |
| persons    | Json_array | 人像信息     | 否           | 仅当命中人像-多人时，数组元素会有多个，最多10个 |
| person_num | int        | 人像数量     | 否           |                                                 |

businessDetail中，faces数组的每个元素的内容如下：

| **参数名**  | **类型**  | **参数说明** | **是否必返** | **规范**                                                     |
| ----------- | --------- | ------------ | ------------ | ------------------------------------------------------------ |
| id          | string    | 编号         | 是           |                                                              |
| name        | string    | 人名         | 否           |                                                              |
| location    | int_array | 位置坐标     | 否           | 该数组有四个值，分别代表左上角的坐标和右下角的坐标。例如[207,522,340,567]<br/>207代表的是左上角的x坐标<br/>522代表左上角的y坐标<br/>340代表的是右下角的x坐标<br/>567代表的是右下角的y坐标 |
| probability | float     | 置信度       | 否           | 可选值在0～1之间，值越大，可信度越高<br/>                    |
| face_ratio  | float     | 人脸占比     | 否           |                                                              |

businessDetail中，objects数组的每个元素的内容如下：

| **参数名**  | **类型**  | **参数说明** | **是否必返** | **规范**                                                     |
| ----------- | --------- | ------------ | ------------ | ------------------------------------------------------------ |
| id          | string    | 编号         | 是           |                                                              |
| name        | string    | 名称         | 否           |                                                              |
| location    | int_array | 位置坐标     | 否           | 该数组有四个值，分别代表左上角的坐标和右下角的坐标。例如[207,522,340,567]<br/>207代表的是左上角的x坐标<br/>522代表左上角的y坐标<br/>340代表的是右下角的x坐标<br/>567代表的是右下角的y坐标 |
| probability | float     | 置信度       | 否           | 可选值在0～1之间，值越大，可信度越高<br/>                    |

businessDetail中，persons数组的每个元素的内容如下：

| **参数名**   | **类型**  | **参数说明** | **是否必返** | **规范**                                                     |
| ------------ | --------- | ------------ | ------------ | ------------------------------------------------------------ |
| id           | string    | 编号         | 是           |                                                              |
| location     | int_array | 位置坐标     | 否           | 该数组有四个值，分别代表左上角的坐标和右下角的坐标。例如[207,522,340,567]<br/>207代表的是左上角的x坐标<br/>522代表左上角的y坐标<br/>340代表的是右下角的x坐标<br/>567代表的是右下角的y坐标 |
| probability  | float     | 置信度       | 否           | 可选值在0～1之间，值越大，可信度越高<br/>                    |
| person_ratio | float     | 人像占比     | 否           |                                                              |

<span id="audioDetail">其中，audioDetail数组中每个成员的具体内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| requestId       | stringstring | 当前音频片段的唯一标识音频片段地址 | 是 | |
| audioText       | string | 音转文文字 | 否 | 识别出文本会返回 |
| riskLevel | string | 当前事件的处置建议 | 是 | `PASS`：正常内容<br/>`REVIEW`：可疑内容<br/>`REJECT`：违规内容 |
| riskLabel1 | string | 各个一级标签之间是并列的关系，riskLevel为`PASS`时返回`normal` | 是 | 一级标签 |
| riskLabel2 | string | 二级标签归属于一级标签，当riskLevel为`PASS`时为空 | 是 | 二级标签 |
| riskLabel3 | string | 三级标签归属于二级标签，当riskLevel为`PASS`时为空 | 是 | 三级标签 |
| riskDescription | string | 标签解释 | 是 | 格式为&quot;一级风险标签：二级风险标签：三级风险标签&quot;的中文名称<br/>对于命中用户自定义名单时返回：`命中自定义名单` |
| riskDetail | json_object | 风险详情信息 | 是 | 详见[riskDetail说明](#riskDetail2) |
| allLabels | json_array | 全部的风险标签列表 | 是 | 详见[allLabels说明](#allLabels2) |
| businessLabels | json_array | 业务标签列表，传入audioBusinessType时返回 | 否 | 详见[businessLabels说明](#businessLabels2) |

<span id="riskDetail2">audioDetail中，riskDetail的详细内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| riskSource | int | 风险来源 | 是 | 风险来源，可选值：<br/>`1000`：无风险<br/>`1001`：文本风险<br/>`1002`：视觉风险<br/>`1003`：音频风险 |
| audioText | string | 音频转译文本的结果 | 否 | |
| matchedLists | json_array | 命中的客户自定义名单信息 | 否 | 命中客户自定义名单时返回，其他时不存在，详见[matchedLists说明](#matchedLists2) |
| riskSegments | json_array | 高风险内容片段 | 否 | 在涉政、暴恐、违禁、竞品、广告法等功能的时候存在，详见[riskSegments说明](#riskSegments2) |

<span id="matchedLists2">riskDetail中，matchedLists的每个元素详细内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| name | string | 客户自定义名单名 | 是 | |
| words | json_array | 命中的这个名单中的敏感词信息 | 是 | 下标从0开始计数，详见[words说明](#words2) |

<span id="words2">matchedLists中，words的每个元素详细内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| word | string | 敏感词 | 是 | |
| position | int_array | 敏感词所在位置 | 是 | 下标从0开始计数 |

<span id="riskSegments">riskDetail中，riskSegments的每个元素详细内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| segment | string | 高风险内容片段 | 否 | |
| position | int_array | 高风险内容片段所在位置 | 否 | 下标从0开始计数 |

<span id="allLabels">audioDetail中，allLabels数组的每个成员的内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| riskLabel1 | string | 一级风险标签 | 是 | 一级风险标签 |
| riskLabel2 | string | 二级风险标签 | 是 | 二级风险标签 |
| riskLabel3 | string | 三级风险标签 | 是 | 三级风险标签 |
| riskDescription | string | 风险描述 | 是 | 格式为&quot;一级风险标签：二级风险标签：三级风险标签&quot;的中文名称<br/>对于命中用户自定义名单时返回：`命中自定义名单` |
| riskLevel | string | 处置建议 | 是 | `PASS`：正常内容<br/>`REVIEW`：可疑内容<br/>`REJECT`：违规内容 |
| probability | float | 置信度 | 是 | 可选值为0～1，值越大，可信度越高 |
| riskDetail | json_object | 风险详情 | 是 | 同audioDetail中的riskDetail结构一致 |

<span id="businessLabels2">audioDetail中，businessLabels数组的每个成员的内容如下：</span>

| **参数名** | **类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| businessLabel1 | string | 一级标签 | 是 | 一级标签 |
| businessLabel2 | string | 二级标签 | 是 | 二级标签 |
| businessLabel3 | string | 三级标签 | 是 | 三级标签 |
| businessDescription | string | 标签描述 | 是 | 格式为&quot;一级标签：二级标签：三级标签&quot;的中文名称 |
| confidenceLevel | int | 置信等级 | 是 | 可选值在0～2之间，值越大，可信度越高<br/> |
| probability | float | 置信度 | 是 | 可选值为0~1，值越大，可信度越高 |
| businessDetail | Json_object | 详细信息 | 是 |  |

<span id="tokenProfileLabels">其中，tokenProfileLabels数组每个成员的具体内容如下：</span>

| **参数名**  | **类型** | **参数说明** | **是否必返** | **规范**                   |
| ----------- | -------- | ------------ | ------------ | -------------------------- |
| label1      | string   | 一级标签     | 否           |                            |
| label2      | string   | 二级标签     | 否           |                            |
| label3      | string   | 三级标签     | 否           |                            |
| description | string   | 标签描述     | 否           |                            |
| timestamp   | int      | 打标签时间戳 | 否           | 13位Unix时间戳，单位：毫秒 |

<span id="tokenRiskLabels">其中，tokenRiskLabels数组每个成员的具体字段同tokenProfileLabels</span>

### 审核结束回调参数（returnFinishInfo为1时返回）：

| **参数名**        | **类型**    | **参数说明**                                 | **是否必返** | **规范**                                                     |
| ----------------- | ----------- | -------------------------------------------- | ------------ | ------------------------------------------------------------ |
| code              | int         | 请求返回码                                   | 是           | 详见[接口响应码列表](#接口响应码列表)                              |
| message           | string      | 请求返回描述，和请求返回码对应               | 是           | 详见[接口响应码列表](#接口响应码列表)                              |
| requestId         | string      | 请求唯一标识                                 | 是           |                                                              |
| statCode          | int         | 回调状态码                                   | 是           | 回调状态码，当returnFinishInfo为true时存在。状态码对应关系：<br/>0 ：审核结果回调 <br/>1 ：流结束结果回调 <br/>当statCode=1时，如下参数存在 |
| auxInfo        | json_object | 辅助信息                                    | 是           | 详见[auxinfo说明](#auxinfo2) |

<span id="auxinfo2">其中auxInfo字段结构如下：</span>

| **参数名** | **类型** | **参数说明**       | **是否必返** | **规范** |
| ---------- | -------- | ------------------ | ------------ | -------- |
| streamTime | int      | 返回整个流的总时长 | 是           | 向上取整 |



## 视频流关闭接口

### 接口描述

该接口用于客户端通知服务端某个视频流已关闭。

### 请求URL：

| 集群 | URL | 支持产品列表 |
| --- | --- | --- |
| 上海 | `http://api-videostream-sh.fengkongcloud.com/finish_videostream/v5` | 中文视频流 |
| 新加坡 | `http://api-videostream-xjp.fengkongcloud.com/finish_videostream/v5` | 中文视频流<br/>英语视频流<br/>阿语视频流 |
| 硅谷 | `http://api-videostream-gg.fengkongcloud.com/finish_videostream/v5` | 中文视频流<br/>英语视频流<br/>阿语视频流 |
| 印度 | `http://api-videostream-yd.fengkongcloud.com/finish_videostream/v5` | 中文视频流 |

### 请求方法：

`POST`

### 支持协议：

`HTTP`或`HTTPS`

### 字符编码：

`UTF-8`

### 建议超时时间：

1s

### 请求参数：

放在HTTP Body中，采用Json格式，具体参数如下：

| **请求参数名** | **类型** | **参数说明** | **传入说明** | **规范** |
| --- | --- | --- | --- | --- |
| accessKey | string | 公司密钥 | 必传参数 | 用于权限认证，开通账号服务时由数美提供 |
| requestId | string | 本次请求的唯一标识 | 必传参数 | 需要关闭视频流的requestId |

### 返回参数

放在HTTP Body中，采用Json格式，具体参数如下：

| **返回结果参数名** | **参数类型** | **参数说明** | **是否必返** | **规范** |
| --- | --- | --- | --- | --- |
| requestId | string | 本次请求的唯一标识 | 是 | 请求唯一标识 |
| code | int | 请求返回码 | 是 | 请见[接口响应码列表](#接口响应码列表) |
| message | string | 请求返回描述，和请求返回码对应 | 是 | 请见[接口响应码列表](#接口响应码列表) |

## imgBusinessType可选值列表

| 业务标签识别类型 | 类型说明 | 备注 |
| ------------- | ------------- | --------------- |
| AGE           | 人脸 - 年龄   | 可识别未成年人  |
| GENDER        | 人脸 -性别   |  |
| BEAUTY        | 人脸 - 颜值   |  |
| FACEDETECTION | 人脸-人脸检测 | 如识别无人脸、真人、口罩人脸、正脸、侧脸等 |
| FAKEFACE | 人脸 - 伪造人脸 |  |
| RACE | 人脸-人种 | 如黑种人、白种人、黄种人 |
| PUBLICFIGURE | 人物  - 公众人物 | 如识别知名明星、网红等 |
| TAINTEDSTAR | 人物 - 劣迹人物 |  |
| POSTURE | 人像-人像姿态 | 如识别坐姿、跪姿等 |
| DRESS | 人像 - 人像穿着 | 如识别jk、汉服等 |
| BODY | 人体 | 如识别头发、眼睛、鼻子等 |
| PICTUREFORM | 画面属性 - 画面类型 | 如识别动漫、表情包等 |
| PICTURESTRUCT | 画面属性-画面结构 | 如识别宫格图、桥段图等 |
| LOWVISION | 画面属性  - 画面低质 | 如识别模糊、涂抹、马赛克等 |
| LOWCONTNET | 画面属性 - 内容低质 | 如识别点线密集、虫类密集等 |
| LIVEPICTURE | 画面属性-直播画面 | 如识别床上直播、开车直播等 |
| SCREENSHOT | 画面属性 -  APP截图（内容搬运） | 如识别朋友圈截图、聊天截图等 |
| FITNESS | 场景主题-健身 |  |
| CATE | 场景主题-美食 |  |
| MUSIC | 场景主题-音乐 |  |
| SPORTS | 场景主题-体育 |  |
| SCENERY | 场景主题-自然风光 | 如识别天空、大海、草原等 |
| CITYVIEW | 场景主题-城市风光 | 如识别街景 |
| 3CPRODUCTSLOGO | LOGO - 3C电子类品牌 | 如识别华为、小米、OPPO等LOGO |
| SHOPPINGAPPSLOGO | LOGO - 购物比价类应用 | 如识别拼多多等LOGO |
| RETOUCHAPPSLOGO | LOGO - 拍摄美化类应用 | 如识别快剪辑、秒拍等LOGO |
| SOCIALAPPSLOGO | LOGO - 社交通讯类应用 | 如识别微博、小红书等LOGO |
| PHOTOMATERIALLOGO | LOGO - 素材版权类应用 | 如识别CFP等LOGO |
| NEWSAPPSLOGO | LOGO - 新闻阅读类应用 | 如识别新浪、视觉中国等LOGO |
| ENTERTAINMENTAPPSLOGO | LOGO - 影音娱乐类应用 | 如识别抖音、快手等LOGO |
| SPORTSLOGO | LOGO  - 体育赛事 | 如识别奥运会等LOGO |
| APPARELLOGO | LOGO - 鞋帽服饰类品牌 | 如识别VANS、H&M等LOGO |
| ACCESSORIESLOGO | LOGO - 饰品首饰类品牌 | 如识别AudemarsPiguet、Nomos等LOGO | 
| COSMETICSLOGO | LOGO - 化妆品类品牌 | 如识别LOTTE、EyesLipsFace等LOGO | 
| FOODLOGO | LOGO - 食品类品牌 | 如识别Starbucks、LOTTE等LOGO | 
| VEHICLE | 物品-交通工具 |  |
| BUILDING | 物品-建筑 |  |
| TABLEWARE | 物品-餐具 |  |
| FOOD | 物品-食物 |  |
| HOMEAPPLICATION | 物品-家用电器 |  |
| OFFICESUPPLIES | 物品-办公用品 |  |
| FASHION | 物品-穿着用品 |  |
| SPORTEQUIPMENT | 物品-运动器材 |  |
| TOY | 物品-玩具 |  |
| MAKEUP | 物品-化妆品 |  |
| DRUGS | 物品-药品 |  |
| PAINTING | 物品-绘画作品 |  |
| ELECTRONIC | 物品-电子产品 |  |
| MEDICALIMAGE | 物品-医疗影像 |  |
| FURNITURE | 物品-家居用品 |  |
| DAILYSUPPLIES | 物品-生活用品 |  |
| CONSTELLATION | 物品-星座占卜 |  |
| KITCHENWARE | 物品-厨房用品 |  |
| KEEPSAKE | 物品 - 纪念品 |  |
| MAMMAL | 动物-哺乳动物 |  |
| BIRDS | 动物 - 鸟类 |  |
| REPTILE | 动物-爬行动物 |  |
| FISH | 动物-鱼 |  |
| ARTHROPOD | 动物  - 节肢动物 |  |
| COELENTERATE | 动物  - 腔肠动物 |  |
| MOLLUSKS | 动物  - 软体动物 |  |
| CRUSTACEAN | 动物  - 甲壳动物 |  |
| PLANT | 植物 |  |
| SETTING | 场所 | 如识别卫生间、酒店、厨房等 |

## 接口响应码列表

code请求返回码列表如下：

| **code** | **message**      |
| -------- | ---------------- |
| 1100     | 成功             |
| 1901     | QPS超限          |
| 1902     | 参数不合法       |
| 1903     | 服务失败         |
| 1904     | 流路数超限       |
| 9100     | 余额不足         |
| 9101     | 无权限操作       |

## 示例

### 上传接口请求示例

```json
{
    "accessKey": "*********",
    "appId": "defaulttest",
    "eventId": "VIDEOSTREAM",
    "imgType": "POLITICS_VIOLENCE_BAN_PORN_MINOR_AD_SPAM_LOGO_STAR",
    "imgBusinessType": "SCREEN_SCENCE_QR_FACE_QUALITY_MINOR_LOGO_BEAUTY",
    "audioType": "POLITICAL_PORN_AD_MOAN",
    "audioBusinessType": "SING_LANGUAGE_MINOR_GENDER_TIMBRE",
    "callback": "http://www.xxx.top/xxx",
    "data": {
        "streamType": "NORMAL",
        "tokenId": "123",
        "ip": "123.171.34.4",
        "detectFrequency": 10,
        "room": "5e1854a6a0a79d0001a09bc3",
        "url": "http://rtmp.xxxx.cn/live/3637778raLSXdOdu.flv",
        "returnAllVideo": 1,
        "lang": "zh",
        "extra": {
            "passThrough": {
                "passThrough1": "111",
                "passThrough2": "222",
                "passThrough3": "333"
            }
        }
    }
}
```

### 上传接口返回示例：

```json
{
    "code":1100,
    "message":"正常",
    "requestId":"d05f4270374ca516ce7aafc0139afd25"
}
```

### 异步回调结果示例：

**视频片段回调**

```json
{
    "code": 1100,
    "message": "成功",
    "requestId": "1639825145166_v130_1639825248361471656",
    "statCode": 0,
    "riskLevel": "PASS",
    "riskLabel1": "normal",
    "riskLabel2": "",
    "riskLabel3": "",
    "videoDetail": {
        "frameDetail": [
            {
                "allLabels": [
                    {
                        "riskDescription": "涉政:涉政:涉政",
                        "riskLabel1": "politics",
                        "riskLabel2": "shezheng",
                        "riskLabel3": "shezheng",
                        "riskLevel": "REJECT"
                    }
                ],
                "businessLabels": [],
                "imgUrl": "http://bj.cos.ap-beijing.xxx.com/image/1639825145166_vs130_1639825248361471656.jpg",
                "riskDescription": "涉政:涉政:涉政",
                "riskDetail": {
                    "ocrText": {
                        "text": "第四页（ban第五页（violence"
                    },
                    "riskSource": 1002
                },
                "riskLabel1": "politics",
                "riskLabel2": "shezheng",
                "riskLabel3": "shezheng",
                "riskLevel": "REJECT",
                "time": 5,
                "requestId": "1639825145166_v130_1639825248361471656",
                "imgText": "OCR文字"
            }
        ],
        "audioDetail": {
            "allLabels": [
                {
                    "riskDescription": "涉政:涉政:涉政",
                    "riskLabel1": "politics",
                    "riskLabel2": "shezheng",
                    "riskLabel3": "shezheng",
                    "riskLevel": "REJECT"
                }
            ],
            "businessLabels": [],
            "audioText": "ASR问题",
            "riskDescription": "涉政:涉政:涉政",
            "riskDetail": {
                "ocrText": {
                    "text": "第四页（ban第五页（violence"
                },
                "riskSource": 1002
            },
            "riskLabel1": "politics",
            "riskLabel2": "shezheng",
            "riskLabel3": "shezheng",
            "riskLevel": "REJECT",
            "requestId": "1639825145166_v130_1639825248361471656"
        },
        "videoUrl": "http://xxx.com/xxx",
        "startTime": 1639825145166,
        "endTime": 1639825145166
    },
    "auxInfo": {
        "passThrough": {
            "passThrough1": "111",
            "passThrough2": "222",
            "passThrough3": "333"
        },
        "frameCount": 1
    }
}
```

### 关闭接口请求示例：

```json
{
    "accessKey": "*********",
    "requestId": "1639825145166"
}
```

### 关闭接口返回示例

```json
{
    "code":1100,
    "message":"成功",
    "requestId":" a78eef377079acc6cdec24967ecde722"
}
```
