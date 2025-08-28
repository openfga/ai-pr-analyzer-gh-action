# AI PR Analyzer GitHub Action

A GitHub Actions workflow that provides AI-powered code reviews using Claude via Amazon Bedrock. Automatically analyze pull requests and provide intelligent feedback when triggered by mentioning `@claude` in PR comments or descriptions.

## Key Features

- **AI-Powered Code Reviews**: Leverages Claude AI through Amazon Bedrock for intelligent code analysis
- **Inline Comments**: Provides line-specific feedback directly in GitHub pull requests
- **Customizable Instructions**: Support for custom review instructions tailored to your project
- **File Filtering**: Ability to exclude specific files or directories from review
- **Clean PR Experience**: Automatically hides outdated bot reviews to keep PRs focused
- **OIDC Authentication**: Secure AWS authentication without long-lived credentials
- **Trigger-Based**: Only runs when explicitly requested via `@claude` mentions

## Installation

Add this reusable workflow to your repository by creating a new workflow file (e.g., `.github/workflows/claude-code-review.yml`):

```yaml
name: Claude Code PR Review

on:
  issue_comment:
    types: [ created ]
  pull_request_review_comment:
    types: [ created ]
  pull_request_review:
    types: [ submitted ]

jobs:
  claude-review:
    uses: openfga/ai-pr-analyzer-gh-action/.github/workflows/claude-code-review.yml@main
```

## Usage

### Basic Usage

Once the workflow is set up, simply mention `@claude` in:
- Pull request descriptions
- PR comments
- Review comments

The AI will analyze the code changes and provide intelligent feedback.

### Advanced Configuration

#### Custom Review Instructions

```yaml
jobs:
  claude-review:
    uses: openfga/ai-pr-analyzer-gh-action/.github/workflows/claude-code-review.yml@main
    with:
      custom_review_instructions: |
        When reviewing code changes, please:
        - Focus on Go best practices and idioms
        - Check for proper error handling patterns
        - Verify context usage in long-running operations
        - Review goroutine and channel usage for race conditions
        - Check for proper resource cleanup (defer statements)
```

#### Project Context with CLAUDE.md

For comprehensive project context and review instructions, we recommend creating a `CLAUDE.md` file in your repository root instead of using `custom_review_instructions`. This approach provides better organization and maintainability of your AI assistant context.

## Examples

### Minimal Configuration

For a basic setup without any custom parameters:

```yaml
jobs:
  claude-review:
    uses: openfga/ai-pr-analyzer-gh-action/.github/workflows/claude-code-review.yml@main
```

#### Ignoring Files and Directories

By default, `vendor` and `dist` directories, and `package-lock.json` files are ignored from the review.

You can prevent the reviewer from reading specific files and directories by using the `disallowed_tools` parameter with the `Read()` syntax:

```yaml
jobs:
  claude-review:
    uses: openfga/ai-pr-analyzer-gh-action/.github/workflows/claude-code-review.yml@main
    with:
      disallowed_tools: |
        Read(build)
        Read(__pycache__)
```

You can also use wildcards with the star symbol:

```yaml
jobs:
  claude-review:
    uses: openfga/ai-pr-analyzer-gh-action/.github/workflows/claude-code-review.yml@main
    with:
      disallowed_tools: |
        Read(*_mock.go)
        Read(*.pb.go)
        Read(*.generated.ts)
```

For more information on using the `disallowed_tools` parameter, check the [Claude Code documentation](https://github.com/anthropics/claude-code-action/blob/main/docs/configuration.md#custom-tools).