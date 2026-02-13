## Scaling strategy for 500+ nodes (MNC)

At 500+ nodes, the file format is not the main risk. The risks are:
- human navigation
- LLM “global coherence”
- conflict/noise in edits
- UI performance
- keeping IDs stable while reorganizing

The strategy is: **partition + index + progressive summarization + strong tagging + deterministic refactors.**

---

# 1) Partition the project into multiple canvases

### Rule of thumb
- Keep a single canvas file in the **50–200 node** range when possible.
- Use multiple canvases for anything larger.

### Common partition schemes
Pick one primary axis and stick to it:

**System design**
- By bounded context / domain: `auth.mnc`, `payments.mnc`, `notifications.mnc`
- By layer: `edge.mnc`, `services.mnc`, `data.mnc`
- By lifecycle: `ingest.mnc`, `compute.mnc`, `serve.mnc`, `ops.mnc`

**Narrative / interactive fiction**
- By act/sequence: `act1.mnc`, `act2.mnc`, `act3.mnc`
- By location: `cannery.mnc`, `downtown.mnc`, `archives.mnc`
- By character POV: `krystin.mnc`, `mother.mnc`, `supervisor.mnc`

### Practical benefit
- LLM edits become localized.
- Git diffs become smaller and reviewable.
- UI doesn’t choke on one mega-canvas.

---

# 2) Add a “Hub” canvas (index/navigation)

Create a top-level `hub.mnc` with:
- high-level nodes summarizing each subcanvas
- links to “entry point” nodes in each subcanvas

You need cross-file linking. Keep it simple:

### Option A (no spec change): use normal Markdown URLs
In hub node body:
- `[Auth Canvas](file:auth.mnc)`
- `[Payments Canvas](file:payments.mnc)`

Your app can treat `file:` links as “open canvas”.

### Option B (small future extension): `mncfile:` scheme
- `[Auth](mncfile:auth.mnc#AUTH_ENTRY)`

Do not add this until you actually implement multi-file navigation.

---

# 3) Use hierarchy: containers + collapse

For 500 nodes, the UI must support visual chunking.

You already have `magnet` and `collapsed`. Use them aggressively.

Pattern:
- Make a “container” node per cluster (module, act, subsystem)
- Set `magnet layer=back`
- Default containers to `collapsed=true` once stable

This gives:
- overview mode
- drill-down mode
- fast navigation

---

# 4) Tagging strategy (make tags do the heavy lifting)

Tags are the primary query/filter mechanism.

### Use a small controlled vocabulary
Avoid free-for-all tags. You want consistency.

**System design tag set**
- Domain: `#auth #payments #search #identity`
- Type: `#service #db #queue #api #ui #batch`
- Flow: `#control #data #event`
- Status: `#todo #open_question #risk #decision #resolved`
- Quality: `#latency #security #reliability #cost`

**Narrative tag set**
- Structure: `#act1 #act2 #scene #beat`
- Function: `#clue #reveal #red_herring #setup #payoff`
- Character: `#krystin #maddie #clara #supervisor`
- Status: `#draft #needs_rewrite #locked`
- Tone: `#comic #tense #tender`

### Make tags composable
Prefer:
- `tags=#act2,#reveal,#krystin`
over
- `#Act2RevealKrystin`

---

# 5) Enforce reading order: `seq` (don’t rely on NODEID)

At scale, node ID sorting is for determinism, not meaning.

Use:
- `seq` for narrative order
- `seq` for system “flow order” in walkthroughs

Then the LLM can reorder by editing `seq` values without renaming node IDs.

---

# 6) Progressive summarization (prevents LLM overload)

For 500 nodes, you need “compression layers”.

### Recommended pattern: 3 tiers
**Tier 0: Hub canvas**
- ~10–30 nodes: major areas only

**Tier 1: Area canvas**
- ~50–150 nodes: detailed clusters

**Tier 2: Deep detail**
- optional sub-canvases per cluster (50–150 nodes)

### Add “summary nodes”
Within each canvas:
- one node per cluster that summarizes:
  - what the cluster is
  - key decisions
  - entry points
  - open questions
- tag it `#summary`
- keep it short and curated

This dramatically improves LLM usefulness because it has anchor points.

---

# 7) Deterministic refactors (renames, splits, merges)

You will eventually:
- split a canvas
- move nodes between canvases
- rename IDs (ideally rarely)

You need safe operations.

### Operations to support (even manually)
1) **Split canvas**: move a tagged subset into a new file
2) **Extract cluster**: move a container + contents
3) **Relabel tags**: rename tag across files
4) **Re-sequence**: renumber `seq` across a region

### ID strategy: immutable and boring
- Never encode meaning in IDs once you’re beyond ~200 nodes.
- Use stable IDs like `N0001`, `N0002` or `A12` etc.
- Put meaning in title + tags.

This reduces pain when reorganizing.

---

# 8) Edge/Link scaling

At 500 nodes, edges can explode.

### Principles
- Keep most edges **local** inside a canvas
- Use “summary-to-summary” edges across areas
- Prefer inline links for narrative and flow
- Use explicit link overrides only for styling/semantics when necessary

### Avoid hairballs
Introduce “router/connector” nodes:
- `AUTH_ENTRY`
- `EVENT_BUS`
- `PAYMENTS_ENTRY`

They act as intentional aggregation points.

---

# 9) UI requirements (or the text file will feel “too big”)

For 500 nodes, the app should provide:

- Filter by tags (AND/OR)
- Search titles and bodies
- Jump-to-node by ID
- “Outline view” sorted by `seq`
- Collapse/expand containers
- Hide edges by type
- Show only edges within selection

Without these, it’ll feel unmanageable regardless of file format.

---

# 10) A practical working workflow with an LLM

### Ingest phase (big dump)
- Create nodes quickly (IDs auto)
- Minimal tags
- No careful layout

### Organize phase (LLM-assisted)
- Cluster by tags
- Create containers
- Add summaries
- Assign `seq` where relevant
- Split into multiple canvases when a cluster stabilizes

### Maintain phase
- Changes happen locally within one canvas
- Hub summary updated occasionally
- Use `#open_question`, `#decision`, `#resolved` rigorously

---

# 11) Recommended “hard limits” to keep you sane
- One canvas: target 50–150 nodes
- One container cluster: 10–40 nodes
- One “summary node” per cluster
- One controlled tag taxonomy per project