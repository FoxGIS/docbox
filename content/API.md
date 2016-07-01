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
createdAt | 用户创建时间
updatedAt | 用户信息更新时间


### 用户注册

用户注册不需要提供`access_token`。用户注册时必须填写`username`、`password`,可以选择性地完善自己的`name`、`location`、`organization`、`position`、`telephone`、`mobile`、`email`信息。

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


### 更新用户信息

只允许更新用户信息中的`scope`、`name`、`location`、`organization`、`postition`、`telephone`、`mobile`、`email`属性

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

获取地图样式中的瓦片数据

```endpoint
GET /styles/{username}/{style_id}/{z}/{x}/{y}{@2x}.{format}
```


### 获取地图预览

获取设定范围的地图预览

```endpoint
GET /styles/{username}/{style_id}/thumbnail
```


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


### 获取瓦片集列表
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

上传数据，系统根据上传的数据自动地转换为瓦片数据，支持的格式有`mbtiles`、`geojson`、`shapefile`。

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


### 瓦片集预览

获取设定范围内的瓦片预览

```endpoint
GET /tilesets/{username}/{tileset_id}/thumbnail
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
createdAt | 符号库创建时间
updatedAt | 符号库更新时间


### 获取符号库列表
```endpoint
GET /sprites/{username}
```

#### 响应成功
```json
[{
  "updatedAt": "2016-05-05T10:29:00.845Z",
  "createdAt": "2016-05-05T10:29:00.845Z",
  "owner": "jingsam",
  "name": "Sprite",
  "scope": "private",
  "sprite_id": "BkgH_lsub"
},{
  "updatedAt": "2016-05-05T10:00:00.845Z",
  "createdAt": "2016-05-05T10:00:00.845Z",
  "owner": "jingsam",
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

允许更新的字段有`name`，`scope`。

```endpoint
PATCH /sprites/{username}/{sprite_id}
```

#### 请求示例
```json
{
  "scope": "public",
  "name": "newname"
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-05T10:37:17.182Z",
  "createdAt": "2016-05-05T10:29:00.845Z",
  "owner": "jingsam",
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
