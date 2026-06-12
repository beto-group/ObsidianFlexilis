# Contribution Guidelines — ObsidianFlexilis

Welcome! This component is part of the BetoOS Datacore library. Please adhere to the following architectural standards.

## Codebase Architecture

The module utilizes a split-file structure to guarantee legibility, testability, and isolated execution scopes:

```text
ObsidianFlexilis/
├── OBSIDIAN FLEXILIS.md   # Obsidian entry point
├── METADATA.md            # Component manifest
├── README.md              # Documentation
├── CONTRIBUTION.md        # This file
├── LICENSE.md             # MIT license
├── data/
│   └── mcp_commands.json  # External watch/reload trigger
├── assets/
│   ├── image/
│   │   └── preview_1.webp # Static preview image
│   └── videos/
│   │   └── preview.gif    # Interactive walkthrough GIF
└── src/
    ├── ObsidianFlexilis.component.jsx # High-fidelity React grid and metadata controller
    └── App.jsx            # Main bootstrap application loader and coordinator
```

## Developer Standards

1. **Strict Zero Emojis**: All UI elements, buttons, headers, and control indicators must use Lucide vector icons (`<dc.Icon>`) or plain text. Emojis are reserved strictly for documentation.
2. **Path Safety**: Do not hardcode absolute path strings (e.g. `/Volumes/` or `file:///`). Always resolve vault directories dynamically.
3. **No-Polling Code Watcher**: The index bootstrapper registers an event listener with `app.vault.on("modify")` targeting files under `ObsidianFlexilis/src/`. This triggers an instant reload of the component's React view when source code modifications are saved, bypassing background CPU polling entirely.
4. **HMR Command System**: To force a code reload or command watch directory path change remotely via MCP agents, write the reload payload to `data/mcp_commands.json`.
5. **Zero-Lag In-Memory Caching**: We do not perform full-vault directory scans or heavy markdown parsing loops inside active render cycles. Always utilize the global `obsidianFlexilisCache` Map. Real-time vault events (`changed`, `resolve`, `create`, `delete`, `rename`) must update the in-memory Map in-place for the mutated file only (`O(1)`).
6. **Sandboxed Styling (Anti-Bleed Protocol)**: Flexilis runs nested within active Obsidian workspace panels. Ensure all CSS layout changes are strictly scoped within component style declarations. Never bleed general tags or global styles into the native Obsidian workspace.
7. **Legacy Sorting Compatibility**: To keep database columns transparently sorting across different versions, always represent timestamps in seconds (divide milliseconds by 1000, i.e., `file.stat.ctime / 1000`) before binding files to data cells.
