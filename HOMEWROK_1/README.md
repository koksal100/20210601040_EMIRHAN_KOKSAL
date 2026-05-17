# ask

`ask` is a small Bash CLI tool that sends prompts to an OpenAI-compatible chat completions API.

## Requirements

- Bash
- `curl`
- `jq`

## Environment Variables

Set these exact variable names before running:

- `ASK_API_URL`
- `ASK_MODEL`
- `ASK_API_KEY`

Example:

```bash
export ASK_API_URL="https://api.groq.com/openai/v1/chat/completions"
export ASK_MODEL="llama-3.3-70b-versatile"
export ASK_API_KEY="your_api_key"
```

## Usage

All arguments are combined into one prompt:

```bash
./ask "Establishment dates of" "Turkey" "Azerbaijan" "Japan"
```

Arguments + command output:

```bash
./ask "Explain this shell command output:" "$(uname -a)"
```

Pipeline input:

```bash
cat script.sh | ./ask "Explain what this Bash script does in simple terms:"
```

Alias example:

```bash
alias ask-fix="./ask 'Correct any grammatical, spelling, or punctuation errors in the input text. Input text:'"
echo Rhythim | ask-fix
```

## Known Limitations

- The script expects a response in OpenAI-compatible chat completions format.
- Streaming responses are not implemented.
- If API responses are non-standard, error parsing may be limited.

## Open Source Design Notes

This project follows open source development principles:

- Simple and transparent implementation with minimal dependencies.
- Reproducible CLI behavior via environment-based configuration.
- Contributor-friendly structure (`README`, `CONTRIBUTING`, license file).
- Public collaboration flow based on Git history and reviewable commits.

## License Choice

This project uses the MIT License to keep reuse and contribution friction low.
MIT is short, widely understood, and compatible with many open source and commercial workflows.
