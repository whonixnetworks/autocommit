<p align="center">
  <img src="assets/autocommit.png" alt="logo" width="300"/>
</p>

AI-powered Git commit message generator using Ollama models. Automatically analyzes your code changes and generates meaningful commit messages.

## Features

- **AI-powered commit messages**: Uses Ollama models to generate meaningful commit messages
- **Smart context analysis**: Analyzes git diff, git status, and README.md
- **Interactive workflow**: Confirms before committing
- **Automatic staging**: Stages all changes automatically
- **Push support**: Optionally pushes to remote after commit
- **Model selection**: Interactive model selection
- **Fallback generation**: Works even if Ollama is unavailable

## Requirements

- Python 3.6+
- Git
- [Ollama](https://ollama.com) installed and running
- At least one Ollama model

## Installation

### Via pip (recommended)

```bash
pip install autocommit
```

### Via GitHub

```bash
git clone https://github.com/whonixnetworks/autocommit.git
cd autocommit
pip install -e .
```

### Via script

```bash
chmod +x autocommit
./autocommit --install
```

## Quick Start

```bash
# Make executable and run
chmod +x autocommit

# Basic usage (commits and pushes)
./autocommit

# Use specific model
./autocommit --model llama3.2:3b

# Commit without pushing
./autocommit --no-push

# Auto-accept (skip confirmation)
./autocommit -y
```

## Usage

```bash
autocommit [OPTIONS]

Options:
  -m, --model MODEL      Ollama model to use (default: llama3.2:3b)
  --no-push              Don't push after committing
  -r, --remote REMOTE    Remote to push to (default: origin)
  -b, --branch BRANCH    Branch to push (default: current branch)
  --ollama-url URL       Ollama API URL (default: http://127.0.0.1:11434)
  -y, --yes              Skip confirmation
  --install              Install to system PATH
  --select-model        Select default model interactively
  -h, --help            Show help
  -v, --version         Show version
```

## Configuration

Configuration is stored in `~/.config/autocommit/config.json`:

```json
{
  "default_model": "llama3.2:3b"
}
```

## `.autocommit-ignore`

Create a `.autocommit-ignore` file in your repository to exclude files from commit messages:

```
*.log
logs/
node_modules/
build/
dist/
.env
*.tmp
```

## How It Works

1. **Analyze changes**: Reads git diff, status, and README.md
2. **Generate message**: Sends context to Ollama model
3. **Confirm**: Shows message and asks for confirmation
4. **Stage & commit**: Stages changes and creates commit
5. **Push**: Optionally pushes to remote

## Supported Models

- llama3.2:3b (default)
- qwen2.5:3b
- qwen2.5-coder:7b-instruct
- Any local Ollama model

Install models with:
```bash
ollama pull llama3.2:3b
```

## License

MIT License - see [LICENSE](LICENSE) file.
