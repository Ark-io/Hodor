## SUMMARY

基于RDF Schema的定义与图数据库中的数据，实现将一个三元组转换为自然语言的表达形式，可以支持转换为问句和成数据。

## Motivation

实现三元组到自然语言的转换，是实现自然语言交互处理的基础功能，在整个项目的设计中，本部分功能需要具有如下设计功能：

- 实现残缺三元组转换为简单问句。例如：`<你, 要去做, ?> -> 你要去做什么？`
- 实现完备三元组转换为陈述句。例如：`<北京, 天气, 晴天> -> 北京天气是晴天`

## Detailed design

### 谓语类型

图数据库中记录的数据类型，大类可以区分为表达实体特征的属性关系，例如 `人` 的 `姓名` ，这种类型的谓词类型为`属性`(`attr`)；和表达实体之间关系的，例如`张三的哥哥是李四` ，这种类型的谓词类型是`关系`(`rel`)。

### 抽象层

本部分功能需要设计为一个独立运行的服务，可以选择调用外部的云服务实现功能。但对接的外部云服务需要设计一个抽象层来进行调用，需要明确具体NLP分析中所需要的服务，来实现具体的抽象层设计。

### API设计

#### 三元组转换为自然语言句子

`GET /transform/nl`
请求体示例模板：

```json
{
    "type": "description", // answer 问句， description 陈述句
    "triples": [ // 构成组件，数组中的每一个都代表一个三元组
        {
            "subject": {
                "type": "entity",
                "id":"0xaaaa",
                "value": "你"
            },
            "predicate": {
                "type": "children"
            },
            "object": {
                "type": "entity",
                "id":"0xbbbb"
            }
        },
        {
            "subject": {
                "type": "entity",
                "id":"0xbbbb"
            },
            "predicate": {
                "type": "attr",
                "value": "名字"
            },
            "object": {
                "type": "text",
                "value": "张三"
            }
        }
    ]
}
```

响应体示例：

```json
{
    "code": 0,
    "message": "success",
    "data": {
        "result": "你的姐姐的名字是张三。"
    }
}
```

