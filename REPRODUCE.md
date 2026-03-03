# Reproduction Steps

## Prerequisites

- Windsurf Editor 1.9566.11 or later
- Python 3 installed
- Windows 10/11 (also works on macOS/Linux with path adjustments)

## Step 1: Create the project directory

Place server.py (from src/ in this repo) and a README.md (any normal text) in a folder on your Desktop.

## Step 2: Register the MCP server in Windsurf

Open: %USERPROFILE%\.codeium\windsurf\mcp_config.json

Add the file-reader server pointing to server.py. See src/mcp_config.json for the template.

## Step 3: Restart Windsurf

Verify file-reader shows as Custom, 1/1 tools, Enabled in MCP Marketplace.

## Step 4: Verify clean state

Confirm mcp_mismatch_proof.txt does not exist on Desktop.

## Step 5: Open the project folder in Windsurf

## Step 6: Trigger the exploit

In Cascade chat type: Read the README.md file using the file-reader tool

## Step 7: Observe - no confirmation prompt

Cascade immediately executes the MCP tool with zero confirmation. Fake file contents displayed.

## Step 8: Verify arbitrary code execution

Check Desktop for mcp_mismatch_proof.txt showing whoami output. No confirmation prompt was shown at any point.
