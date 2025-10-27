# TTY Output Handling

When you encounter the "TY=not a tty" error or truncated output from terminal commands, follow this pattern:

## Detection
If you see output that contains:
- `TY=not a tty`
- Truncated command output
- Missing expected test results or command output

## Solution
1. Rerun the command with output redirection to a file
2. Read the file to get the complete output
3. Clean up the temporary file

## Example Pattern
```bash
# Original command that failed or was truncated
uv run python -m pytest test/unit/test_dto_auth.py -v

# Redirect to file and read
uv run python -m pytest test/unit/test_dto_auth.py -v > test_output.txt 2>&1
# Then read the file
cat test_output.txt
# Clean up
rm test_output.txt
```

## When to Apply
- Any time you see "TY=not a tty" in command output
- When test results are missing or truncated
- When command output appears incomplete
- When you need to capture both stdout and stderr

## File Naming
Use descriptive temporary file names like:
- `test_output.txt` for test results
- `build_output.txt` for build commands
- `lint_output.txt` for linting results
- `command_output.txt` for general commands