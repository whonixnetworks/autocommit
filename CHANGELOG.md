# Changelog

All notable changes to this project will be documented in this file.

## [1.2.5] - 2025-04-15

### Added
- Initial public release
- AI-powered commit message generation using Ollama models
- Smart context analysis (git diff, status, README.md)
- Interactive confirmation workflow
- Automatic staging and commit
- Push support to remote repositories
- Model selection and configuration
- Fallback commit message generation
- `.autocommit-ignore` file support for excluding files from commit messages
- 3-part commit message format (heading, subheading, body)

### Supported Models
- llama3.2:3b (default)
- qwen2.5:3b
- qwen2.5-coder:7b-instruct
- Any Ollama model available locally