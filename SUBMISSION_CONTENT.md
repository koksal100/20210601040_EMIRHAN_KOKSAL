Name Surname: Emirhan Köksal
Student ID: 20210601040


git clone "https://github.com/koksal100/ask.git" "20210601040_EMIRHAN_KOKSAL"

Open Source Software Development Essay

Definition and Principles

Open source software is software whose source code is made publicly available so anyone can use, study, modify, and redistribute it under a defined license. Unlike closed-source software, open source development encourages shared ownership of technical progress and public review of implementation details.

Core principles include transparency, collaboration, freedom to modify, and community-driven improvement. Transparency allows users and developers to inspect code for quality, security, and correctness. Collaboration allows distributed contributors to fix bugs and add features faster than isolated teams. Freedom to modify makes software adaptable to different needs, from personal automation to enterprise integration. Community governance, whether formal or informal, helps projects evolve through shared standards and collective decision-making.

Open source also supports sustainability in software ecosystems. Public code can outlive a single company, enabling continuity through forks and new maintainers when project ownership changes. This reduces vendor lock-in and increases long-term accessibility.

Open Source Licensing

Open source licenses define legal permissions and obligations. Without a license, source code may be visible but not legally reusable. Three common licenses are GPL, MIT, and Apache:

- The GPL is a special rule for sharing software. It says that if you take someone's code and change it, you must show your new code to everyone. You cannot keep your changes a secret. This is good because it keeps the software free for all users in the future. However, it can be difficult for some companies. They might not want to share their private code, so they cannot easily mix GPL software with their own secret programs.

- The MIT license is a very easy and friendly rule for software. It allows you to use, change, and sell the code however you like. You can use it for free projects or for secret, paid programs. The only main rule is that you must keep the original creator's name and the license text in your code. Because there are almost no limits, many people and companies love to use this license.

- The Apache license is similar to the MIT license because it lets you use the code for any project. You can change it and sell it freely. However, it has an extra benefit: it includes a special rule about patents. This rule says that when someone shares their code, they also give you permission to use any patents related to that code. This makes it very safe and popular for big companies because they don't have to worry about legal problems or patent fights.

These rules (licenses) help people decide how to share their software. Some rules, like the GPL, want everyone to be open. If you use GPL code, your new project must be open and free for everyone too. Other rules, like MIT and Apache, are more relaxed. They want many people and companies to use the code. With these rules, you can use the code for free or sell it to make money. Teams choose a license based on their goal: do they want to keep everything free, or do they want to make it easy for everyone to use?

## A3. Community and Collaboration

Open source projects grow because many people work together. There are different roles for everyone. Maintainers lead the project, while contributors write the code. Some people help by checking the code (reviewers), writing guides (documentation), or testing the software to find mistakes. You don’t have to write code to help. Finding bugs or making the instructions better is also very important for a high-quality project.

Contributing to a project follows a simple path. First, you open an issue to find a problem and talk about it with the leaders. Then, you create a branch to make a separate copy of the code. You write the new code, check if it works, and update the instructions. After that, you submit a pull request to send your changes back to the leaders. They look at your work and might ask for small changes. When the code is perfect, they merge it into the main project.

Git is a very important tool for software. It lets you create a branch to try new ideas safely and keeps a history of all changes. Platforms like GitHub or GitLab make it easier for teams to work together. They use pull requests to check code and issue tracking to find problems. These tools help people work together even if they live in different time zones (different parts of the world with different times).

Good habits make software better and help new people join easily. You should use a consistent code style so the code is easy to read. It is also important to use testing to find mistakes. When you save your work, write clear commit messages (short notes that explain your changes). Keep your concise pull requests (small and simple requests to add your code), and always update the instructions. Projects with clear rules are easier to join and stay healthy for a long time.


Part B - CLI Project: `ask`

Why the ask Design is Good for Open Source

The ask tool is very simple and easy to understand. It is small and uses only two basic programs (curl and jq) to run. You can change the settings using environment variables (like ASK_API_URL or ASK_API_KEY) without changing the code itself. This makes the tool easy to use in different places.

The project is easy for everyone to use because it includes:

README.md: A guide for how to use the tool.
CONTRIBUTING.md: A list of rules for working together.
LICENSE: The legal rules for sharing.
ask script: The main code written in easy Bash logic.

This design makes it easy for new people to join, fix bugs, and share the tool.

License Choice and Justification

This project uses the MIT license for a few simple reasons. First, it is very short and everyone knows it. Second, it allows people to use this code for any project, even if they want to sell it later. Finally, it makes things easy because there are almost no complex legal rules. This helps more people join the project and use the code without any problems.

`ask` Script File Content

```bash
#!/usr/bin/env bash
set -euo pipefail

SYSTEM_PROMPT="You are a CLI tool. Output plain text only. No yapping. Keep the output concise."

require_env() {
  local name="$1"
  if [[ -z "${!name:-}" ]]; then
    printf 'Error: %s is not set.\n' "$name" >&2
    exit 1
  fi
}

require_cmd() {
  local name="$1"
  if ! command -v "$name" >/dev/null 2>&1; then
    printf 'Error: required dependency not found: %s\n' "$name" >&2
    exit 1
  fi
}

require_env "ASK_API_URL"
require_env "ASK_MODEL"
require_env "ASK_API_KEY"
require_cmd "curl"
require_cmd "jq"

args_prompt="${*:-}"
stdin_prompt=""

if [[ ! -t 0 ]]; then
  stdin_prompt="$(cat)"
fi

if [[ -n "$args_prompt" && -n "$stdin_prompt" ]]; then
  user_prompt="${args_prompt}"$'\n\n'"${stdin_prompt}"
elif [[ -n "$args_prompt" ]]; then
  user_prompt="$args_prompt"
elif [[ -n "$stdin_prompt" ]]; then
  user_prompt="$stdin_prompt"
else
  printf 'Usage: ask "your prompt"\n' >&2
  printf 'You can also pipe input: cat file.txt | ask "Explain this"\n' >&2
  exit 1
fi

payload="$(jq -n \
  --arg model "$ASK_MODEL" \
  --arg system "$SYSTEM_PROMPT" \
  --arg user "$user_prompt" \
  '{
    model: $model,
    messages: [
      { role: "system", content: $system },
      { role: "user", content: $user }
    ]
  }')"

response="$(curl -sS "$ASK_API_URL" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ASK_API_KEY" \
  -d "$payload")"

content="$(printf '%s' "$response" | jq -r '.choices[0].message.content // empty' 2>/dev/null || true)"

if [[ -n "$content" ]]; then
  printf '%s\n' "$content"
  exit 0
fi

error_message="$(printf '%s' "$response" | jq -r '.error.message // empty' 2>/dev/null || true)"
if [[ -n "$error_message" ]]; then
  printf 'API Error: %s\n' "$error_message" >&2
  exit 1
fi

printf 'Error: unexpected API response.\n' >&2
printf '%s\n' "$response" >&2
exit 1
```
