## 路径
所有API部署到以下基础路径：
```
http://foxgis.com/api/v1
```

## access_token

除用户注册和用户登录外，所有的API都需要`access_token`获取权限。
请妥善保管好您的`access_token`，因为`access_token`可以获取到您的所有资源权限。
设置`access_token`有三种方式，优先级从上至下有：

方式 | 示例
---|---
url方式 | GET /{endpoint}?access_token={your_access_token}
cookie方式 | cookies["access_token"] = {your_access_token}
header方式 | headers['x-access-token'] = {your_access_token}


## 用户模块

字段 | 说明
---|---
username | 用户名
access_token | 访问令牌，只在用户注册和登录的时候返回
scope | 用户信息访问范围，可以是`private`或`public`
is_verified | 是否经过了认证
name | 姓名
location | 用户所在地区
organization | 单位
position | 职务/职称
telephone | 固定电话
mobile | 手机号码
email | 电子邮箱
signature | 个人签名
createdAt | 用户创建时间
updatedAt | 用户信息更新时间
downloadNum | 用户下载文件数量


### 用户注册

用户注册不需要提供`access_token`。用户注册时必须填写`username`、`password`,可以选择性地完善自己的`name`、`location`、`organization`、`position`、`telephone`、`mobile`、`email`、`signature`信息。

```endpoint
POST /users
```

#### 请求示例
```json
{
  "username": "jingsam",
  "password": "123456",
  "name": "jingsam",
  "email": "jingsam@gmail.com",
  "telephone": 123456789,
  "location": "beijing",
  "organization": "foxgis"
}
```

#### 响应成功
```json
{
  "username": "jingsam",
  "access_token": "{your_access_token}",
  "scope": "public",
  "is_verified": false,
  "name": "jingsam",
  "email": "jingsam@gmail.com",
  "telephone": 123456789,
  "location": "beijing",
  "organization": "foxgis",
  "updatedAt": "2016-05-06T12:33:02.689Z",
  "createdAt": "2016-05-06T11:33:02.689Z"
}
```

#### 响应失败
```json
{
  "error": "密码长度太短"
}
```


### 用户登录

用户登录不需要`access_token`，但登录成功后会生成新的`access_token`。

```endpoint
POST /users/{username}
```

#### 请求示例
```json
{
  "password": "123456"
}
```

#### 响应成功
```json
{
  "username": "jingsam",
  "scope": "public",
  "is_verified": false,
  "name": "张三",
  "access_token": "{your_access_token}",
  "email": "jingsam@163.com",
  "telephone": "12345678",
  "organization": "WHU",
  "createdAt": "2016-05-06T10:18:59.498Z",
  "updatedAt": "2016-05-06T11:37:35.551Z"
}
```

#### 响应失败
```json
{
  "error": "用户名或密码错误"
}
```


### 获取用户信息

获取用户信息不会得到`access_token`

```endpoint
GET /users/{username}
```

#### 响应成功
```json
{
  "username": "jingsam",
  "scope": "public",
  "is_verified": false,
  "name": "jingsam",
  "email": "jingsam@163.com",
  "telephone": "12345678",
  "organization": "WHU",
  "downloadNum": 0,
  "createdAt": "2016-05-06T10:18:59.498Z",
  "updatedAt": "2016-05-06T11:37:35.551Z"
}
```

#### 响应失败
```json
{
  "error": "用户不存在"
}
```


### 获取用户列表

超级管理员可以获取一般管理员和普通用户的信息列表

```endpoint
GET /users
```

#### 响应成功
```json
[
  {
    "username": "jingsam",
    "scope": "public",
    "is_verified": false,
    "name": "jingsam",
    "email": "jingsam@163.com",
    "telephone": "12345678",
    "organization": "WHU",
    "downloadNum": 0,
    "createdAt": "2016-05-06T10:18:59.498Z",
    "updatedAt": "2016-05-06T11:37:35.551Z"
  }
]
```

#### 响应失败
```json
HTTP 500
```


### 更新用户信息

一般管理员和普通用户只允许更新各自用户信息中的`scope`、`name`、`location`、`organization`、`postition`、`telephone`、`mobile`、`email`、`signature`属性，而超级管理员可以更新其他所有非超级管理员的所有属性，包括`is_verified`、`role`。

```endpoint
PATCH /users/{username}
```

#### 请求示例
```json
{
  "name": "张三",
  "scope": "private"
}
```

#### 响应成功
```json
{
  "username": "jingsam",
  "scope": "private",
  "is_verified": false,
  "name": "张三",
  "email": "jingsam@163.com",
  "telephone": "12345678",
  "organization": "WHU",
  "createdAt": "2016-05-06T10:18:59.498Z",
  "updatedAt": "2016-05-06T11:37:35.551Z"
}
```

#### 响应失败
```json
{
  "error": "name字段过长"
}
```


### 修改用户密码

修改成功后返回新的access_token。

```endpoint
PATCH /users/{username}/password
```

#### 请求示例
```json
{
  "oldPassword": "123456",
  "newPassword": "111111"
}
```

#### 响应成功
```json
{
  "username": "jingsam",
  "scope": "private",
  "is_verified": true,
  "name": "张三",
  "email": "jingsam@163.com",
  "telephone": "12345678",
  "organization": "WHU",
  "createdAt": "2016-05-06T10:18:59.498Z",
  "updatedAt": "2016-05-06T11:37:35.551Z"
}
```

#### 响应失败
```json
HTTP 500
```


### 删除用户信息

超级管理员可以删除其他用户的信息。

```endpoint
DELETE /users/{username}
```

#### 响应成功
```json
HTTP 204
```

#### 响应失败
```json
HTTP 401
```


### 更新用户头像

上传的用户头像会自动缩放到100*100px

```endpoint
PUT /users/{username}/avatar
```


### 获取用户头像


```endpoint
GET /users/{username}/avatar
```



## 地图样式模块

管理地图样式，地图样式遵循Mapbox GL JS Spec。

字段信息：
字段 | 说明
---|---
style_id | 地图样式ID
owner | 所有者
scope | 共享范围，可能的取值有`private`和`public`，默认为`public`
tags | 标签
description | 描述信息
version | 版本
name | 名称
metadata | 元数据信息
center | 中心点，[lnt, lat]
zoom | 缩放级别
bearing | 方位角
pitch | 倾斜度
sources | 数据来源
sprite | 符号库
glyphs | 字体库
transition | 渐变
layers | 图层信息
createdAt | 样式创建时间
updatedAt | 样式更新时间


### 获取样式列表
```endpoint
GET /styles/{username}
```

#### 响应成功
```json
[{
    "updatedAt": "2016-05-05T11:06:22.957Z",
    "createdAt": "2016-05-05T10:06:22.957Z",
    "owner": "jingsam",
    "name": "Basic",
    "scope": "private",
    "tags": ['basic', "style"],
    "version": 8,
    "style_id": "BywXo5O-"
},{
    "updatedAt": "2016-05-05T11:06:00.957Z",
    "createdAt": "2016-05-05T10:06:00.957Z",
    "owner": "jingsam",
    "name": "Empty",
    "scope": "private",
    "tags": ["empty", "style"]
    "version": 8,
    "style_id": "SygXI5OZ"
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取某个样式
```endpoint
GET /styles/{username}/{style_id}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:06:22.957Z",
  "createdAt": "2016-05-05T10:06:22.957Z",
  "owner": "jingsam",
  "name": "Basic",
  "scope": "private",
  "metadata": {
    "mapbox:autocomposite": true
  },
  "glyphs": "mapbox://fonts/mapbox/{fontstack}/{range}.pbf",
  "sources": {},
  "layers": [
    {
      "id": "background",
      "type": "background",
      "paint": {
        "background-color": "rgba(0,0,0,0)"
      }
    }
  ],
  "pitch": 0,
  "bearing": 0,
  "center": [],
  "version": 8,
  "draft": true,
  "style_id": "BywXo5O-"
}
```

#### 响应失败
```json
HTTP 404
```


### 新建样式

```endpoint
POST /styles/{username}
```

#### 请求示例
```json
{
  "version": 8,
  "name": "Basic",
  "metadata": {
    "mapbox:autocomposite": true
  },
  "glyphs": "mapbox://fonts/mapbox/{fontstack}/{range}.pbf",
  "sources": {},
  "layers": [
    {
      "id": "background",
      "type": "background",
      "paint": {
        "background-color": "rgba(0,0,0,0)"
      }
    }
  ]
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:06:22.957Z",
  "createdAt": "2016-05-05T10:06:22.957Z",
  "owner": "jingsam",
  "name": "Basic",
  "scope": "private",
  "metadata": {
    "mapbox:autocomposite": true
  },
  "glyphs": "mapbox://fonts/mapbox/{fontstack}/{range}.pbf",
  "sources": {},
  "layers": [
    {
      "paint": {
        "background-color": "rgba(0,0,0,0)"
      },
      "type": "background",
      "id": "background"
    }
  ],
  "pitch": 0,
  "bearing": 0,
  "center": [],
  "version": 8,
  "style_id": "BywXo5O-"
}
```

#### 响应失败
```json
{
  "error": "格式不正确"
}
```


### 更新某个样式

字段除`style_id`、`owner`、`createdAt`、`updatedAt`都可更新

```endpoint
PATCH /styles/{username}/{style_id}
```

#### 请求示例
```json
{
  "scope": "public",
  "tags": ["foxgis"],
  "style_id": "abcd",
  "owner": "judy",
  "name": "jingsam2",
  "center": [50.000000, 40.000000],
  "layers": [{
    "paint": {
      "background-color": "rgba(1,1,1,1)"
    }
  }]
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:22:12.020Z",
  "createdAt": "2016-05-05T10:06:22.957Z",
  "owner": "jingsam",
  "scope": "public",
  "name": "jingsam2",
  "tags": ["foxgis"]
  "metadata": {
    "mapbox:autocomposite": true
  },
  "glyphs": "mapbox://fonts/mapbox/{fontstack}/{range}.pbf",
  "layers": [
    {
      "paint": {
        "background-color": "rgba(1,1,1,1)"
      }
    }
  ],
  "pitch": 0,
  "bearing": 0,
  "center": [50,40],
  "version": 8,
  "draft": true,
  "style_id": "BywXo5O-"
}
```

#### 响应失败
```json
HTTP 404
```


### 删除样式
```endpoint
DELETE /styles/{username}/{style_id}
```

#### 响应成功
```json
HTTP 204
```


### 获取地图瓦片数据

获取地图样式的瓦片数据

```endpoint
GET /styles/{username}/{style_id}/{z}/{x}/{y}{@2x}.{format}
```


### 获取地图预览

获取设定范围的地图预览

```endpoint
GET /styles/{username}/{style_id}/thumbnail?zoom={zoom}&scale={scale}&bbox={bbox}
```

参数 | 说明
--- | ---
zoom | 缩放级别
scale | 像素密度级别，以72dpi为基础等比例递增，取值范围[1, 4]
bbox | 以四至范围设置输出范围，格式为[w, s, e, n]，例如全球范围为[-180,-85.0511,180,85.0511]
center | 以中心点设置输出范围，格式为{x: 0, y: 0, width: 256, height: 256}
format | 输出图片的格式，可以是png, jpg
quality | 输出图片的质量，取值范围为[0, 100]


## 瓦片集模块

管理用户上传的制图数据

字段信息：
字段 | 说明
--- | ---
tileset_id | 瓦片集ID
owner | 所有者
scope | 共享范围，可能的取值有`private`和`public`，默认为`public`
tags | 标签
filename | 上传文件名
filesize | 上传文件大小
tilejson... | tilejson 相关字段，遵循Tilejson Spec标准
createdAt | 瓦片集创建时间
updatedAt | 瓦片集更新时间


### 获取瓦片集列表1

获取系统中所有瓦片集信息，普通用户只能获取个人或其他用户公开的瓦片集内容

```endpoint
GET /tilesets
```

#### 响应成功
```json
[{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "owner": "jingsam",
  "filesize": 65794689024,
  "description": "Extract from osm2vectortiles.org",
  "format": "pbf",
  "name": "Open Streets",
  "attribution": "&copy; OpenStreetMap contributors",
  "progress": 100,
  "center": [ 116.3400305, 39.9589555, 10 ],
  "bounds": [ 116.04142, 39.75872, 116.638641, 40.159191 ],
  "maxzoom": 14,
  "minzoom": 0,
  "data": [],
  "grids": [],
  "tiles": [],
  "scheme": "xyz",
  "version": "1",
  "tilejson": "2.1.0",
  "tags": [],
  "scope": "private",
  "tileset_id": "rJ6P9GlQ"
},{
  "updatedAt": "2016-05-23T06:47:29.012Z",
  "createdAt": "2016-05-23T06:47:26.847Z",
  "owner": "jingsam",
  "progress": 100,
  "filesize": 65794689024,
  "description": "Extract from osm2vectortiles.org",
  "format": "pbf",
  "name": "beijing",
  "attribution": "&copy; OpenStreetMap contributors",
  "center": [ 116.3400305, 39.9589555, 10 ],
  "bounds": [ 116.04142, 39.75872, 116.638641, 40.159191 ],
  "maxzoom": 14,
  "minzoom": 0,
  "data": [],
  "grids": [],
  "tiles": [],
  "scheme": "xyz",
  "version": "1",
  "tilejson": "2.1.0",
  "tags": [],
  "scope": "private",
  "tileset_id": "rJvKDXgQ"
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取瓦片集列表2

获取系统中某个用户{username}上传的瓦片集信息

```endpoint
GET /tilesets/{username}
```

#### 响应成功
```json
[{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "owner": "jingsam",
  "filesize": 65794689024,
  "description": "Extract from osm2vectortiles.org",
  "format": "pbf",
  "name": "Open Streets",
  "attribution": "&copy; OpenStreetMap contributors",
  "progress": 100,
  "center": [ 116.3400305, 39.9589555, 10 ],
  "bounds": [ 116.04142, 39.75872, 116.638641, 40.159191 ],
  "maxzoom": 14,
  "minzoom": 0,
  "data": [],
  "grids": [],
  "tiles": [],
  "scheme": "xyz",
  "version": "1",
  "tilejson": "2.1.0",
  "tags": [],
  "scope": "private",
  "tileset_id": "rJ6P9GlQ"
},{
  "updatedAt": "2016-05-23T06:47:29.012Z",
  "createdAt": "2016-05-23T06:47:26.847Z",
  "owner": "jingsam",
  "progress": 100,
  "filesize": 65794689024,
  "description": "Extract from osm2vectortiles.org",
  "format": "pbf",
  "name": "beijing",
  "attribution": "&copy; OpenStreetMap contributors",
  "center": [ 116.3400305, 39.9589555, 10 ],
  "bounds": [ 116.04142, 39.75872, 116.638641, 40.159191 ],
  "maxzoom": 14,
  "minzoom": 0,
  "data": [],
  "grids": [],
  "tiles": [],
  "scheme": "xyz",
  "version": "1",
  "tilejson": "2.1.0",
  "tags": [],
  "scope": "private",
  "tileset_id": "rJvKDXgQ"
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取瓦片集信息

获取瓦片集信息，以tilejson格式表达。

```endpoint
GET /tilesets/{username}/{tileset_id}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "owner": "jingsam",
  "filesize": 65794689024,
  "description": "Extract from osm2vectortiles.org",
  "format": "pbf",
  "name": "Open Streets",
  "attribution": "&copy; OpenStreetMap contributors",
  "progress": 100,
  "center": [ 116.3400305, 39.9589555, 10 ],
  "bounds": [ 116.04142, 39.75872, 116.638641, 40.159191 ],
  "maxzoom": 14,
  "minzoom": 0,
  "data": [],
  "grids": [],
  "tiles": [],
  "scheme": "xyz",
  "version": "1",
  "tilejson": "2.1.0",
  "tags": [],
  "scope": "private",
  "tileset_id": "rJ6P9GlQ"
}
```

#### 响应失败
```json
HTTP 404
```


### 上传数据

上传数据，系统根据上传的数据自动地转换为瓦片数据，支持的格式有`mbtiles`、`geojson`、`topojson`、`shapefile`、`tif`。

```endpoint
POST /tilesets/{username}
```


#### 响应成功
```json
{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "owner": "jingsam",
  "filesize": 65794689024,
  "data": [],
  "grids": [],
  "tiles": [],
  "scheme": "xyz",
  "version": "1",
  "tilejson": "2.1.0",
  "tags": [],
  "scope": "private",
  "tileset_id": "rJ6P9GlQ"
}
```

#### 响应失败
```json
HTTP 500
```


### 更新瓦片集信息

可更新的字段有`scope`、`tags`、`name`、`description`、`vector_layers`。

```endpoint
PATCH /tilesets/{username}/{tileset_id}
```

#### 请求示例
```json
{
  "scope": "public"
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "scope": "public",
  "owner": "jingsam",
  "filesize": 65794689024,
  "description": "Extract from osm2vectortiles.org",
  "format": "pbf",
  "name": "Open Streets",
  "attribution": "&copy; OpenStreetMap contributors",
  "progress": 100,
  "center": [ 116.3400305, 39.9589555, 10 ],
  "bounds": [ 116.04142, 39.75872, 116.638641, 40.159191 ],
  "maxzoom": 14,
  "minzoom": 0,
  "data": [],
  "grids": [],
  "tiles": [],
  "scheme": "xyz",
  "version": "1",
  "tilejson": "2.1.0",
  "scope": "private",
  "tileset_id": "rJ6P9GlQ"
}
```

#### 响应失败
```json
HTTP 404
```


### 删除瓦片集
```endpoint
DELETE /tilesets/{username}/{tileset_id}
```

#### 响应成功
```json
HTTP 204
```


### 获取瓦片

根据`z`、`x`、`y`获取单张瓦片

```endpoint
GET /tilesets/{username}/{tileset_id}/{z}/{x}/{y}{@2x}.{format}
```


### 下载源文件

下载生成瓦片的原始数据

```endpoint
GET /tilesets/{username}/{tileset_id}/raw
```



## 字体模块

制图模块管理用户上传的字体。

字段信息：
字段 | 说明
--- | ---
fontname | 字体名称
owner | 所有者
scope | 共享范围，可能的取值有`private`和`public`，默认为`public`
family_name | 字族名称
style_name | 字体样式，如`Regular`，`Bold`，`Italic`
coverages | 字体语言覆盖率信息
coverages.name | 语言名称，如`English`
coverages.id | 语言名称简写，如`en`
coverages.count | 字体在该语言中的覆盖的字符数
coverages.total | 该语言的中字符数


### 获取用户字体列表
```endpoint
GET /fonts/{username}
```

#### 响应成功
```json
[{
  "fontname": "STKaiti Regular",
  "owner": "jingsam",
  "updatedAt": "2016-06-19T06:17:10.795Z",
  "style_name": "Regular",
  "family_name": "STKaiti",
  "createdAt": "2016-06-19T06:17:10.795Z",
  "coverages": [{
      "total": 45,
      "count": 0,
      "id": "ar",
      "name": "Arabic"
  }]
},{
  "fontname": "Open Sans Regular",
  "owner": "jingsam",
  "updatedAt": "2016-06-19T06:17:10.795Z",
  "style_name": "Regular",
  "family_name": "STKaiti",
  "createdAt": "2016-06-19T06:17:10.795Z",
  "coverages": [{
      "total": 45,
      "count": 0,
      "id": "ar",
      "name": "Arabic"
  }]
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取字体信息
```endpoint
GET /fonts/{username}/{fontname}
```

#### 响应成功
```json
{
  "fontname": "STKaiti Regular",
  "owner": "jingsam",
  "updatedAt": "2016-06-19T06:17:10.795Z",
  "style_name": "Regular",
  "family_name": "STKaiti",
  "createdAt": "2016-06-19T06:17:10.795Z",
  "coverages": [{
    "total": 45,
    "count": 0,
    "id": "ar",
    "name": "Arabic"
  }, {
    "total": 37,
    "count": 37,
    "id": "ca",
    "name": "Catalan"
  }, {
    "total": 42,
    "count": 42,
    "id": "cs",
    "name": "Czech"
  }]
}
```

#### 响应失败
```json
{
  "errors": "字体不存在"
}
```


### 上传字体

仅支持ttf、otf字体文件的上传

```endpoint
POST /fonts/{username}
```

#### 响应成功
```json
{
  "fontname": "STKaiti Regular",
  "owner": "jingsam",
  "updatedAt": "2016-06-19T06:17:10.795Z",
  "style_name": "Regular",
  "family_name": "STKaiti",
  "createdAt": "2016-06-19T06:17:10.795Z",
  "coverages": [{
      "total": 45,
      "count": 0,
      "id": "ar",
      "name": "Arabic"
  }]
}
```

#### 响应失败
```json
HTTP 500
```


### 更新字体信息

允许修改字段有`scope`。

```endpoint
PATCH /fonts/{username}/{fontname}
```

#### 请求示例
```json
{
  "scope": "private"
}
```

#### 响应成功
```json
{
  "fontname": "STKaiti Regular",
  "owner": "jingsam",
  "scope": "private",
  "updatedAt": "2016-06-19T06:17:10.795Z",
  "style_name": "Regular",
  "family_name": "STKaiti",
  "createdAt": "2016-06-19T06:17:10.795Z",
  "coverages": [{
      "total": 45,
      "count": 0,
      "id": "ar",
      "name": "Arabic"
  }]
}
```

#### 响应失败
```json
{
  "errors": "字体不存在"
}
```


### 删除字体
```endpoint
DELETE /fonts/{username}/{fontname}
```

#### 响应成功
```json
HTTP 204
```


### 获取字形

根据支付码点范围，获取转换后的pbf格式的字形文件

```endpoint
GET /fonts/{username}/{fontname}/{start}-{end}.pbf
```

参数 | 说明
start | 字形码点的起点，取值为256的倍数，范围为[0, 65280]
end | 字形码点的终点，取值为`start` + 255

#### 响应成功
```json
HTTP 200
```


### 下载字体源文件

下载字体源文件，如`ttf`或`otf`字体文件

```endpoint
GET /fonts/{username}/{fontname}/raw
```

#### 响应成功
```json
HTTP 200
```


### 字体预览

获取字体预览，格式为`png`

```endpoint
GET /fonts/{username}/{fontname}/thumbnail
```

#### 响应成功
```json
HTTP 200
```



## 符号库模块

符号库模块管理用户上传的符号库

字段信息：
字段 | 说明
--- | ---
sprite_id | 符号库id
owner | 所有者
scope | 共享范围，可能的取值有`private`和`public`，默认为`public`
name | 符号库名称
filename | 上传符号库文件名称
filesize | 上传符号库文件大小
description | 符号库描述信息
createdAt | 符号库创建时间
updatedAt | 符号库更新时间


### 获取符号库列表1

获取系统中所有符号库信息，普通用户只能获取个人或其他用户公开的符号库内容

```endpoint
GET /sprites
```

#### 响应成功
```json
[{
  "updatedAt": "2016-05-05T10:29:00.845Z",
  "createdAt": "2016-05-05T10:29:00.845Z",
  "owner": "jingsam",
  "filename": "Sprite.zip",
  "filesize": 101346,
  "description": "",
  "name": "Sprite",
  "scope": "private",
  "sprite_id": "BkgH_lsub"
},{
  "updatedAt": "2016-05-05T10:00:00.845Z",
  "createdAt": "2016-05-05T10:00:00.845Z",
  "owner": "jingsam",
  "filename": "Sprite.zip",
  "filesize": 101346,
  "description": "",
  "name": "Sprite",
  "scope": "private",
  "sprite_id": "BywXo5O-"
}]
```

#### 响应失败
```json
{
  "errors": "内部错误"
}
```


### 获取符号库列表2

获取某个用户{username}上传的符号库信息

```endpoint
GET /sprites/{username}
```

#### 响应成功
```json
[{
  "updatedAt": "2016-05-05T10:29:00.845Z",
  "createdAt": "2016-05-05T10:29:00.845Z",
  "owner": "jingsam",
  "filename": "Sprite.zip",
  "filesize": 101346,
  "description": "",
  "name": "Sprite",
  "scope": "private",
  "sprite_id": "BkgH_lsub"
},{
  "updatedAt": "2016-05-05T10:00:00.845Z",
  "createdAt": "2016-05-05T10:00:00.845Z",
  "owner": "jingsam",
  "filename": "Sprite.zip",
  "filesize": 101346,
  "description": "",
  "name": "Sprite",
  "scope": "private",
  "sprite_id": "BywXo5O-"
}]
```

#### 响应失败
```json
{
  "errors": "内部错误"
}
```


### 获取符号库信息
```endpoint
GET /sprites/{username}/{sprite_id}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:37:17.182Z",
  "createdAt": "2016-05-05T10:29:00.845Z",
  "owner": "jingsam",
  "filename": "Sprite.zip",
  "filesize": 101346,
  "description": "",
  "name": "Sprite",
  "scope": "private",
  "sprite_id": "BkgH_lsub"
}
```

#### 响应失败
```json
{
  "errors": "获取不存在的符号库"
}
```


### 上传符号库

只允许上传包含`svg`文件的`zip`文件，生成新的符号库文件。

```endpoint
POST /sprites/{username}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:29:00.845Z",
  "createdAt": "2016-05-05T10:29:00.845Z",
  "owner": "jingsam",
  "filename": "Sprite.zip",
  "filesize": 101346,
  "description": "",
  "name": "sprite",
  "scope": "public",
  "sprite_id": "BkgH_lsub"
}
```

#### 响应失败
```json
{
  "errors": "内部错误"
}
```


### 添加图标

为符号库添加一个图标，上传的图标格式为`svg`。注意，如果图标在符号库中已存在，将会替换符号库中的图标。

```endpoint
PUT /sprites/{username}/{sprite_id}/{icon}
```

#### 相应成功
```json
HTTP 204
```

#### 响应失败
```json
{
  "error": "仅支持svg格式的图标"
}
```


### 更新符号库信息

允许更新的字段有`name`，`scope`，`description`。

```endpoint
PATCH /sprites/{username}/{sprite_id}
```

#### 请求示例
```json
{
  "scope": "public",
  "name": "newname",
  "description": "this is a description example"
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:37:17.182Z",
  "createdAt": "2016-05-05T10:29:00.845Z",
  "owner": "jingsam",
  "filename": "Sprite.zip",
  "filesize": 101346,
  "description": "this is a description example",
  "name": "newname",
  "scope": "public",
  "sprite_id": "BkgH_lsub"
}
```


### 删除符号库
```endpoint
DELETE /sprites/{username}/{sprite_id}
```

#### 响应成功
```json
HTTP 204
```

#### 响应失败
```json
HTTP 404
```

### 删除图标

删除符号库中的一个图标

```endpoint
DELETE /sprites/{username}/{sprite_id}/{icon}
```

#### 响应成功
```json
HTTP 204
```

#### 响应失败
```json
HTTP 404
```


### 获取符号库

```endpoint
GET /sprites/{username}/{sprite_id}/sprite@{scale}x.{format}
```

参数 | 说明
--- | ---
scale | 分辨率级别，取值范围为[1, 4]，默认为1
format | 文件格式，可能的取值为`json`和`png`，默认为`json`

#### 响应成功
```json
{
  "us-highway-alternate-3": {
    "width": 26,
    "height": 38,
    "x": 0,
    "y": 0,
    "pixelRatio": 1
  },
  "us-highway-business-2": {
    "width": 20,
    "height": 38,
    "x": 26,
    "y": 0,
    "pixelRatio": 1
  }
}
```

#### 响应失败
```json
HTTP 404
```


### 打包符号库

包括符号库中的`svg`文件并下载。

```endpoint
GET /sprites/{username}/{sprite_id}/raw
```


### 下载图标

下载符号库中的一个`svg`图标。

```endpoint
GET /sprites/{username}/{sprite_id}/{icon}
```



## 文件模块

文件模块用于管理用户上传的文件，上传的文件大小限制在`200M`。如果上传的文件为图片，还可获取缩略图。

字段信息：
字段 | 说明
---|---
file_id | 文件id
owner | 文件所有者
scope | 共享范围，可能的取值为`private`和`public`，默认为`private`
name | 文件名
year | 制图时间
location | 制图地区
tags | 标签
description | 描述信息
scale | 比例尺
dimensions | 图幅的长度和宽度，单位为毫米
filename | 上传文件的文件名
filesize | 文件大小，单位为字节数
createdAt | 文件上传时间
updatedAt | 文件信息更新时间
downloadNum | 文件被下载次数


### 获取文件统计信息

统计系统中各地区总共上传了多少文件

```endpoint
GET /files/stats
```

#### 响应成功
```json
[{
  "total": 11,
  "location": "湖北"
},{
  "total": 5,
  "location": "北京"
}]
```


### 搜索文件

搜索公开分享的文件

```endpoint
GET /files/search?keywords={keyword1}+{keyword2}&limit=10&skip=20&sort=-createdAt
```

查询参数 | 说明
---|---
keywords | 搜索的关键字，多个关键字用`+`隔开，例如：`search=北京+上海`
limit | 返回结果的最大数目
skip | 跳过前面多少个查询结果，默认值为0
sort | 设置排序字段，降序在字端前加`-`,例如：`sort=-createdAt`


### 获取文件列表
```endpoint
GET /files/{username}
```

#### 响应成功
```json
[{
    "updatedAt": "2016-05-05T10:44:56.201Z",
    "createdAt": "2016-05-05T09:44:56.101Z",
    "name": "china",
    "format": "json",
    "size": 99410,
    "downloadNum": 0,
    "owner": "jingsam",
    "tags": ["china", "2016"],
    "description": "a big file",
    "file_id": "BJ7Sgvd-"
},{
    "updatedAt": "2016-05-05T11:44:56.201Z",
    "createdAt": "2016-05-05T09:00:56.101Z",
    "name": "beijing",
    "format": "mbtiles",
    "size": 102347,
    "downloadNum": 0,
    "owner": "jingsam",
    "tags": ["beijing", "2015"],
    "description": "a bigger file",
    "file_id": "SygXI5OZ"
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取文件信息
```endpoint
GET /files/{username}/{file_id}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:44:56.201Z",
  "createdAt": "2016-05-05T09:44:56.101Z",
  "name": "china",
  "format": "json",
  "size": 99410,
  "downloadNum": 0,
  "owner": "jingsam",
  "tags": ["china", "2016"],
  "description": "a big file",
  "file_id": "SygXI5OZ"
}
```

#### 响应失败
```json
{
  "error": "文件不存在"
}
```


### 上传文件

一次只允许上传一个文件，且文件大小要小于200M。

```endpoint
POST /files/{username}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:44:56.201Z",
  "createdAt": "2016-05-05T09:44:56.101Z",
  "name": "china",
  "format": "png",
  "size": 99410,
  "owner": "jingsam",
  "upload_id": "BJ7Sgvd-"
}
```

#### 响应失败
```json
HTTP 500
```


### 更新文件信息
```endpoint
PATCH /files/{username}/{file_id}
```

可更新的字段有`scope`，`name`，`year`，`location`，`tags`，`description`，`scale`，`dimensions`。

#### 请求示例
```json
{
  "name": "new",
  "tags": ["new"],
  "description": "a new file"
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:44:56.201Z",
  "createdAt": "2016-05-05T09:44:56.101Z",
  "name": "new",
  "format": "json",
  "size": 99410,
  "owner": "jingsam",
  "tags": ["new"],
  "description": "a new file",
  "file_id": "SygXI5OZ"
}
```

#### 响应失败
```json
{
  "error": "更新的文件不存在"
}
```


### 删除文件
```endpoint
DELETE /files/{username}/{file_id}
```

#### 响应成功
```json
HTTP 204
```

#### 响应失败
```json
HTTP 404
```


### 下载文件
```endpoint
GET /files/{username}/{file_id}/raw
```


### 获取概览图

```endpoint
GET /files/{username}/{file_id}/thumbnail?width=300&height=400&quality=50
```

查询参数 | 说明
---|---
width | 缩略图的宽度，单位为像素，当width和height同时未设置时，width取默认值1000
height | 缩略图的高度，单位为像素
quality | 缩略图的质量，取值范围为[0, 100]，默认为100


### 获取文件元数据信息

返回一个excel文件，包含用户上传所有文件的元数据信息。

```endpoint
GET /uploads/excel
```

#### 响应成功
```json
HTTP 200
```

#### 响应失败
```json
HTTP 500
```


## 统计模块


### 统计用户下载信息

统计系统中各用户上传文件共被下载了多少次，按下载次数从高到低进行排列

```endpoint
GET /stats/userdownloads
```

#### 响应成功
```json
[{
  "organization": "foxgis",
  "location": "beijing",
  "name": "foxgis",
  "username": "foxgis",
  "downloadNum": 12
},{
  "organization": "foxgis",
  "location": "beijing",
  "name": "jingsam",
  "username": "jingsam",
  "downloadNum": 8
}]
```


### 统计文件下载信息

统计系统中文件各被下载了多少次，按下载次数从高到低进行排列

```endpoint
GET /stats/filedownloads
```

#### 响应成功
```json
[{
  "name": "武汉交通游览图2016",
  "location": "武汉市",
  "year": 2015,
  "downloadNum": 12
},{
  "name": "辅助决策用图",
  "location": "北京市",
  "year": 2016,
  "downloadNum": 8
}]
```


### 统计各区域文件数量

根据制图区域对系统中上传文件进行统计

```endpoint
GET /stats/location
```

#### 响应成功
```json
[{
  "total": 89,
  "location": "武汉"
},{
  "total": 23,
  "location": "湖北"
},{
  "total": 17,
  "location": "北京"
},{
  "total": 3,
  "location": "南京"
}]
```


### 统计各年份文件数量

根据年份对系统中上传文件进行统计

```endpoint
GET /stats/year
```

#### 响应成功
```json
[{
  "total": 108,
  "year": "2016"
},{
  "total": 97,
  "year": "2015"
},{
  "total": 25,
  "year": "2013"
}]
```


### 统计文件主题词

按照主题词出现次数从大到小排列。

```endpoint
GET /stats/tags
```

#### 响应成功
```json
[{
  "total": 10,
  "tag": "旅游"
},{
  "total": 8,
  "tag": "政务"
},{
  "total": 2,
  "tag": "水利工程"
}]
```


## 模板模块

管理用户上传的样式模板。

字段信息：
字段 | 说明
--- | ---
name | 模板名称
owner | 所有者
scope | 共享范围，可能的取值有`private`和`public`，默认为`public`
style | 模板上传json文件的名称
thumb | 模板图片信息
replace | 替换字段
template_id | 模板ID


### 获取模板列表
```endpoint
GET /templates
```

#### 响应成功
```json
[{
  "updatedAt": "2016-08-18T02:53:58.982Z",
  "createdAt": "2016-08-17T07:07:15.215Z",
  "replace": "四川省",
  "name": "省级行政区划图",
  "owner": "foxgis",
  "style": "admin-prov-v8.json",
  "thumb": {
    "background-image": "url('http://192.9.105.205:8090/api/v1/templates/wanyanyan/rkXfofQc/image')"
  },
  "scope": "public",
  "template_id": "rJiXTF-9"
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取模板信息
```endpoint
GET /templates/{username}/{template_id}
```

#### 响应成功
```json
{
  "updatedAt": "2016-08-18T02:53:58.982Z",
  "createdAt": "2016-08-17T07:07:15.215Z",
  "replace": "四川省",
  "name": "省级行政区划图",
  "owner": "foxgis",
  "style": "admin-prov-v8.json",
  "thumb": {
    "background-image": "url('http://192.9.105.205:8090/api/v1/templates/wanyanyan/rkXfofQc/image')"
  },
  "scope": "public",
  "template_id": "rJiXTF-9"
}
```

#### 响应失败
```json
HTTP 404
```


### 创建模板

只有管理员账户才有权限创建模板，模板以json文件的形式上传，并需提供name和replace属性

```endpoint
POST /templates/{username}
```

#### 响应成功
```json
{
  "updatedAt": "2016-08-18T02:53:58.982Z",
  "createdAt": "2016-08-17T07:07:15.215Z",
  "replace": "四川省",
  "name": "省级行政区划图",
  "owner": "foxgis",
  "style": "admin-prov-v8.json",
  "thumb": {
    "background-image": "url('http://192.9.105.205:8090/api/v1/templates/wanyanyan/rkXfofQc/image')"
  },
  "scope": "public",
  "template_id": "rJiXTF-9"
}
```

#### 响应失败
```json
HTTP 500
```


### 更新模板信息

允许修改字段有`name`,`replace`和`styleJSON`。

```endpoint
PATCH /templates/{username}/{template_id}
```

#### 请求示例
```json
{
  "name": "newname",
  "replace": "newreplace",
  "styleJSON": "some style string"
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-08-18T02:53:58.982Z",
  "createdAt": "2016-08-17T07:07:15.215Z",
  "replace": "newreplace",
  "name": "newname",
  "owner": "foxgis",
  "style": "admin-prov-v8.json",
  "thumb": {
    "background-image": "url('http://192.9.105.205:8090/api/v1/templates/wanyanyan/rkXfofQc/image')"
  },
  "scope": "public",
  "template_id": "rJiXTF-9"
}
```

#### 响应失败
```json
HTTP 500
```


### 更新模板JSON

支持json文件的上传更新。

```endpoint
PUT /templates/{username}/{template_id}
```

#### 响应成功
```json
{
  "updatedAt": "2016-08-18T02:53:58.982Z",
  "createdAt": "2016-08-17T07:07:15.215Z",
  "replace": "newreplace",
  "name": "newname",
  "owner": "foxgis",
  "style": "admin-prov-v8.json",
  "thumb": {
    "background-image": "url('http://192.9.105.205:8090/api/v1/templates/wanyanyan/rkXfofQc/image')"
  },
  "scope": "public",
  "template_id": "rJiXTF-9"
}
```

#### 响应失败
```json
HTTP 500
```


### 删除模板
```endpoint
DELETE /templates/{username}/{template_id}
```

#### 响应成功
```json
HTTP 204
```


### 获取JSON

根据模板ID，获取用户最近一次更新的json文件信息

```endpoint
GET /templates/{username}/{template_id}/json
```

#### 响应成功
```json
HTTP 200
```


### 修改图片信息

支持用户上传图片以新建图片或更新已有的图片

```endpoint
POST /templates/{username}/{template_id}/image
```

#### 响应成功
```json
{
  "updatedAt": "2016-08-18T02:53:58.982Z",
  "createdAt": "2016-08-17T07:07:15.215Z",
  "replace": "newreplace",
  "name": "newname",
  "owner": "foxgis",
  "style": "admin-prov-v8.json",
  "thumb": {
    "background-image": "url('http://192.168.56.101:3000/api/v1/templates/foxgis/rJiXTF-9/image')"
  },
  "scope": "public",
  "template_id": "rJiXTF-9"
}
```

#### 响应失败
```json
HTTP 500
```


### 获取图片信息


```endpoint
GET /templates/{username}/{template_id}/image
```

#### 响应成功
```json
HTTP 200
```

#### 响应失败
```json
HTTP 500
```


## 数据集模块

管理用户上传的源数据。

字段信息：
字段 | 说明
--- | ---
dataset_id | 数据集ID
owner | 所有者
scope | 共享范围，可能的取值有`private`和`public`，默认为`public`
bounds | 数据集范围[WSEN]
center | 数据集中心点坐标[lon,lat]
format | 文件格式
filename | 上传文件名
filesize | 上传文件大小
createdAt | 数据集创建时间
updatedAt | 数据集更新时间


### 获取数据集列表1

获取系统中所有数据集信息，普通用户只能获取个人或其他用户公开的数据集内容。

```endpoint
GET /datasets
```

#### 响应成功
```json
[{
  "updatedAt": "2016-09-28T03:13:24.892Z",
  "createdAt": "2016-09-28T03:13:24.892Z",
  "owner": "foxgis",
  "filename": "wuhan",
  "format": ".json",
  "filesize": 3774,
  "center": [
    114.4384469476708,
    30.532440455939344
  ],
  "bounds": [
    114.35352938210184,
    30.463123047818996,
    114.52336451323976,
    30.601757864059692
  ],
  "scope": "public",
  "dataset_id": "Sk6US3dT"
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取数据集列表2

获取系统中某个用户{username}上传的数据集信息。

```endpoint
GET /datasets/{username}
```

#### 响应成功
```json
[{
  "updatedAt": "2016-09-28T03:13:24.892Z",
  "createdAt": "2016-09-28T03:13:24.892Z",
  "owner": "foxgis",
  "filename": "wuhan",
  "format": ".json",
  "filesize": 3774,
  "center": [
    114.4384469476708,
    30.532440455939344
  ],
  "bounds": [
    114.35352938210184,
    30.463123047818996,
    114.52336451323976,
    30.601757864059692
  ],
  "scope": "public",
  "dataset_id": "Sk6US3dT"
}]
```

#### 响应失败
```json
HTTP 500
```


### 获取数据集信息

获取数据集元数据信息。

```endpoint
GET /datasets/{username}/{dataset_id}
```

#### 响应成功
```json
{
  "updatedAt": "2016-09-28T03:13:24.892Z",
  "createdAt": "2016-09-28T03:13:24.892Z",
  "owner": "foxgis",
  "filename": "wuhan",
  "format": ".json",
  "filesize": 3774,
  "center": [
    114.4384469476708,
    30.532440455939344
  ],
  "bounds": [
    114.35352938210184,
    30.463123047818996,
    114.52336451323976,
    30.601757864059692
  ],
  "scope": "public",
  "dataset_id": "Sk6US3dT"
}
```

#### 响应失败
```json
HTTP 404
```


### 获取数据集内容


```endpoint
GET /datasets/{username}/{dataset_id}/raw
```

#### 响应成功
```json
{
  "type":"FeatureCollection",
  "features":[
    {
      "id":"e204756f7b62084b110a3d597c401473",
      "type":"Feature",
      "properties":{},
      "geometry":{
        "coordinates":[[
          [114.42497142166377,30.521336483038624],
          [114.425715609576,30.53351611253545],
          [114.42199467001552,30.542489599326572],
          [114.42645979748818,30.554025721092245],
          [114.40673881781731,30.557550373746736],
          [114.40004112660836,30.553064429971215],
          [114.39892484474024,30.547616933792582],
          [114.39594809309176,30.54505330040503],
          [114.40190159638871,30.53640053799296],
          [114.40264578430066,30.531272611296373],
          [114.39520390517981,30.538002959568487],
          [114.38366899254203,30.538643920796005],
          [114.37883177111348,30.537361994110825],
          [114.38627365023456,30.517810515888698],
          [114.3777154892453,30.523580213713828],
          [114.3728782678167,30.538002959568487],
          [114.36580848265152,30.546335125560333],
          [114.35352938210184,30.54986005747648],
          [114.35501775792619,30.554025721092245],
          [114.36841314034405,30.55690953733054],
          [114.37101779803635,30.56652163922054],
          [114.369157328256,30.578695601185373],
          [114.37697130133313,30.586703953256787],
          [114.3821806167179,30.588305544285646],
          [114.38478527441015,30.591508646951098],
          [114.39111087166316,30.589907108850667],
          [114.40041322056436,30.595031937612987],
          [114.40562253594913,30.601757864059692],
          [114.41455279089439,30.600156495351555],
          [114.42273885792747,30.600156495351555],
          [114.42422723375182,30.593110158586313],
          [114.41343650902627,30.589266486200387],
          [114.41604116671851,30.582539692737583],
          [114.42013420023517,30.576773498257467],
          [114.42906445518042,30.58093800644602],
          [114.42608770353195,30.573890272429722],
          [114.42608770353195,30.562997312423718],
          [114.43576214638938,30.558511620436477],
          [114.44543658924681,30.552103129329723],
          [114.4368784282575,30.550821380331797],
          [114.4368784282575,30.547616933792582],
          [114.44208774364233,30.542169131932525],
          [114.45287846836766,30.536080050505305],
          [114.45883197166467,30.531272611296373],
          [114.45659940792837,30.526785453384605],
          [114.44766915298311,30.530631601425853],
          [114.43725052221345,30.53287511746842],
          [114.43092492496078,30.533836608482716],
          [114.42497142166377,30.521336483038624]
        ]],
        "type":"Polygon"
      }
    },
    {
      "id":"aba8e9d98e045737a06efbb466822255",
      "type":"Feature",
      "properties":{},
      "geometry":{
        "coordinates":[[
          [114.49917840609629,30.508014684146318],
          [114.47610858082123,30.506732353360192],
          [114.46792251378793,30.506091181625834],
          [114.4660620440078,30.497434949466935],
          [114.47089926543646,30.485892108316847],
          [114.47982952038166,30.476272028116227],
          [114.48280627203019,30.464085228525718],
          [114.49248071488734,30.463123047818996],
          [114.50364353356889,30.469858113173174],
          [114.51034122477779,30.47274442717925],
          [114.51666682203074,30.485250799215763],
          [114.52224823137158,30.494549367556075],
          [114.52336451323976,30.506411768021508],
          [114.50289934565694,30.504808825474612],
          [114.49917840609629,30.508014684146318]
        ]],
        "type":"Polygon"
      }
    }
  ]
}
```

#### 响应失败
```json
HTTP 404
```


### 上传数据集

上传数据集，支持的格式有`geojson`。

```endpoint
POST /datasets/{username}
```


#### 响应成功
```json
{
  "updatedAt": "2016-09-28T03:13:24.892Z",
  "createdAt": "2016-09-28T03:13:24.892Z",
  "owner": "foxgis",
  "filename": "wuhan",
  "format": ".json",
  "filesize": 3774,
  "center": [
    114.4384469476708,
    30.532440455939344
  ],
  "bounds": [
    114.35352938210184,
    30.463123047818996,
    114.52336451323976,
    30.601757864059692
  ],
  "scope": "public",
  "dataset_id": "Sk6US3dT"
}
```

#### 响应失败
```json
HTTP 500
```


### 删除数据集

```endpoint
DELETE /datasets/{username}/{dataset_id}
```

#### 响应成功
```json
HTTP 204
```


### 更新数据集信息

可更新的字段有`scope`、`filename`、`tags`。

```endpoint
PATCH /datasets/{username}/{dataset_id}
```

#### 请求示例
```json
{
  "scope": "private"
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-09-28T03:13:24.892Z",
  "createdAt": "2016-09-28T03:13:24.892Z",
  "owner": "foxgis",
  "filename": "wuhan",
  "format": ".json",
  "filesize": 3774,
  "tags": [],
  "center": [
    114.4384469476708,
    30.532440455939344
  ],
  "bounds": [
    114.35352938210184,
    30.463123047818996,
    114.52336451323976,
    30.601757864059692
  ],
  "scope": "private",
  "dataset_id": "Sk6US3dT"
}
```

#### 响应失败
```json
HTTP 404
```


### 更新数据集内容

数据集内容可通过json形式。

```endpoint
PATCH /datasets/{username}/{dataset_id}/raw
```

#### 请求示例
```json
{
  "type":"FeatureCollection",
  "features":[
    {
      "id":"e204756f7b62084b110a3d597c401473",
      "type":"Feature",
      "properties":{},
      "geometry":{
        "coordinates":[[
          [114.42497142166377,30.521336483038624],
          [114.425715609576,30.53351611253545],
          [114.42199467001552,30.542489599326572],
          [114.42645979748818,30.554025721092245],
          [114.40673881781731,30.557550373746736],
          [114.40004112660836,30.553064429971215],
          [114.39892484474024,30.547616933792582],
          [114.39594809309176,30.54505330040503],
          [114.40190159638871,30.53640053799296],
          [114.40264578430066,30.531272611296373],
          [114.39520390517981,30.538002959568487],
          [114.38366899254203,30.538643920796005],
          [114.37883177111348,30.537361994110825],
          [114.38627365023456,30.517810515888698],
          [114.3777154892453,30.523580213713828],
          [114.3728782678167,30.538002959568487],
          [114.36580848265152,30.546335125560333],
          [114.35352938210184,30.54986005747648],
          [114.35501775792619,30.554025721092245],
          [114.36841314034405,30.55690953733054],
          [114.37101779803635,30.56652163922054],
          [114.369157328256,30.578695601185373],
          [114.37697130133313,30.586703953256787],
          [114.3821806167179,30.588305544285646],
          [114.38478527441015,30.591508646951098],
          [114.39111087166316,30.589907108850667],
          [114.40041322056436,30.595031937612987],
          [114.40562253594913,30.601757864059692],
          [114.41455279089439,30.600156495351555],
          [114.42273885792747,30.600156495351555],
          [114.42422723375182,30.593110158586313],
          [114.41343650902627,30.589266486200387],
          [114.41604116671851,30.582539692737583],
          [114.42013420023517,30.576773498257467],
          [114.42906445518042,30.58093800644602],
          [114.42608770353195,30.573890272429722],
          [114.42608770353195,30.562997312423718],
          [114.43576214638938,30.558511620436477],
          [114.44543658924681,30.552103129329723],
          [114.4368784282575,30.550821380331797],
          [114.4368784282575,30.547616933792582],
          [114.44208774364233,30.542169131932525],
          [114.45287846836766,30.536080050505305],
          [114.45883197166467,30.531272611296373],
          [114.45659940792837,30.526785453384605],
          [114.44766915298311,30.530631601425853],
          [114.43725052221345,30.53287511746842],
          [114.43092492496078,30.533836608482716],
          [114.42497142166377,30.521336483038624]
        ]],
        "type":"Polygon"
      }
    },
    {
      "id":"aba8e9d98e045737a06efbb466822255",
      "type":"Feature",
      "properties":{},
      "geometry":{
        "coordinates":[[
          [114.49917840609629,30.508014684146318],
          [114.47610858082123,30.506732353360192],
          [114.46792251378793,30.506091181625834],
          [114.4660620440078,30.497434949466935],
          [114.47089926543646,30.485892108316847],
          [114.47982952038166,30.476272028116227],
          [114.48280627203019,30.464085228525718],
          [114.49248071488734,30.463123047818996],
          [114.50364353356889,30.469858113173174],
          [114.51034122477779,30.47274442717925],
          [114.51666682203074,30.485250799215763],
          [114.52224823137158,30.494549367556075],
          [114.52336451323976,30.506411768021508],
          [114.50289934565694,30.504808825474612],
          [114.49917840609629,30.508014684146318]
        ]],
        "type":"Polygon"
      }
    }
  ]
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-09-28T03:13:24.892Z",
  "createdAt": "2016-09-28T03:13:24.892Z",
  "owner": "foxgis",
  "filename": "wuhan",
  "format": ".json",
  "filesize": 3774,
  "center": [
    114.4384469476708,
    30.532440455939344
  ],
  "bounds": [
    114.35352938210184,
    30.463123047818996,
    114.52336451323976,
    30.601757864059692
  ],
  "scope": "public",
  "dataset_id": "Sk6US3dT"
}
```

#### 响应失败
```json
HTTP 404
```