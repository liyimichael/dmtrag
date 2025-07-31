# Multi-modal RAG 图文问答项目

本项目旨在实现一个多模态 RAG（Retrieval-Augmented Generation）图文问答系统，支持从 PDF 文档自动解析、内容结构化、分块、向量化检索到大模型生成式问答的全流程。


## 主要文件说明

| 文件名 | 作用简介 |
| ------ | ------------------------------------------------------------ |
| pipeline_all.py | 一键式数据处理主脚本，自动完成 PDF 解析、内容结构化、分块合并 |
| rag_from_page_chunks.py | RAG 检索与问答主脚本，支持向量检索与大模型生成式问答 |
| get_text_embedding.py | 文本向量化，支持批量处理 |
| extract_json_array.py | 从大模型输出中提取 JSON 结构，保证结果可解析 |
| all_pdf_page_chunks.json | 所有 PDF 分页内容的合并结果，供 RAG 检索 |
| .env | 环境变量配置文件，存放 API 密钥、模型等参数 |
| caches/ | 预留目录（可选） |
| datas/ | 原始 PDF 文件及测试集存放目录 |
| data_base_json_content/ | PDF 解析后的内容列表（中间结果） |
| data_base_json_page_content/ | 分页后的内容（中间结果） |
| output/ | 其他输出文件目录 |


## 目录结构说明

### 运行前（初始目录结构）

```
multi_rag/
├── pipeline_all.py
├── rag_from_page_chunks.py
├── get_text_embedding.py
├── extract_json_array.py
├── .env
├── datas/                # 存放原始PDF和测试集
├── caches/               # 预留目录（可选）
├── output/               # 其他输出文件目录
└── ...
```

### 运行后（自动生成的中间及输出文件）

```
multi_rag/
├── data_base_json_content/         # PDF解析后的内容列表（中间结果，含json）
├── data_base_json_page_content/    # 分页内容（中间结果，含json）
├── all_pdf_page_chunks.json        # 合并后的所有分页内容（最终json）
├── output/*.json                   # 其他输出json
└── ...
```

> 说明：所有自动生成的json文件和中间目录已加入.gitignore，默认不会被上传到git。

- `pipeline_all.py`：一键式数据处理主脚本，自动完成 PDF 解析、内容结构化、分块合并。
- `rag_from_page_chunks.py`：RAG 检索与问答主脚本，支持向量检索与大模型生成式问答。
- `get_text_embedding.py`：文本向量化与缓存。
- `extract_json_array.py`：从大模型输出中提取 JSON 结构。
- `all_pdf_page_chunks.json`：所有 PDF 分页内容的合并结果，供 RAG 检索。
- 其他目录：
  - `datas/`：原始 PDF 及测试集。
  - `data_base_json_content/`：PDF 解析后的内容列表。
  - `data_base_json_page_content/`：分页后的内容。
  - `caches/`、`output/` 等：缓存与输出。

## 使用流程

1. **PDF 数据处理**
   
   运行 `pipeline_all.py`，自动完成：
   - PDF → 内容结构化（content_list.json）
   - 内容分页（page_content.json）
   - 分块合并（all_pdf_page_chunks.json）

   ```sh
   python pipeline_all.py
   ```

2. **RAG 检索与问答**

   运行 `rag_from_page_chunks.py`，加载分块内容，支持批量评测和交互式问答。

   ```sh
   python rag_from_page_chunks.py
   ```

   - 支持自动读取测试集并输出结构化结果。
   - 支持自定义问题检索与生成。

3. **环境变量配置**

   请在 `.env` 文件中配置以下内容（示例）：
   ```env
   LOCAL_API_KEY=你的API密钥
   LOCAL_BASE_URL=你的API地址
   LOCAL_TEXT_MODEL=大模型名称
   LOCAL_EMBEDDING_MODEL=嵌入模型名称
   ```

## 依赖安装

推荐使用 Python 3.8+，安装依赖：

```sh
pip install -r requirements.txt
```

## 主要特性
- 支持批量 PDF 自动解析与内容结构化
- 支持分页内容分块与向量化检索
- 支持大模型生成式问答，输出结构化 JSON
- 支持多线程批量评测

## 适用场景
- 金融、法律、科研等领域的 PDF 文档智能问答
- 多文档、多页内容的高效检索与分析

---
如有问题欢迎反馈与交流！
