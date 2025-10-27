### 🧭 Context Brief for Tomorrow

You’re working on an **Obsidian plugin** that extends the **WishMap** system.  
The plugin currently has a working module:  
`src/schedulers/areaScheduler.ts` — it creates a **“Yearly Schedule” grid** under area pages where users can **drag wish links** (e.g., `✨Wish - drag1`) into year columns like `2025` and `2026`.

- It updates the wish’s frontmatter with a tag (`Y2025`, `Y2026`).
    
- It displays a purple card in that column.
    
- Links under “✍️ My Wishes” get a light purple `#Y2025` bubble when already scheduled.
    
- The drag interaction disables link preview popups and prevents duplicate drags.
    
- The layout uses Obsidian theme variables and `color-mix` for adaptive coloring.
    

Tomorrow’s task:  
Create a **similar column layout for the “My Wishes” page itself**, where users can drag wish links into different **custom categories or priorities** (e.g., “Short-term”, “Long-term”, “Someday”).

The new scheduler should:

- Work exactly like `areaScheduler` (drag + drop cards).
    
- Use `kindFromBasename` and frontmatter updates as before.
    
- Allow adding or changing a wish’s category tag (e.g., `#WishShort`, `#WishLong`).
    
- Visually follow the same design style: light grey containers, purple cards, rounded corners.
    
- Be implemented as a new file, likely `src/schedulers/wishScheduler.ts`.
    
- Use the same helpers and CSS approach as `areaScheduler.ts`.
    

You can reference `areaScheduler.ts` for structure:  
`class WishColsChild extends MarkdownRenderChild` → mounted via `postProcess()` inside a new `WishScheduler` class.

Order:

### 🧭 Summary of order

|Order|Category|Examples|
|---|---|---|
|1|**Fields / constants**|private vars, state flags|
|2|**Constructor**|`constructor(app, file, container)`|
|3|**Lifecycle hooks**|`onload()`|
|4|**Core render**|`doRender()`|
|5|**Event hooks**|`installPageReentryHook()`, `installAutoAnnotateObserver()`|
|6|**Annotation logic**|`reAnnotateFresh()`, `pruneScheduledLinksUnderWishes()`|
|7|**DOM helpers**|`findHeaderLoose()`, `getNoteContainer()`|
class AreaColsChild extends MarkdownRenderChild {
  // 1. ---------- Fields ----------
  // Variables that belong to this instance
  private _reentryInstalled = false;
  private appRef: App;
  private fileRef: TFile;

  // 2. ---------- Constructor ----------
  constructor(app: App, file: TFile, containerEl: HTMLElement) {
    super(containerEl);
    this.appRef = app;
    this.fileRef = file;
  }

  // 3. ---------- Lifecycle ----------
  onload() {
    // Runs once when this MarkdownRenderChild mounts
    ensureSchedulerStyles();

    const noteRoot = this.getNoteContainer(this.containerEl);

    this.installPageReentryHook();
    this.installAutoAnnotateObserver(noteRoot);
    this.installDragPayloadBridge(noteRoot);

    this.doRender();        // layout
    this.reAnnotateFresh(); // final annotation
  }

  // 4. ---------- Core render ----------
  private doRender() {
    ensureSchedulerStyles();

    const noteRoot = this.getNoteContainer(this.containerEl);
    const hSchedule =
      this.findHeaderLoose(noteRoot, "yearly schedule") ||
      this.findHeaderLoose(noteRoot, "my yearly schedule");
    if (!hSchedule) return;

    const mount =
      (noteRoot.querySelector(".wm-year-grid-mount") as HTMLElement) ||
      this.ensureMountAfter(hSchedule, "wm-year-grid-mount");
    mount.innerHTML = "";

    const now = new Date();
    const years = [
      String(now.getFullYear()),
      String(now.getFullYear() + 1),
    ] as YearString[];

    const instruct = document.createElement("div");
    instruct.classList.add("wm-year-instruction");
    instruct.textContent = "Drag your Wish links to one of the boxes below.";
    mount.appendChild(instruct);

    const grid = this.renderYearGrid(years);
    mount.appendChild(grid.wrapper);

    const counts = this.populateWishes(grid, years, noteRoot);
    for (const y of years) {
      const col = grid.columns[y];
      const n = counts[y] ?? 0;
      col.header.textContent = n > 0 ? `${y} (${n})` : y;
    }
  }

  // 5. ---------- Hooks ----------
  private installPageReentryHook() { /* handles navigation */ }
  private installAutoAnnotateObserver(noteRoot: HTMLElement) { /* handles live DOM changes */ }
  private installDragPayloadBridge(noteRoot: HTMLElement) { /* manages drag events */ }

  // 6. ---------- Annotation ----------
  private reAnnotateFresh() {
    const root = this.getNoteContainer(this.containerEl);
    if (!root) return;
    if (this.appRef.workspace.getActiveFile()?.path !== this.fileRef.path) return;

    this.pruneScheduledLinksUnderWishes(root);
    this.bindMyWishesAnchors(root);
  }

  private pruneScheduledLinksUnderWishes(noteRoot: HTMLElement) { /* marks scheduled wishes */ }
  private bindMyWishesAnchors(root: HTMLElement) { /* manages draggable state */ }

  // 7. ---------- DOM + helpers ----------
  private renderYearGrid(years: YearString[]) { /* creates grid */ }
  private populateWishes(...) { /* fills grid */ }
  private getNoteContainer(el: HTMLElement): HTMLElement { /* gets note root */ }
  private findHeaderLoose(root: HTMLElement, text: string) { /* finds header */ }
  private ensureMountAfter(h: HTMLElement, cls: string) { /* ensures mount div */ }
}
