# 体育通识教学系统

基于知识图谱和双智能体调控的智能教学系统，实现结构化的教学路径生成与自适应学习调控。

## ✨ 特性

- 🗺️ **知识图谱构建**：从教学材料中自动抽取结构化知识，构建多层次的语义网络
- 🔗 **任务链生成**：基于拓扑排序生成G-P-F（Guidance-Presentation-Feedback）三阶段教学任务链
- 🤖 **双智能体调控**：Guiding Agent和Structural Agent协同工作，实现自适应学习路径调整
- 📊 **图增强RAG**：结合知识图谱检索和向量检索，生成高质量教学内容
- 📝 **证据化日志**：全程记录学习路径和调控决策，支持可复现实验

## 🏗️ 系统架构

系统采用模块化设计，包含5个核心模块：

```
┌─────────────────────────────────────────────────────────┐
│                    数据接入与语义切分                      │
│              (data_processor.py)                         │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                  知识图谱构建                             │
│              (kg_builder.py)                            │
│  • 三元组抽取  • 规范化去重  • Neo4j集成                │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                  任务链生成器                              │
│         (task_chain_generator.py)                        │
│  • 目标锚定  • 图遍历检索  • 拓扑排序  • G-P-F生成      │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│              RAG内容生成器 + 双智能体调控器                │
│  (rag_generator.py) + (dual_agent_controller.py)        │
│  • 图检索  • 文本检索  • 内容生成  • 路径调控            │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                    证据化日志                              │
│                  (logger.py)                              │
│              • 路径记录  • 决策追踪                       │
└─────────────────────────────────────────────────────────┘
```

## 📋 前置要求

- Python 3.8+
- Neo4j 5.x
- Qwen API密钥（用于大语言模型和向量模型）

## 🚀 快速开始

### 1. 克隆仓库

```bash
git clone <repository-url>
cd shuang_agent
```

### 2. 安装依赖

```bash
pip install -r requirements.txt
```

### 3. 配置

编辑 `config.py` 文件，设置以下配置：

```python
# Neo4j配置
NEO4J_URI = "bolt://localhost:7687"
NEO4J_USER = "neo4j"
NEO4J_PASSWORD = "your_password"

# Qwen API配置
DASHSCOPE_API_KEY = 'your_api_key'
```

### 4. 初始化目录

```bash
python init_dirs.py
```

### 5. 准备数据

将你的教学材料（课程文本、教案等）处理为JSONL格式，保存到 `data/chunks.jsonl`。格式如下：

```json
{"chunk_id": "chunk_1", "text": "教学内容...", "module_hint": "模块名", "objective_hint": "学习目标", "source": "来源"}
```

### 6. 运行系统

#### 完整流程

```bash
python main.py
```

#### 分步执行

```bash
# 构建知识图谱
python main.py --step kg

# 生成任务链
python main.py --step chain

# 运行仿真实验
python main.py --step sim
```

## 📁 项目结构

```
shuang_agent/
├── config.py                 # 系统配置
├── data_processor.py         # 数据接入与语义切分
├── kg_builder.py             # 知识图谱构建
├── task_chain_generator.py   # 任务链生成器
├── rag_generator.py          # RAG内容生成器
├── dual_agent_controller.py  # 双智能体调控器
├── logger.py                 # 证据化日志系统
├── simulation.py             # 对话仿真实验
├── main.py                   # 主程序
├── utils/                    # 工具模块
│   ├── api_client.py         # Qwen API客户端
│   └── embedder.py           # 向量模型封装
├── data/                     # 数据目录
│   ├── chunks.jsonl          # 文本块数据
│   ├── kg_export/            # 知识图谱导出
│   ├── task_chains/          # 任务链文件
│   ├── logs/                 # 日志文件
│   └── simulation/           # 仿真数据
└── requirements.txt          # 依赖列表
```

## 🔧 核心功能

### 知识图谱构建

系统支持5类节点和5类关系：

**节点类型**：
- `Module`：教学模块
- `KnowledgePoint`：知识点
- `CognitiveSkill`：认知技能
- `TeachingTask`：教学任务
- `TeachingActivity`：教学活动

**关系类型**：
- `BELONGS_TO_MODULE`：属于模块
- `SUPPORTS_UNDERSTANDING`：支撑理解
- `CONSTITUTES_SKILL`：构成技能
- `PREDECESSOR_TASK`：先修任务
- `DEPENDENT_TASK`：依赖任务

### 任务链生成

基于知识图谱生成结构化的教学任务链，每个任务包含：

- **Guidance（引导）**：澄清目标、激活先备知识
- **Presentation（讲解）**：讲解关键概念、提供示例
- **Feedback（反馈）**：检查理解、识别偏差

### 双智能体调控

**Guiding Agent**：
- 插入补桥节点，填补知识缺口
- 解释转场因果，恢复可学性
- 适用于知识误解和跳任务场景

**Structural Agent**：
- 回退主路径，确保目标清晰
- 简化提示，减少复杂度
- 适用于目标含糊和对话漂移场景

## 📊 输出文件

- `data/kg_export/nodes.csv`：知识图谱节点
- `data/kg_export/edges.csv`：知识图谱边
- `data/task_chains/*.json`：任务链文件
- `data/logs/path_log.jsonl`：路径调控日志
- `data/simulation/*.json`：仿真会话数据

## 🔬 实验设计

系统支持批量仿真实验，实验配置：

- **模块**：2个（Movement Systems, Health and Aerobic Training Design）
- **场景**：3种（ambiguous, misunderstanding, cross-task）
- **智能体**：2种（guiding, structural）
- **会话数**：每个智能体50次，共100个会话

## 📖 使用示例

### 构建知识图谱

```python
from kg_builder import KGBuilder
from data_processor import DataProcessor

# 加载chunks
processor = DataProcessor()
chunks = processor.load_chunks()

# 构建知识图谱
with KGBuilder() as kg_builder:
    kg_builder.build_from_chunks(chunks)
    kg_builder.export_graph()
```

### 生成任务链

```python
from task_chain_generator import TaskChainGenerator

with TaskChainGenerator() as chain_gen:
    chain = chain_gen.generate_chain(
        target_objective="掌握运动系统的核心内容",
        learner_state={"level": "beginner"}
    )
    chain_gen.save_chain(chain, "output/chain.json")
```

### 运行仿真实验

```python
from simulation import Simulation

sim = Simulation()
results = sim.simulate_session(
    module="Movement Systems",
    target_task="理解运动系统",
    scenario_trigger="misunderstanding",
    agent_type="guiding"
)
```

## 🛠️ 开发

### 添加新的节点类型

1. 在 `config.py` 的 `NODE_TYPES` 中添加
2. 更新 `kg_builder.py` 中的抽取逻辑

### 自定义智能体策略

编辑 `dual_agent_controller.py` 中的prompt和逻辑。

## 📝 许可证

本项目采用 MIT 许可证。

## 🙏 致谢

- 使用 [Qwen](https://github.com/QwenLM/Qwen) 作为大语言模型
- 使用 [Neo4j](https://neo4j.com/) 作为图数据库
- 使用 [LangChain](https://github.com/langchain-ai/langchain) 进行文本处理

## 📧 联系方式

如有问题或建议，请提交 Issue 或 Pull Request。

---

⭐ 如果这个项目对你有帮助，请给个Star！
