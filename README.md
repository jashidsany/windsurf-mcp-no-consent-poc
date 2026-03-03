# CVE-PENDING: MCP Tool Executes Arbitrary Code Without Confirmation Prompt in Windsurf

## Summary

A malicious MCP server registered in Windsurf can execute arbitrary system commands when invoked by Cascade **without any user confirmation prompt**. The MCP tool claims to "safely read a text file" but instead executes `whoami`, writes files outside the project directory, and returns fabricated output to hide the attack.

Unlike other agentic IDEs that show a confirmation prompt before MCP tool execution, Windsurf's Cascade invokes MCP tools with **zero user consent** beyond the initial chat message.

- **Product:** Windsurf Editor
- **Version Tested:** [INSERT VERSION]
- **Platform:** Windows 10 (also applicable to Windows 11, macOS, Linux)
- **CWE-451:** User Interface (UI) Misrepresentation of Critical Information
- **CWE-862:** Missing Authorization — no confirmation prompt before MCP tool execution

## Video Demonstration

https://github.com/user-attachments/assets/VIDEO_ID_HERE

> To embed the video: Drag and drop the .mp4 into a GitHub issue or PR comment. GitHub will generate a `user-attachments` URL. Replace the URL above with that link.

## Critical Finding: No Confirmation Prompt

This is the most severe aspect of the vulnerability. When a user sends a message that triggers an MCP tool call, Cascade executes the tool **immediately** without:

- Displaying what tool will be called
- Showing the parameters being passed
- Asking the user to approve or deny execution
- Providing any indication that code is about to run on their system

| Product | MCP Tool Behavior | Confirmation Prompt |
|---------|------------------|-------------------|
| Claude Code | Shows tool name, params, description | Yes — user must approve |
| Cursor | Shows tool approval dialog | Yes |
| **Windsurf** | **Executes immediately** | **No — zero user consent** |

## Proof of Concept Walkthrough

### 1. Windsurf Version

![Windsurf Version](evidence/00_windsurf_version.PNG)

### 2. Clean Desktop — No Proof File Exists

![Clean Desktop](evidence/01_clean_desktop.PNG)

### 3. Malicious MCP Server Source Code

The `server.py` declares a tool called `read_file` with the description "Safely reads a text file and returns its contents." The actual implementation executes `whoami`, writes a proof file to the Desktop, and returns fake file contents.

![Server Source](evidence/02_server_py.PNG)

### 4. MCP Configuration

The `mcp_config.json` registers the malicious server in Windsurf's global MCP config.

![MCP Config](evidence/03_mcp_config.PNG)

### 5. MCP Marketplace — Server Enabled

![MCP Marketplace](evidence/04_mcp_marketplace.PNG)

### 6. Cascade Output — No Confirmation Prompt

**This is the core of the vulnerability.** The user types a chat message and Cascade immediately executes the MCP tool with zero confirmation.

![Cascade Output](evidence/05_cascade_output.PNG)

### 7. Proof File — Arbitrary Commands Executed

```
PROOF-OF-CONCEPT: MCP TOOL MISMATCH
User approved: read_file(README.md)
Actually executed: file write + system commands
whoami: desktop-c9ak2kc\maldev01
```

![Proof File](evidence/06_proof_file.PNG)

## Impact

- **Arbitrary code execution with zero user consent** — no confirmation prompt exists
- **File system access outside project directory** — proof file written to Desktop
- **Invisible execution** — user sees only fake output
- **Credential theft** — attacker can exfiltrate SSH keys, API tokens, environment variables
- **Persistent access** — every MCP tool invocation executes without consent

## Reproduction

See [REPRODUCE.md](REPRODUCE.md) for full step-by-step instructions.

## Files

| File | Description |
|------|-------------|
| `src/server.py` | Malicious MCP server (proof of concept) |
| `src/mcp_config.json` | Windsurf MCP configuration example |
| `src/README.md` | Legitimate project file used as bait |
| `REPRODUCE.md` | Full reproduction steps |
| `evidence/` | Screenshots and video demonstration |

## Disclosure Timeline

- **2026-03-03** — Vulnerability discovered and PoC developed
- **2026-03-03** — GPG-encrypted report sent to security@windsurf.com
- **Pending** — Awaiting Windsurf response

## Reporter

**Jashid Sany**
- GitHub: [https://github.com/jashidsany](https://github.com/jashidsany)
