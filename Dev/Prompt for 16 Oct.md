Wishmap is an Obsidian plugin that builds a visual, map-first productivity framework. The current dev branch (v0.2) has working **wikilink routing**, **page templating**, and **checkbox ↔ frontmatter sync**. Typing [[task - write brief]] or [[task write brief]] instantly creates a properly formatted note in 03 SaveBox/Active, loads the right template, sets YAML (created, due, done), and renames the link so it resolves. Task, Project, and Goal notes now sync the first checkbox with the done: field in YAML in both directions. The hierarchy relation between notes is: Area-Wish-Goal-Project-Task. Any notes that has links in or out, will follow this rule. Next step: create columns in template pages so user can drag goals/projects/tasks to schedule them. I will give you related files and folder structure screenshot in batches. Do not start coding before I said done sending files. Acknowledge which files have you received on every batch. This is batch 1.



So main is in the dev root. fm, checkboxsync, periodics, rollupAuto, rollups, router, scaffold, templates, utils, wikilinks are inside src/core. habitAutolog, habitDaily, habitGraph are inside src/embeds. periodicMenu is inside src/ui

---
### 🧩 Core Files (root)

- `main.ts` — main plugin entry
    
- `manifest.json` — plugin metadata
    
- `package.json` — build and deploy config
    
- `tsconfig.json` — TypeScript settings
    
- `styles.css` — optional styling
    

---

### ⚙️ Core Logic (`src/core/`)

Attach all of these:

- `utils.ts` — helpers (`localISO()`, token logic, etc.)
    
- `router.ts` — note creation, token application, YAML helpers
    
- `wikilinks.ts` — wikilink watcher and routing logic
    
- `scaffold.ts` — starter folder creation
    
- `periodics.ts` — daily/weekly note handling
    
- `fm.ts` — frontmatter read/write utilities (if still separate)
    
- `checkboxSync.ts` — frontmatter ↔ checkbox sync logic
    

---

### 🧱 Templates (`03 SaveBox/Templates/`)

Attach at least these:

- `Task Template.md`
    
- `Project Template.md`
    
- `Goal Template.md`
    
- `Daily Template.md` _(when we start periodic sync)_
    

---

### 🗂 Optional

- Screenshot of your **dev folder structure** (helps verify deploy path).
    
- `esbuild.config.mjs` if build issues appear.