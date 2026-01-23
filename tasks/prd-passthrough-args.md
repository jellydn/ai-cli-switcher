# PRD: Passthrough Arguments

## Introduction

Add passthrough argument support to ai-cli-switcher so users can pass additional arguments directly to the underlying AI tool. For example, `ai o /slop` will launch opencode with the `/slop` command, and `ai claude --help` will show Claude's help instead of ai-switcher's help.

## Goals

- Pass any arguments after the tool name/alias directly to the underlying tool
- Support both implicit passthrough (`ai o /slop`) and explicit separator (`ai o -- /slop`)
- Allow passthrough args after fuzzy selection via `ai -- /slop` syntax
- Prompt for optional args after interactive fuzzy selection
- Remove validation restrictions on passthrough args (pass raw to tool)
- Update help text with passthrough examples

## User Stories

### US-001: Remove validation for passthrough arguments
**Description:** As a user, I want to pass any CLI arguments to the underlying tool without validation errors.

**Acceptance Criteria:**
- [ ] Remove or bypass `validateArguments()` for passthrough args
- [ ] Args like `/slop`, `--model=gpt-4`, `-v` pass through unchanged
- [ ] Special characters in args don't cause rejection
- [ ] Typecheck passes

### US-002: Implicit passthrough for direct tool invocation
**Description:** As a user, I want `ai o /slop` to launch opencode with `/slop` without needing `--` separator.

**Acceptance Criteria:**
- [ ] `ai claude /slop` launches `claude /slop`
- [ ] `ai o --help` launches `opencode --help`
- [ ] `ai amp -p "prompt"` launches `amp -p "prompt"`
- [ ] Multiple args work: `ai claude --model sonnet --help`
- [ ] Typecheck passes

### US-003: Explicit separator passthrough
**Description:** As a user, I want to use `--` separator for explicit passthrough when needed.

**Acceptance Criteria:**
- [ ] `ai o -- /slop` launches `opencode /slop`
- [ ] `ai claude -- --help` launches `claude --help`
- [ ] Existing `--` behavior preserved for edge cases
- [ ] Typecheck passes

### US-004: Passthrough after fuzzy selection with separator
**Description:** As a user, I want to select a tool via fuzzy search and pass args using `ai -- /slop`.

**Acceptance Criteria:**
- [ ] `ai -- /slop` opens fuzzy selector, then passes `/slop` to selected tool
- [ ] `ai -- --help` opens fuzzy selector, then passes `--help` to selected tool
- [ ] Multiple args after `--` all pass through
- [ ] Typecheck passes

### US-005: Optional args prompt after interactive selection
**Description:** As a user, I want to optionally provide arguments after selecting a tool interactively.

**Acceptance Criteria:**
- [ ] After fuzzy selection, prompt: "Arguments (optional, press Enter to skip):"
- [ ] Empty input launches tool without args
- [ ] Entered args pass directly to the tool
- [ ] Prompt only appears for non-template tools (templates already prompt for `$@`)
- [ ] Typecheck passes

### US-006: Update help text with passthrough examples
**Description:** As a user, I want the help text to show how to use passthrough arguments.

**Acceptance Criteria:**
- [ ] Help shows example: `ai claude /slop` - Pass /slop to claude
- [ ] Help shows example: `ai o --help` - Show opencode's help
- [ ] Examples section clearly demonstrates passthrough usage
- [ ] Typecheck passes

## Functional Requirements

- FR-1: Arguments after tool name/alias pass directly to the underlying tool command
- FR-2: No validation applied to passthrough arguments (pass raw)
- FR-3: `--` separator continues to work for explicit passthrough
- FR-4: `ai -- <args>` opens fuzzy selector then passes args to selection
- FR-5: Interactive fuzzy selection prompts for optional arguments
- FR-6: Template tools continue using existing `$@` prompt behavior
- FR-7: Help text updated with passthrough examples

## Non-Goals

- No argument parsing or interpretation by ai-switcher
- No special handling for specific tool flags
- No persistent "last used args" memory
- No auto-completion for tool-specific arguments

## Technical Considerations

- Modify `launchTool()` to skip `validateArguments()` for passthrough
- Update argument parsing logic in `main()` to handle implicit passthrough
- Reuse existing `promptForInput()` for optional args prompt
- Keep stdin passthrough (`$@`) behavior unchanged for templates

## Success Metrics

- `ai o /slop` successfully launches opencode with /slop command
- All existing functionality continues to work
- No validation errors for valid CLI arguments
- Help text clearly explains passthrough usage

## Open Questions

- None - requirements are clear from user input
