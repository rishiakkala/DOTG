# DOTG 🧠📚  
## Dynamic Objective Test Generation Framework

DOTG is a **multi-agent MCQ generation and evaluation framework** that automatically builds structured knowledge bases, generates objective questions with intelligent distractors, adapts difficulty based on learner performance, and evaluates question quality using custom metrics.

The system combines **LLMs, adaptive learning logic, and multi-agent orchestration** to create an end-to-end intelligent assessment pipeline.

---

## ✨ Features

### Knowledge Base Construction
- From raw documents (PDF / DOC via parsing utilities)
- From direct text topics provided by the user

### Multi-Agent Workflow (crewai)
- Document parsing / topic research agent
- Knowledge structuring agent
- Question generation agent
- Distractor specialization agent
- User analysis & difficulty adaptation agent

### Interactive CLI Learning
- Multi-round sessions
- Timed responses
- Confidence ratings (1–5) per question

### Adaptive Difficulty
- Performance-aware difficulty adjustment
- Automatic switching between **easy**, **medium**, and **hard**

### Question Quality Evaluation
- Batch evaluation of saved question files
- Custom metrics:
  - **Distractor Plausibility Score (DPS)**
  - **Semantic Option Spread (SOS)**
- Text and JSON evaluation reports

---

## 📁 Project Structure

DOTG/
├── main.py # CLI entry point & orchestration
├── Agents.py # Multi-agent definitions
├── tasks.py # Task definitions
├── utils.py # Parsing & helper utilities
├── evaluation_metrics.py # DPS, SOS & reporting
├── evaluation_results.json # Sample evaluation output
├── knowledge_base.txt # Persisted knowledge base
├── sample_questions.txt # Example MCQs
├── userProfile.py # User profile & stats
├── user_profile_*.json # Stored user profiles
├── .env.example # Environment template
├── requirements.txt # Dependencies
└── README.md # Documentation


---

## ⚙️ Installation

### 1. Clone the Repository
```bash
git clone https://github.com/rishiakkala/DOTG.git
cd DOTG
2. Create & Activate Virtual Environment (Recommended)
python -m venv .venv
source .venv/bin/activate      # Linux / macOS
.venv\Scripts\activate         # Windows
3. Install Dependencies
pip install -r requirements.txt
4. Configure Environment Variables
cp .env.example .env
Edit .env:

OPENROUTER_API_KEY=your_openrouter_key_here
DOTG uses openrouter/google/gemma-2-9b-it as the default LLM.

▶️ Usage
Run the CLI:

python main.py
Menu Options
1. Start Learning Session
2. View Performance
3. Evaluate Questions (Comprehensive)
4. Quick Generate & Evaluate
5. Exit
🧑‍🏫 1. Start Learning Session
Launches a multi-round adaptive MCQ session.

Flow
Phase 1 – Knowledge Preparation

Parse document or research topic

Build structured knowledge base

Save to knowledge_base.txt

Phase 2 – Question Generation

Generate MCQs based on difficulty

Optionally save to:

questions_<difficulty>_<timestamp>.txt
Interactive Answering

Answer: A/B/C/D

Confidence: 1–5

Immediate feedback shown

Phase 3 – Adaptive Analysis

Performance analysis by agents

Difficulty adjusted for next round

Final Statistics

Accuracy

Response time

Skill level

Current difficulty

User profile is persisted automatically.

📊 2. View Performance
Displays:

Total questions answered

Overall accuracy

Average response time

Skill level

Topic-wise mastery

🧪 3. Evaluate Questions (Comprehensive)
Select any questions_*.txt file

Optional reference comparison

Generates:

Console report

<file>_evaluation.txt

<file>_evaluation.json

Metrics include DPS, SOS, and option diversity.

⚡ 4. Quick Generate & Evaluate
Rapid benchmarking mode:

Prepare knowledge base

Generate MCQs

Auto-evaluate latest question file

🤖 LLM Configuration
llm = LLM(
    model="openrouter/google/gemma-2-9b-it",
    api_key=OPENROUTER_API_KEY,
    base_url="https://openrouter.ai/api/v1"
)
You can swap models by editing main.py.

🧠 System Architecture
DOTG operates in three phases:

Knowledge Preparation

Question Generation

Adaptive Analysis

User performance is tracked via UserProfile:

Correctness

Time taken

Difficulty

Topic

Derived skill metrics

🛣️ Roadmap
Web-based UI

Enhanced PDF/Word parsing

Item-Response-Theory-style evaluation

Support for local LLMs
