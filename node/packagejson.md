# packagejson文件详解

###目录

---
```
1. 概述
2. scripts字段
3. dependencies字段，devDependencies字段
4. peerDependencies
5. bin字段
6. main字段
7. config 字段
8. 其他
9. browser字段
10. engines 字段
11. man字段
12. preferGlobal字段
13. style字段
```
###1. 概述

---
每个项目的根目录下面，一般都有一个package.json文件，定义了这个项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等元数据）。npm install命令根据这个配置文件，自动下载所需的模块，也就是配置项目所需的运行和开发环境。

下面是一个最简单的package.json文件，只定义两项元数据：项目名称和项目版本。
```
{
  "name" : "xxx",
  "version" : "0.0.0",
}
```
package.json文件就是一个JSON对象，该对象的每一个成员就是当前项目的一项设置。比如name就是项目名称，version是版本（遵守“大版本.次要版本.小版本”的格式）。

下面是一个更完整的package.json文件。
```
{
	"name": "Hello World",
	"version": "0.0.1",
	"author": "张三",
	"description": "第一个node.js程序",
	"keywords":["node.js","javascript"],
	"repository": {
		"type": "git",
		"url": "https://path/to/url"
	},
	"license":"MIT",
	"engines": {"node": "0.10.x"},
	"bugs":{"url":"http://path/to/bug","email":"bug@example.com"},
	"contributors":[{"name":"李四","email":"lisi@example.com"}],
	"scripts": {
		"start": "node index.js"
	},
	"dependencies": {
		"express": "latest",
		"mongoose": "~3.8.3",
		"handlebars-runtime": "~1.0.12",
		"express3-handlebars": "~0.5.0",
		"MD5": "~1.2.0"
	},
	"devDependencies": {
		"bower": "~1.2.8",
		"grunt": "~0.4.1",
		"grunt-contrib-concat": "~0.3.0",
		"grunt-contrib-jshint": "~0.7.2",
		"grunt-contrib-uglify": "~0.2.7",
		"grunt-contrib-clean": "~0.5.0",
		"browserify": "2.36.1",
		"grunt-browserify": "~1.3.0",
	}
}
```
下面详细解释package.json文件的各个字段。
