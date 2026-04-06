
  Maddox v4.5 — Complete Documentation                       
  Ethical Hacking Assistant with Google Gemini 2.5 Flash      


# 1. WHAT IS MADDOX?


Maddox is a pentesting assistant that uses Google Gemini 2.5 Flash through
the Google AI API (OpenAI-compatible endpoint). It features an extremely
fast model with a 1M token context window. It analyzes security tools,
suggests commands, generates reports, remembers targets across sessions,
and can execute commands directly from the chat.

Single script: maddox.py (~6450 lines, Python 3.8+)
Storage: ~/.maddox/ (sessions, targets, timeline, files, notes, reports)

<img width="571" height="393" alt="img1" src="https://github.com/user-attachments/assets/990c7424-646f-4eb4-b1ae-ccf956f5e242" />


# 2. REQUIREMENTS


- Python 3.8 or higher
- openai package: pip install openai
- Internet connection (to access the Google AI API)
- Google AI API key
- Operating system: Kali Linux (designed and tested exclusively for Kali)

Python Dependencies (all stdlib except openai):
  openai, sys, re, os, json, subprocess, readline, atexit,
  time, urllib.request, urllib.error, socket, select, threading,
  fcntl, pathlib, datetime, signal


# 3. HOW TO GET AND CONFIGURE API KEYS

Maddox uses Google's Gemini 2.5 Flash model, which requires an API key
from Google AI Studio. The service offers a generous free tier.

HOW TO GET YOUR API KEY:
1. Go to Google AI Studio: https://aistudio.google.com/apikey
2. Sign in with your Google account.
3. Click on "Create API key" (Create new key in a new project).
4. Copy the generated key.

HOW TO CONFIGURE THEM IN MADDOX:
Maddox supports multiple API keys and features automatic rotation. If one
key hits its rate limit, Maddox will seamlessly rotate to the next one.

1. Open `maddox.py` in your favorite text editor (e.g., nano, vim, code).
2. Locate the configuration section at the top of the file.
3. Look for the `GEMINI_KEYS` array.
4. Replace the placeholder keys with your actual API keys:

   GEMINI_KEYS = [
       "AIzaSyYourGeneratedKeyHere_1",
       "AIzaSyYourGeneratedKeyHere_2", # Optional: add more keys
   ]

You can add as many keys as you want (one per Google account). Maddox
will manage them automatically.



# 4. CONFIGURATION SETTINGS

Edit the constants at the top of maddox.py if needed:

  GEMINI_URL              API URL (default: https://generativelanguage.googleapis.com/v1beta/openai/)
  GEMINI_KEYS             List of Google AI API keys (automatic rotation)
  MODEL                   Model to use (default: "gemini-2.5-flash")
  MAX_TOKENS_RESPUESTA    Max tokens per response (default: 12000)
  MAX_TOKENS_PEAS         Max tokens for PEAS reports (default: 32000)
  MAX_CHUNK_CHARS         Max chars per analysis chunk (default: 250000)
  MAX_HISTORY             Max turns in memory (default: 40)
  MAX_RETRIES             Retries on connection error (default: 1)
  RETRY_DELAY             Seconds between retries (default: 5)
  RATELIMIT_DELAY         Minimum seconds between calls (default: 4.0)
  CONNECTION_TIMEOUT      Timeout for health check (default: 15)
  RPD_POR_KEY             RPD limit per key in free tier (default: 20)
  RPD_AHORRO_CRITICO      Threshold to save tokens (default: 3)



# 5. USAGE MODES

  maddox                          Interactive chat
  maddox <IP>                     Chat with preconfigured target IP
  maddox <question>               Quick response (no chat mode)
  maddox <file>                   Analyze a security tool's output file
  cat scan.txt | maddox           Analyze via pipe
  nmap -sCV 10.10.10.1 | maddox   Analyze direct output via pipe


  
# 6. SLASH COMMANDS

All commands have multiple aliases so you can type naturally. Here are
the main ones with their aliases:

ANALYSIS AND EXECUTION:
  /file (/archivo /fichero /analizar /abrir)    Load and analyze a file
  /cmd (/exec /ejecutar /run /shell)            Execute command + analyze
  /ip (/target /objetivo /victima /host)        Set/change target IP

INFORMATION:
  /target (/objetivo /victima /host /maquina)   View target status
  /timeline (/cronologia /actividad)            View timeline of actions
  /context (/contexto /tokens /memoria /uso)    View context usage
  /status (/estado /conexion /health /ping)     Check API connection
  /search (/buscar /find /grep)                 Search in history

CHEATSHEETS (AI-generated):
  /methodology (/metodologia /pasos /guia)      Pentesting methodology
  /chisel (/pivoting /pivot /tunel /tunnel)     Pivoting cheatsheet
  /revshell (/reverse /shell /shells /rev)      Reverse shells
  /privesc linux (/escalada linux /priv linux)  Linux Privilege Escalation
  /privesc windows (/escalada win /priv win)    Windows Privilege Escalation
  /transfer (/transferir /subir /descargar)     File transfer

SESSIONS AND MANAGEMENT:
  /save (/guardar /salvar /grabar /exportar)    Save session
  /load (/cargar /restaurar /abrir sesion)      Load previous session
  /sessions (/sesiones /sesion)                 List sessions
  /clear (/limpiar /reset /borrar /vaciar)      Reset history
  /optimize (/optimizar /comprimir /reducir)    Compress context
  /undo (/deshacer /atras /revert /revertir)    Undo last exchange
  /note (/nota /apunte /anotacion)              Quick note
  /report (/reporte /informe /resumen)          Generate Markdown report

MODES:
  /stealth (/sigilo /sigiloso /quiet)           Stealth mode on/off
  /help (/ayuda /? /comandos)                   List of commands
  /exit (/salir /quit /q /bye /adios)           Exit



# 7. SMART DETECTION (NATURAL LANGUAGE)

You don't have to use /commands. Maddox understands natural language,
including first-person phrasing, questions, requests, and slang:

COMMAND EXECUTION:
  "run nmap -sCV 10.10.10.5"             -> Executes the command
  "launch gobuster against the web"       -> Executes gobuster
  "nmap -sS 10.10.10.5"                  -> Detects and executes directly
  "throw nikto against the server"        -> Executes nikto

  <img width="1686" height="782" alt="nmap" src="https://github.com/user-attachments/assets/b20b4121-14d9-4f16-b99d-3f66b4049b21" />


FILE ANALYSIS:
  "analyze /tmp/scan.txt"                 -> Loads and analyzes
  "what's in results.xml"                -> Loads and analyzes
  "check the content of linpeas.txt"      -> Loads and analyzes

SET TARGET IP:
  "the target is 10.10.10.15"            -> Sets IP
  "let's go with 10.10.10.5"             -> Sets IP
  "the victim is 192.168.1.100"          -> Sets IP

METHODOLOGY (also in first person):
  "where do I start with 10.10.10.5?"    -> Sets IP + methodology
  "how do I hack this machine?"          -> Requests methodology
  "what steps should I follow?"          -> Requests methodology
  "help me compromise the target"        -> Requests methodology

PRIVILEGE ESCALATION (first person):
  "how do I escalate privileges?"        -> PrivEsc cheatsheet
  "how can I become root?"               -> PrivEsc cheatsheet
  "I want to be root"                    -> PrivEsc cheatsheet
  "how do I become root in linux?"       -> PrivEsc Linux cheatsheet
  "I need to escalate in windows"        -> PrivEsc Windows cheatsheet

REVERSE SHELLS (first person):
  "how do I get a reverse shell?"        -> RevShell cheatsheet
  "I need a reverse shell"               -> RevShell cheatsheet
  "give me a reverse shell"              -> RevShell cheatsheet

PIVOTING/TUNNELING (first person):
  "how do I do pivoting?"                -> Pivoting cheatsheet
  "I need a tunnel"                      -> Pivoting cheatsheet
  "how do I move to another network?"    -> Pivoting cheatsheet
  "how do I do port forwarding?"         -> Pivoting cheatsheet

FILE TRANSFER (first person):
  "how do I transfer files?"             -> Transfer cheatsheet
  "how do I send a script?"              -> Transfer cheatsheet
  "I need to upload a file"              -> Transfer cheatsheet
  "how do I download something to it?"   -> Transfer cheatsheet

SESSION MANAGEMENT (first person):
  "save the conversation"                -> Saves session
  "I want to save this"                  -> Saves session
  "clear the history"                    -> Resets chat
  "optimize the context"                 -> Compresses history
  "I am running out of space"            -> Compresses history
  "generate a report"                    -> Generates report
  "show the timeline"                    -> Shows timeline
  "what have I done so far?"             -> Shows timeline



# 8. MULTILINE INPUT (PASTE)

Maddox automatically detects if you paste multiline text (Ctrl+Shift+V in
most terminals). It uses select() with a 200ms timeout to capture all lines
arriving quickly (compatible with SSH latency).

If auto-detection fails, use the manual mode:

  maddox:~$ """
  (paste all your multiline text here)
  (multiple lines)
  """

It will display how many lines and chars were captured.



# 9. TARGETS MEMORY

Each target IP has a persistent JSON file in ~/.maddox/targets/.
It automatically updates with each analysis:

  - Detected open ports
  - Services and versions
  - Found credentials
  - Findings with timestamps
  - Attack vectors
  - Achieved accesses
  - Manual notes (/note)
  - Relationships between findings

Data persists across sessions. When setting an IP that was analyzed
before, all previous data is loaded automatically.



# 10. STEALTH MODE

Activate with /stealth. Modifies the system prompt so ALL suggested
commands prioritize stealth:

  - Nmap: -T2, -sS, --data-length, decoys, fragmentation
  - Avoids noisy tools (nikto, aggressive gobuster)
  - Prefers proxychains/tor
  - Suggests cleaning logs and traces
  - IDS/IPS evasion (encode payloads, timing)
  - Temporary files in /dev/shm
  - Alerts when a command is noisy

The stealth state is saved in sessions and restored upon loading.



# 11. CONTEXT MANAGEMENT

Gemini 2.5 Flash has 1M context tokens. Maddox manages it like this:

  /context           View current usage with a visual bar, role breakdown,
                     and warnings if nearing the limit.

  /optimize          Manual compression: the AI summarizes the ENTIRE
                     conversation in ~400 words, freeing up most context.

  Auto-optimization  When reaching 85% context, it automatically
                     compresses OLD messages and keeps the last 10
                     intact. Transparent to the user.

  Auto-save          Upon exit, it optimizes the session before
                     saving. When restored, it will be heavily optimized.

Temperatures used:
  0.1  Scan analysis, context summary
  0.2  Professional reports
  0.3  Interactive chat (default)



# 12. FLAG VALIDATOR (ANTI-HALLUCINATION)

AI sometimes hallucinates non-existent flags. Maddox mitigates this:

1) SYSTEM PROMPT: Explicit instruction not to invent flags.

2) POST-RESPONSE VALIDATION: After every response, it extracts suggested
   commands and validates each flag against the tool's actual --help
   output in a background thread. If suspect flags are found:

   [!] POSSIBLY INVENTED FLAGS DETECTED:
       nmap: --vuln-scan, --deep-check
       (not found in 'nmap --help')

3) PRE-EXECUTION VALIDATION: Before executing a /cmd or NL command,
   it validates flags. If suspicious, it prompts before continuing.

Caches --help to avoid re-executing. 30+ tools supported.



# 13. SESSIONS

Saved in ~/.maddox/sesiones/ as JSON. Include:
  - Timestamp
  - Target IP
  - Stealth status
  - All messages
  - Number of turns

Auto-saves upon exit. List with /sessions, load with /load <number>.
Restores IP, stealth mode, and compressed context.



# 14. REPORTS

/report generates a professional Markdown document with:
  - General info (date, IP, scope)
  - Executive summary
  - Findings with severity, evidence, impact, recommendation
  - Credentials found
  - Attack vectors
  - Timeline of activities
  - Recommendations
  - Conclusion

Saved as ~/.maddox/reporte_<ip>_<timestamp>.md



# 15. TIMELINE

Automatic chronology of all your actions per target:
  - Scans executed
  - Analyses performed
  - Mode changes (stealth on/off)
  - Reports generated
  - Notes added
  - Files created by the AI

View with /timeline. Saved in ~/.maddox/timeline/



# 16. FILE GENERATION & READING

GENERATION:
You can ask Maddox to create files:
  "create a python script that does X"
  "save a payload in /tmp/shell.php"

The AI includes special blocks that Maddox detects and writes to disk
automatically (protecting dangerous paths).

READING:
The AI can request to read local files to give precise answers.
It uses tags like: ---MADDOX_LEER:/etc/passwd---
Maddox intercepts this, reads the file securely, and injects it.
Security measures: max 5 files per response, truncates at 100KB,
detects binary files, blocks /dev, /proc, /sys.



# 17. SMART PARSERS

Maddox auto-detects the tool that generated output and applies a
specialized parser. 30+ tools supported including:
nmap, masscan, gobuster, ffuf, feroxbuster, sqlmap, hydra, john,
enum4linux, crackmapexec, linpeas, winpeas, evil-winrm, nuclei, etc.

For large files, it uses automatic chunking: splits, analyzes each part,
and generates a consolidated final summary.



# 18. ERROR HANDLING

  - Automatic retries with delay
  - Health check if all retries fail
  - Smart diagnostics for errors (CONNECTION REFUSED, TIMEOUT,
    NOT FOUND, UNAUTHORIZED, RATE LIMIT, etc.)
  - Graceful exit on SIGTERM with auto-save



# 19. FILE STRUCTURE

  ~/.maddox/
  ├── sesiones/           Saved sessions (JSON)
  ├── targets/            Target memory by IP (JSON)
  ├── timeline/           Chronology per target (JSON)
  ├── files/              AI-generated files (redirected from danger paths)
  ├── .readline_history   Command history
  ├── notas_generales.txt Notes without active target
  └── reporte_*.md        Generated reports

  maddox.py               Main script (~6450 lines)



# 20. NETWORK REQUIREMENTS

Maddox uses Google Gemini 2.5 Flash via the Google AI API.
The model runs on Google servers, so you only need a stable connection.

REQUIREMENTS:
  - Stable internet connection
  - Valid API Key from Google AI Studio
  - Python 3.8+ with openai package

ADVANTAGES vs LOCAL AI:
  - No powerful GPU needed
  - Extremely capable model with 1M token context
  - Fast response times
  - No local RAM/VRAM consumption

LATENCY:
  - Fiber/Cable: 50-200ms
  - WiFi: 100-300ms
  - Mobile/VPN: 200-500ms



# 21. TIPS AND BEST PRACTICES

  - Speak to Maddox like a person: it understands natural phrasing.
  - Set the IP target at the start (/ip or "the target is X.X.X.X").
  - Use /note to jot down important things.
  - Stealth mode is GLOBAL: affects ALL responses.
  - Paste outputs directly (auto-detect paste).
  - Reports use the ENTIRE session context + target + timeline.
  - Use /undo if the AI hallucinates badly so it doesn't pollute context.
  - Run /context periodically to check your token usage.



# 22. CHANGELOG

v4.5 (current):
  + AUTONOMOUS SCANNING: "analyze this ip" -> auto nmap execution
  + IMPROVED KEY MANAGEMENT: Auto invalidation, UI indicators
  + FIX TERMINAL WRAPPING: Respects ANSI sequences
  + UNSOLICITED FILE PROTECTION: AI cannot blind-write
  + RPD OPTIMIZATION: Limits tracked accurately
  * Refactored PENAI to Maddox

v4.4:
  + MIGRATED TO GOOGLE GEMINI 2.5 FLASH
  + No local server required
  + 1M Token Context

v4.3:
  + First-person Natural Language queries
  + Expansion of 30+ tools
  + 80+ aliases for slash commands
  + IPv6 extraction

v4.2:
  + Local file reading by the AI
  + Secure path handling
  + File locking for concurrent instances

v4.1:
  + /undo, /search, /note
  + Multiline input paste auto-detection

v4.0:
  + Stealth mode
  + Persistent targets memory
  + Automatic timeline
  + Markdown reports
