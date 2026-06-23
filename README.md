# Multi-Chart Infographic Reasoning 数据集 MindRecord 转换说明

## 1. 数据集简介

本数据集采用 HuggingFace Dataset 格式存储，样本信息保存在 `test.parquet` 文件中，每条样本包含：

* 图片路径（image_path）
* 图表类型（chart_types）
* 类别（category）
* 数据来源（source）
* 多个子问题（sub_question）
* 对应答案（answer）
* 问题类型（question_type）

单条样本示例如下：

```python
{
    'image_path': 'infographics/bee infographic/food & drink/which-burger-chain-do-you-prefer-shake-shack-five-guys-infographic.jpg',
    'chart_types': ['Card Chart'],
    'category': 'food & drink',
    'source': 'bee infographic',
    'sub_question 1': 'What is the absolute difference of total carbs (in grams) between the Double Shack Burger and the Cheeseburger?',
    'answer 1': '14',
    'question_type 1': 'Difference',
    'sub_question 2': "What is Double Shack Burger's calories?",
    'answer 2': '760',
    'question_type 2': 'Value',
    ...
}
```

---

## 2. 转换目标

为了方便 MindSpore 训练与分布式数据加载，推荐将原始 HuggingFace 数据集转换为 MindRecord 格式。

转换后每条样本保存如下字段：

| 字段名            | 类型     | 说明              |
| -------------- | ------ | --------------- |
| image          | bytes  | 图片二进制数据         |
| image_path     | string | 图片相对路径          |
| chart_types    | string | 图表类型列表(JSON字符串) |
| category       | string | 数据类别            |
| source         | string | 数据来源            |
| questions      | string | 问题列表(JSON字符串)   |
| answers        | string | 答案列表(JSON字符串)   |
| question_types | string | 问题类型列表(JSON字符串) |

转换后样例：

```python
{
    "image": bytes,
    "image_path": "...jpg",
    "chart_types": "[\"Card Chart\"]",
    "category": "food & drink",
    "source": "bee infographic",
    "questions": "[\"Q1\", \"Q2\", \"Q3\"]",
    "answers": "[\"A1\", \"A2\", \"A3\"]",
    "question_types": "[\"Difference\", \"Value\", \"Extrema\"]"
}
```

---

## 3. 环境依赖

安装以下依赖：

```bash
pip install pyarrow
pip install mindspore
```

或者：

```bash
pip install mindspore-gpu
```

根据实际硬件平台选择对应版本。

---

## 4. 数据目录结构

推荐目录结构如下：

```text
multi-chart-infographic-reasoning/
│
├── test.parquet
│
├── infographics/
│   ├── bee infographic/
│   ├── ...
│
└── convert_to_mindrecord.py
```

其中：

* `test.parquet` 保存元数据
* `infographics/` 保存实际图片文件

---

## 5. MindRecord 转换

运行转换脚本：

```bash
python convert_to_mindrecord.ipynb
```

转换完成后生成：

```text
test.mindrecord
test.mindrecord.db
```

其中：

* `.mindrecord` 为数据文件
* `.db` 为索引文件

训练时需要同时保留。

## 6. 适用场景

本 MindRecord 数据格式可直接用于：

* 多模态视觉问答（VQA）
* 图表理解（Chart Understanding）
* 信息图推理（Infographic Reasoning）
* 图文联合训练
* MindSpore Dataset Pipeline
* MindFormers 多模态模型训练
