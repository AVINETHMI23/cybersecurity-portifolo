# Binary Reverse Engineering, Dynamic Memory Patching, and Anti-Tamper Mitigations

## 🎮 Project Overview
A structural reverse engineering lifecycle case-study executed against an un-obfuscated execution engine (*Cave Story / NXEngine-Evo v2.6.5-1*). The codebase mimics legacy game loop logic, making it an excellent platform for demonstrating memory allocation exploits and defensive anti-debugging engineering.

## 🔬 Reverse Engineering Techniques
* **Static & Dynamic Disassembly:** Mapped program layout segments, registers, and specific instruction pointer references using modern debugging software.
* **Instruction Modification (NOPing):** Located precise assembly instructions controlling player damage and runtime life tracking arrays. Implemented direct patches using `NOP` (No Operation, `0x90`) instructions to eliminate health decrement functions.
* **Permanent Binary Patching:** Altered the application's `.text` execution section to export a permanently modified executable (`nx_modified.exe`) capable of executing outside an active debugging context.

## 🛡️ Strategic Architectural Mitigations
To secure legacy application environments from similar exploitation, I authored a portfolio of modern protection blueprints:
* **Anti-Debugging Traps:** Intercepting unauthorized process attachments by checking system loops for active instruction tracking.
* **Self-Checksumming:** Calculating cryptographic hashes of the `.text` segment during boot phases to prevent runtime bytecode alterations.

