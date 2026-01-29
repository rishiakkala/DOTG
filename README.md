# DOTG 🧠📚  
Dynamic Objective Test Generation Framework

DOTG is a **multi-agent MCQ generation and evaluation system** that:
- Builds a structured knowledge base from raw documents or topics
- Generates objective questions (MCQs) with distractors using LLM agents
- Adapts difficulty over multiple rounds based on user performance
- Evaluates question quality using custom metrics and detailed reports

---

## Features

- **Knowledge base construction** from:
  - Arbitrary text documents (PDF/doc via parsing utility)
  - Direct text topics entered by the user

- **Multi-agent workflow** using `crewai`:
  - Document parsing / topic research
  - Knowledge structuring
  - Question generation
  - Distractor specialization
  - User analysis and difficulty adaptation

- **Interactive CLI learning sessions** with:
  - Multiple rounds
  - Timed responses
  - Confidence ratings (1–5) per question

- **Adaptive difficulty**:
  - Analyses performance history and suggestions from agents
  - Automatically shifts between `easy`, `medium`, and `hard`

- **Question quality evaluation**:
  - Comprehensive batch evaluation of saved question files
  - Metrics like Distractor Plausibility Score (DPS) and option diversity (SOS)
  - Text and JSON reports per question set

---

## Project Structure

Key files in this repository:

- `main.py` – Entry point, CLI menu, session orchestration
- `Agents.py` – Definitions of DOTG agents (parser, researcher, generator, adapter, etc.)
- `tasks.py` – Task definitions for knowledge prep, question generation, and adaptive analysis
- `utils.py` – Helper utilities (document parsing, cleaning questions, answer/reasoning extraction)
- `evaluation_metrics.py` – Evaluation functions and metrics (DPS, SOS, parsing question blocks, report printing)
- `evaluation_results.json` – Example stored evaluation results
- `knowledge_base.txt` – Persisted knowledge base from the last run
- `sample_questions.txt` – Example questions
- `userProfile.py` – User profile model (performance history, stats, persistence)
- `user_profile_*.json` – Sample stored user profiles
- `.env.example` – Example environment configuration
- `requirements.txt` – Python dependencies

---

## Installation

### 1. Clone the repository
```bash
git clone https://github.com/rishiakkala/DOTG.git
cd DOTG
```

### 2. Create and activate a virtual environment (recommended)
```bash
python -m venv .venv
source .venv/bin/activate   # On Windows: .venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```
Dependencies include `crewai`, `python-dotenv` and other support libraries.

### 4. Set environment variables
Copy the example env file and fill in your OpenRouter API key:

```bash
cp .env.example .env
```

Then edit `.env`:
```text
OPENROUTER_API_KEY=your_openrouter_key_here
```

DOTG uses `openrouter/google/gemma-2-9b-it` as the LLM via OpenRouter.

---

## Usage

### Run the CLI:
```bash
python main.py
```

You will see a menu:

1. Start Learning Session
2. View Performance
3. Evaluate Questions (Comprehensive)
4. Quick Generate & Evaluate
5. Exit

---

## Menu Options

### 1. Start Learning Session
This launches a multi-round interactive MCQ session with adaptive difficulty.

**Steps (prompted by the CLI):**

1. **Enter Name/ID** – any identifier for the user (used to persist profile)

2. **Choose study source:**
   - `document` – provide a file path
   - `topic` – provide a textual topic

3. **Set Questions per round** (default is 3)

**Flow:**

#### Phase 1 – Knowledge Preparation
- Parses the given document or researches the given topic
- Builds and saves a knowledge base to `knowledge_base.txt`

#### Phase 2 – Question Generation
- Generates MCQs at the current difficulty level
- Optionally saves them to timestamped `questions_<difficulty>_<timestamp>.txt` files

#### Interactive answering
For each question:
- Question and options are displayed (cleaned for CLI)
- User inputs:
  - **Answer**: A/B/C/D
  - **Confidence**: integer 1–5 (default 3)

The system:
- Records correctness, time taken, difficulty, topic and confidence
- Shows immediate feedback (Correct/Incorrect with time spent)

#### Round summary & explanations
- Prints per-round score and percentage
- For each question:
  - Shows user answer, correct answer, and an explanation extracted from the question block

#### Phase 3 – Adaptive Analysis
- Runs multi-agent analysis on the user profile
- Adjusts difficulty (easy/medium/hard) for the next round
- Three rounds are run by default, with a pause between rounds

#### Final session statistics
- Total questions answered
- Overall accuracy
- Average response time
- Current skill level (numeric)
- Current difficulty level

User profile is persisted and updated after each session.

---

### 2. View Performance
Option 2 loads an existing UserProfile and prints a performance summary:

- Total questions answered
- Overall accuracy
- Average response time
- Skill level
- Current difficulty
- **Topic-wise mastery**: per-topic accuracy and counts (correct/total)

If no history exists for the given ID, it informs you to run a session first.

---

### 3. Evaluate Questions (Comprehensive)
This mode evaluates previously generated question files in bulk.

**Workflow:**
1. Lists all files matching `questions_*.txt` in the current directory
2. You select one by index
3. Optionally provide a reference questions file for comparison
4. Uses:
   - `evaluate_question_set_comprehensive`
   - `print_comprehensive_report`
   - Knowledge base from `knowledge_base.txt` if available

**Outputs:**
- Detailed console report
- `<questions_file>_evaluation.txt` – human-readable evaluation report
- `<questions_file>_evaluation.json` – JSON metrics and counts

**Metrics include:**
- Question-level parsing and validation
- **DPS**: Distractor Plausibility Score
- **SOS**: Option diversity and related stats

---

### 4. Quick Generate & Evaluate
Designed for rapid benchmarking of question quality without an interactive learning session.

**Prompts:**
- Study source: `document` or `topic`
- Input path or topic
- Difficulty: `easy` / `medium` / `hard` (default medium)
- Number of questions to generate

**Steps:**
1. Prepare knowledge base (Phase 1)
2. Generate questions (Phase 2) and save to a timestamped file
3. Automatically find the latest `questions_*.txt` file
4. Run comprehensive evaluation on it (same as Option 3)

---

### 5. Exit
Prints a short motivational message and exits the program.

---

## Environment & LLM Configuration

DOTG currently uses `openrouter/google/gemma-2-9b-it` via `crewai.LLM`:

```python
llm = LLM(
    model="openrouter/google/gemma-2-9b-it",
    api_key=OPENROUTER_API_KEY,
    base_url="https://openrouter.ai/api/v1"
)
```

**Make sure:**
- `OPENROUTER_API_KEY` is correctly set in `.env`
- Your OpenRouter account has access to this model

You can swap this model for another compatible OpenRouter LLM by editing `main.py`.

---

## Implementation Details (High Level)

DOTG is structured around three main phases:

### Phase 1 – Knowledge Preparation (`phase1_knowledge_prep`)
- Parses documents or uses topic text
- Invokes document/topic agents and knowledge structurer agents
- Persists the resulting knowledge base

### Phase 2 – Question Generation (`phase2_question_generation`)
- Creates question-generation tasks based on difficulty and count
- Uses question generator and distractor specialist agents
- Optionally saves raw question text for later evaluation

### Phase 3 – Adaptive Analysis (`phase3_adaptive_analysis`)
- Uses user-analyzer and difficulty-adapter agents
- Suggests difficulty changes based on performance trends and confidence

**User performance** is tracked in `UserProfile`, which stores:
- Per-question correctness and response time
- Difficulty and topic of each attempt
- Derived stats like accuracy rate and skill level

---

## Roadmap / Potential Improvements

- Add a web UI on top of the existing CLI
- Support more input formats for documents (PDF/Word integration)
- Enhance evaluation metrics with item-response-theory–style analysis
- Plug in different LLM backends or local models

---

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

---

## License

[Add your license information here]

---

## Contact

For questions or issues, please open an issue on the [GitHub repository](https://github.com/rishiakkala/DOTG).

---

**Happy Learning! 🎓✨**
