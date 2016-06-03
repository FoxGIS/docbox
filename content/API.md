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
url方式(不推荐) | GET /{endpoint}?access_token={your_access_token}
cookie方式 | cookies["access_token"] = {your_access_token}
header方式 | headers['x-access-token'] = {your_access_token}


## 用户模块

用户有`username` `access_token` `scope` `is_verified` `name` `email` `phone` `location` `organization` `createdAt` `updatedAt`等字段。其中`access_token`只在用户注册和登录的时候返回。


### 用户注册

用户注册不需要提供`access_token`。用户注册时必须填写`username` `password`,可以选择性地完善自己的`name` `email` `phone` `location` `organization`信息。

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
  "phone": 123456789,
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
  "phone": 123456789,
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

获取用户信息不能得到`access_token`

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
  "phone": "12345678",
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

只允许更新用户信息中的`name` `scope` `email`, `phone`, `location`, `organization`属性

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
  "phone": "12345678",
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

用户登录不需要`access_token`，但登录成功后会生成新的`access_token`，已有的`access_token`会失效。

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
  "phone": "12345678",
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


## 样式模块

样式有`owner` `style_id` `scope` `tags` `version` `name` `metadata` `center` `zoom`   `bearing` `pitch` `sources` `sprite` `glyphs` `transition` `layers` `updatedAt`       `createdAt`等字段，其中`owner` `style_id` `scope` `tags` `version` `name` `updatedAt`  `createdAt`为样式文件描述字段。


### 搜索公开样式

搜索全局的公开样式。在`url`中用`query`指定搜索关键字与`page`,每次搜索返回20条结果。

```endpoint
GET /styles
```

#### 响应成功
```json
[{
    "updatedAt": "2016-05-05T11:06:22.957Z",
    "createdAt": "2016-05-05T10:06:22.957Z",
    "owner": "jingsam",
    "name": "Basic",
    "scope": "public",
    "tags": ["public"],
    "version": 8,
    "style_id": "BywXo5O-"
},{
    "updatedAt": "2016-05-05T11:06:00.957Z",
    "createdAt": "2016-05-05T10:06:00.957Z",
    "owner": "jingsam",
    "name": "Empty",
    "scope": "public",
    "tags": ["public"],
    "version": 8,
    "style_id": "SygXI5OZ"
}]
```

#### 响应失败
```json
{
  "errors": "内部错误"
}
```


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
{
  "errors": "内部错误"
}
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
  "draft": true,
  "style_id": "BywXo5O-"
}
```

#### 响应失败
```json
{
  "error": "格式不正确"
}
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
{
  "errors": "样式不存在"
}
```


### 更新某个样式
```endpoint
PATCH /styles/{username}/{style_id}
```

不允许更新`style_id` `owner` `createdAt` `updatedAt`字段

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
{
  "errors": "更新不存在的样式"
}
```


### 删除样式
```endpoint
DELETE /styles/{username}/{style_id}
```

#### 响应成功
```json
HTTP 204
```


## 瓦片集模块

瓦片集有`owner` `tileset_id` `scope` `tags` `createdAt` `updatedAt`以及`name` `description` `version` `tilejson` `attribution` `template` `legend` `formatter` `scheme` `tiles` `grids` `data` `minzoom` `maxzoom` `resolution` `bounds` `center` `filesize` `format` `vector_layers` 等字段。


### 搜索公开的瓦片集

搜索全局的公开瓦片集。在`url`中用`query`指定搜索关键字与`page`,每次搜索返回20条结果。

```endpoint
GET /tilesets/{username}
```

#### 响应成功
```json
[{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "tags": ["public"],
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
  "scope": "public",
  "tileset_id": "rJ6P9GlQ"
}]
```

#### 响应失败
```json
{
  "errors": "内部错误"
}
```


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
{
  "errors": "内部错误"
}
```


### 获取某个瓦片集
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
{
  "errors": "瓦片集不存在"
}
```


### 上传瓦片集
```endpoint
POST /tilesets/{username}
```

支持`mbtiles` `geojson` `shapefile`形式瓦片集的上传

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
{
  "errors": "内部错误"
}
```


### 更新瓦片集
```endpoint
PATCH /tilesets/{username}/{tileset_id}
```

只允许更新`scope` `tags` `name` `description` `vector_layers`字段

#### 请求示例
```json
{
  "tags": ["public"]
}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "tags": ["public"],
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
{
  "errors": "要更新的瓦片集不存在"
}
```


### 获取瓦片
```endpoint
GET /tilesets/{username}/{tileset_id}/{z}/{x}/{y}{@2x}.{format}
```

`format`必须为`vector.pbf`


### 删除瓦片集
```endpoint
DELETE /tilesets/{username}/{tileset_id}
```

#### 响应成功
```json
HTTP 204
```


## 字体模块

字体有`owner` `fontname` `scope` `createdAt` `updatedAt`等字段。


### 获取用户字体列表
```endpoint
GET /fonts/{username}
```

#### 响应成功
```json
[{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "fontname": "宋体",
  "owner": "jingsam",
  "scope": "public"
},{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "fontname": "楷体",
  "owner": "jingsam",
  "scope": "public"
}]
```

#### 响应失败
```json
{
  "errors": "内部错误"
}
```


### 获取上传字体状态
```endpoint
GET /fonts/{username}/{fontname}
```

#### 响应成功
```json
{
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "fontname": "宋体",
  "owner": "jingsam",
  "scope": "public"
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
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "fontname": "宋体",
  "owner": "jingsam",
  "scope": "public"
}
```

#### 响应失败
```json
{
  "errors": "内部错误"
}
```


### 修改字体状态

只允许修改`scope`字段

```endpoint
PATCH /fonts/{username}
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
  "updatedAt": "2016-05-23T05:51:34.710Z",
  "createdAt": "2016-05-23T05:51:33.199Z",
  "fontname": "宋体",
  "owner": "jingsam",
  "scope": "private"
}
```

#### 响应失败
```json
{
  "errors": "字体不存在"
}
```


### 获取字体
```endpoint
GET /fonts/{username}/{fontname}/{range}.pbf
```

### 删除字体
```endpoint
DELETE /fonts/{username}/{fontname}
```

#### 响应成功
```json
HTTP 204
```


## 符号库模块

符号库有`owner` `sprite_id` `scope` `name` `createdAt` `updatedAt`等描述性字段。


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


### 查看符号库状态
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


### 下载符号库

获取符号库时，`scale`可以指定为`@1x`或者`@2x`;`format`缺省时默认为`.json`,也可指定为`.json`或`.png`。

```endpoint
GET /sprites/{username}/{sprite_id}/sprite:scale(@[2]x)?.:format([\\w\\.]+)?
```


### 更新符号库

只允许更新`name` `scope`字段

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

#### 响应失败
```json
{
  "errors": "更新不存在的符号库"
}
```


### 删除符号库
```endpoint
DELETE /sprites/{username}/{style_id}
```

#### 响应成功
```json
HTTP 204
```


## 文件上传模块

文件上传有`owner` `upload_id` `tags` `scope` `name` `year` `location` `description` `size` `format` `updatedAt` `createdAt` `thumbnail` `mini_thumbnail`等字段，其中`thumbnail`用来储存上传图片文件的概览图，只在预览时用到。`mini_thumbnail`用来储存上传图片文件的微缩图,只在获取微缩图时用到


### 获取上传文件列表
```endpoint
GET /uploads/{username}
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
    "upload_id": "BJ7Sgvd-"
},{
    "updatedAt": "2016-05-05T11:44:56.201Z",
    "createdAt": "2016-05-05T09:00:56.101Z",
    "name": "beijing",
    "format": "mbtiles",
    "size": 102347,
    "owner": "jingsam",
    "tags": ["beijing", "2015"],
    "description": "a bigger file",
    "upload_id": "SygXI5OZ"
}]
```

#### 响应失败
```json
{
  "error": "内部错误"
}
```


### 获取文件状态
```endpoint
GET /uploads/{username}/{upload_id}
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
  "upload_id": "SygXI5OZ"
}
```

#### 响应失败
```json
{
  "error": "文件不存在"
}
```


### 更新文件状态
```endpoint
PATCH /uploads/{username}/{upload_id}
```

只能更新`tags` `name` `description` `scope` `year` `location`字段

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
  "description": "a new file"
}
```

#### 响应失败
```json
{
  "error": "更新的文件不存在"
}
```


### 下载文件
```endpoint
GET /uploads/{username}/{upload_id}/file
```


### 删除文件
```endpoint
DELETE /uploads/{username}/{upload_id}
```

#### 响应成功
```json
HTTP 204
```


### 上传文件

一次只允许上传一个文件，且文件大小要小于 200M。当上传格式为`jpg` `jpeg` `png`
`tif` `tiff`的图片时，会生成`png`格式的缩略图并保存。

```endpoint
POST /uploads/{username}
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
{
  "error": "内部错误"
}
```


### 获取概览图

上传图片的宽度大于1000像素时，其缩略图宽度为1000像素。
上传图片的宽度小于1000像素时，缩略图大小等于原图。

```endpoint
GET /uploads/{username}/{upload_id}/thumbnail
```


### 获取微缩图

返回原图300*300大小的微缩图

```endpoint
GET /uploads/{username}/{upload_id}/mini_thumbnail
```


### 搜索文件

搜索公开分享的上传文件

```endpoint
GET /uploads?search={keyword1}+{keyword2}
```

查询参数 | 说明
---|---
search | 搜索的关键字，多个关键字用`+`隔开，例如：`search=北京+上海`
limit | 返回结果的最大数目
skip | 跳过前面多少个查询结果
sort | 设置排序字段，降序在字端前加`-`,例如：`sort=-updatedAt`
