# Multi-modal RAG 图文问答项目


> 本项目主要在本地A6000显卡环境下运行，核心依赖本地部署的 qwen3（文本生成）和 bge-m3（文本向量化）模型。

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
spark_multi_rag/
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
spark_multi_rag/
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
   # 本地推理（推荐本地部署时使用）
   LOCAL_API_KEY=anything
   LOCAL_BASE_URL=http://localhost:10002/v1
   LOCAL_TEXT_MODEL=qwen3           # 本地大模型，推荐 qwen3
   LOCAL_EMBEDDING_MODEL=bge-m3     # 本地embedding模型，推荐 bge-m3
   
   # 你也可以直接将 LOCAL_API_KEY、LOCAL_BASE_URL、LOCAL_TEXT_MODEL、LOCAL_EMBEDDING_MODEL
   # 换成硅基流动平台的API参数（如GUIJI_API_KEY、GUIJI_BASE_URL、Qwen/Qwen2.5-VL-32B-Instruct等），无需本地部署也可直接运行。
   ```
   > 推荐在A6000等高性能显卡环境下本地部署上述模型，确保推理效率。如无本地环境，也可直接用硅基流动API参数兼容运行。


## 硬件资源建议

本项目依赖的 MinerU PDF 解析工具对硬件有如下建议（详见 [MinerU官方文档](https://github.com/opendatalab/MinerU/blob/master/README_zh-CN.md)）：

| 资源类型 | 最低要求 | 推荐配置 |
| -------- | ----------------------------- | ----------------------------- |
| GPU      | Turing及以后架构，6G显存以上或Apple Silicon | Turing及以后架构，8G显存以上 |
| 内存     | 16G以上                        | 32G以上                        |

也支持CPU推理，但速度会慢一些。


## 多模态与Embedding模型API说明

本项目支持多模态大模型推理，推荐如下两种方式：

1. **硅基流动云API**
   - 多模态模型：如 Qwen/Qwen2.5-VL-32B-Instruct，API地址见 [硅基流动云平台](https://cloud.siliconflow.cn/i/FcjKykMn)
   - 图片视觉分析能力：pipeline_all.py 支持自动为图片补全caption，默认调用硅基流动Qwen/Qwen2.5-VL-32B-Instruct模型（无需本地部署，API Key见.env的GUIJI_API_KEY）。如需关闭图片caption补全，可在代码中设置 enable_image_caption=False。
   - Embedding模型：如 BAAI/bge-m3、重排序模型 BAAI/bge-reranker-v2-m3，均可免费调用
   - 只需在 `.env` 中配置 GUIJI_API_KEY、GUIJI_BASE_URL、GUIJI_TEXT_MODEL、GUIJI_FREE_TEXT_MODEL、LOCAL_EMBEDDING_MODEL 等参数即可

2. **本地xinference统一部署**
   - 支持本地多模态模型、embedding模型、mineru等一站式推理
   - 推荐A6000等高性能显卡环境
   - 参考 [xinference官方文档](https://inference.readthedocs.io/en/latest/) 部署

> 你可以根据自身条件选择云API或本地推理，硅基流动平台和xinference均支持多模态和embedding模型。

## 本地模型部署推荐

本项目推荐使用 [xinference](https://inference.readthedocs.io/en/latest/) 进行本地大模型和embedding模型的统一部署与管理。

请参考官方文档完成 xinference 的安装与模型加载，确保 `LOCAL_BASE_URL` 指向你的 xinference 服务地址。

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
