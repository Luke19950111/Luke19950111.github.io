---
title: JSON-Tree：把扁平化数据转换成树形结构JSON
date: 2019-07-01 11:13:57
tags: [tree, 数据转换]
categories: JavaScript
---
<center>树需要什么格式的数据呢？</center>
<!-- more --><br/>

# JSON-Tree安装

```
npm install --save @aximario/json-tree
```

# API

## construct(data, config)

- construct会自动过滤掉`null`和`undefined`的值
- data：数组，扁平化数据
- config：配置对象
    - id：数据里的id，string类型
    - pid：数据里的父id，string类型
    - children：生成结果中子节点的字段名，string类型

- 返回一个数组对象，里面可能包含多个树结构

## destruct(data, config)

- destruct会自动过滤掉`null`和`undefined`的值
- data：数组或者属性对象，结构化的数据
- config：配置对象
    - id：数据里的id，string类型，默认为`'id'`
    - pid：数据里添加的父id信息，string类型，默认为`'pid'`
    - children：生成结构中子节点的字段名，string类型，默认为`'children'`
    
- 返回一个数组对象，里面为展开的数据

# 用法

```
import { construct, destruct } from '@aximario/json-tree';

const data = [
  {id: 1, parent: 0, data: 'otherData'},
  {id: 2, parent: 1, data: 'otherData'},
  {id: 3, parent: 1, data: 'otherData'},
  {id: 4, parent: 3, data: 'otherData'},
  {id: 5, parent: 4, data: 'otherData'},
]
const result = construct(data, {
  id: 'id',
  pid: 'parent',
  children: 'kids'
})
console.log(JSON.stringify(result, null, '\t'))

//输出结果
[
	{
		"id": 1,
		"parent": 0,
		"data": "otherData",
		"kids": [
			{
				"id": 2,
				"parent": 1,
				"data": "otherData"
			},
			{
				"id": 3,
				"parent": 1,
				"data": "otherData",
				"kids": [
					{
						"id": 4,
						"parent": 3,
						"data": "otherData",
						"kids": [
							{
								"id": 5,
								"parent": 4,
								"data": "otherData"
							}
						]
					}
				]
			}
		]
	}
]
```

```
const data2 = [{
  "id": 1,
  "parent": 0,
  "data": "otherData",
  "kids": [{
      "id": 2,
      "parent": 1,
      "data": "otherData"
    },
    {
      "id": 3,
      "parent": 1,
      "data": "otherData",
      "kids": [{
        "id": 4,
        "parent": 3,
        "data": "otherData",
        "kids": [{
          "id": 5,
          "parent": 4,
          "data": "otherData"
        }]
      }]
    }
  ]
}]
const result2 = destruct(data2, {
  pid: 'parent',
  children: 'kids'
})
console.log(result2)

//输出结果
[
    {id: 1, parent: 0, data: "otherData"},
    {id: 2, parent: 1, data: "otherData"},
    {id: 3, parent: 1, data: "otherData"},
    {id: 4, parent: 3, data: "otherData"},
    {id: 5, parent: 4, data: "otherData"}
]
```

[参考](https://www.ctolib.com/ax-json-tree.html)

