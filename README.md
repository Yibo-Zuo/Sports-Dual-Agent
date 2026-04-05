
# General Physical Education Teaching System

An intelligent teaching system based on a knowledge graph and dual-agent regulation, designed to support structured instructional path generation and adaptive learning regulation.

## ✨ Features

- 🗺️ **Knowledge Graph Construction**: Automatically extracts structured knowledge from teaching materials and builds a multi-level semantic network
- 🔗 **Task Chain Generation**: Generates a three-stage G-P-F (Guidance–Presentation–Feedback) instructional task chain based on topological sorting
- 🤖 **Dual-Agent Regulation**: Guiding Agent and Structural Agent work collaboratively to enable adaptive learning path adjustment
- 📊 **Graph-Enhanced RAG**: Combines knowledge graph retrieval and vector retrieval to generate high-quality instructional content
- 📝 **Evidence-Based Logging**: Records the full learning path and regulation decisions to support reproducible experiments

## 🏗️ System Architecture

The system adopts a modular design with five core modules:

```text
┌─────────────────────────────────────────────────────────┐
│           Data Ingestion and Semantic Segmentation       │
│                  (data_processor.py)                     │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│               Knowledge Graph Construction               │
│                    (kg_builder.py)                      │
│   • Triple Extraction  • Normalization & Deduplication  │
│   • Neo4j Integration                                 │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                 Task Chain Generator                     │
│              (task_chain_generator.py)                  │
│   • Goal Anchoring  • Graph Traversal Retrieval         │
│   • Topological Sorting  • G-P-F Generation             │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│        RAG Content Generator + Dual-Agent Controller    │
│     (rag_generator.py) + (dual_agent_controller.py)     │
│   • Graph Retrieval  • Text Retrieval  • Content        │
│     Generation  • Path Regulation                       │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                 Evidence-Based Logging                   │
│                      (logger.py)                        │
│          • Path Recording  • Decision Tracking          │
└─────────────────────────────────────────────────────────┘
````

## 📋 Prerequisites

* Python 3.8+
* Neo4j 5.x
* Qwen API key (for the large language model and embedding model)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone <repository-url>
cd shuang_agent
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Configuration

Edit the `config.py` file and set the following configurations:

```python
# Neo4j configuration
NEO4J_URI = "bolt://localhost:7687"
NEO4J_USER = "neo4j"
NEO4J_PASSWORD = "your_password"

# Qwen API configuration
DASHSCOPE_API_KEY = 'your_api_key'
```

### 4. Initialize Directories

```bash
python init_dirs.py
```

### 5. Prepare Data

Process your teaching materials (course texts, lesson plans, etc.) into JSONL format and save them to `data/chunks.jsonl`. The format is as follows:

```json
{"chunk_id": "chunk_1", "text": "instructional content...", "module_hint": "module name", "objective_hint": "learning objective", "source": "source"}
```

### 6. Run the System

#### Full Pipeline

```bash
python main.py
```

#### Step-by-Step Execution

```bash
# Build the knowledge graph
python main.py --step kg

# Generate the task chain
python main.py --step chain

# Run simulation experiments
python main.py --step sim
```

## 🔧 Core Functions

### Knowledge Graph Construction

The system supports 5 node types and 5 relation types:

**Node Types**:

* `Module`: Teaching module
* `KnowledgePoint`: Knowledge point
* `CognitiveSkill`: Cognitive skill
* `TeachingTask`: Teaching task
* `TeachingActivity`: Teaching activity

**Relation Types**:

* `BELONGS_TO_MODULE`: Belongs to a module
* `SUPPORTS_UNDERSTANDING`: Supports understanding
* `CONSTITUTES_SKILL`: Constitutes a skill
* `PREDECESSOR_TASK`: Prerequisite task
* `DEPENDENT_TASK`: Dependent task

### Task Chain Generation

The system generates a structured instructional task chain based on the knowledge graph. Each task includes:

* **Guidance**: Clarifies goals and activates prior knowledge
* **Presentation**: Explains key concepts and provides examples
* **Feedback**: Checks understanding and identifies deviations

### Dual-Agent Regulation

**Guiding Agent**:

* Inserts bridging nodes to fill knowledge gaps
* Explains transition logic to restore learnability
* Suitable for misunderstanding and task-jumping scenarios

**Structural Agent**:

* Returns to the main path to ensure goal clarity
* Simplifies prompts to reduce complexity
* Suitable for ambiguous goals and dialogue drift scenarios

## 📊 Output Files

* `data/kg_export/nodes.csv`: Knowledge graph nodes
* `data/kg_export/edges.csv`: Knowledge graph edges
* `data/task_chains/*.json`: Task chain files
* `data/logs/path_log.jsonl`: Path regulation logs
* `data/simulation/*.json`: Simulated session data

## 🔬 Experimental Design

The system supports batch simulation experiments with the following setup:

* **Modules**: 2 (Movement Systems; Health and Aerobic Training Design)
* **Scenarios**: 3 (ambiguous, misunderstanding, cross-task)
* **Agents**: 2 (guiding, structural)
* **Sessions**: 50 per agent, 100 sessions in total

## 📖 Usage Examples

### Build the Knowledge Graph

```python
from kg_builder import KGBuilder
from data_processor import DataProcessor

# Load chunks
processor = DataProcessor()
chunks = processor.load_chunks()

# Build the knowledge graph
with KGBuilder() as kg_builder:
    kg_builder.build_from_chunks(chunks)
    kg_builder.export_graph()
```

### Generate a Task Chain

```python
from task_chain_generator import TaskChainGenerator

with TaskChainGenerator() as chain_gen:
    chain = chain_gen.generate_chain(
        target_objective="Master the core content of movement systems",
        learner_state={"level": "beginner"}
    )
    chain_gen.save_chain(chain, "output/chain.json")
```

### Run a Simulation Experiment

```python
from simulation import Simulation

sim = Simulation()
results = sim.simulate_session(
    module="Movement Systems",
    target_task="Understand movement systems",
    scenario_trigger="misunderstanding",
    agent_type="guiding"
)
```

## 🛠️ Development

### Add a New Node Type

1. Add it to `NODE_TYPES` in `config.py`
2. Update the extraction logic in `kg_builder.py`

### Customize Agent Strategies

Edit the prompts and control logic in `dual_agent_controller.py`.

## 📝 License

This project is licensed under the MIT License.
