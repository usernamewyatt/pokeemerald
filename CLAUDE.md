# Pokémon Emerald ROM Hack — Project Notes

*A living document with project notes about this ROM hack. Update the "Current state" and "Next steps" sections at the end of each work session. Dump ideas into the "Idea Backlog" anytime — capturing is free, committing is not.*

## What is This Project?
A personal-use ROM hack built on the vanilla pret `pokeemerald`decompilation (deliberately *not* the expansion). The goal is to fix known bugs, implement a curated set of quality-of-life features, and build toward a story-focused hack. This ROM hack is not for distribution; it is built from a legally-owned Emerald copy.

## Who am I?
- I am new to this codebase specifically. I am also new to git, C, and assembly. I am familiar enough with how code works to feel comfortable embarking on this endeavor with help and assisted learning along the way.
- I prefer explanations of *why* a change works and what we are doing, rather than just being told what to type.
- I am working on macOS and working in Terminal (+ a code editor).

## Current State (as of June 25, 2026 — updated same day)
- Toolchain installed: Xcode Command Line Tools, Homebrew, libpng, devkitPro/devkitARM. Env vars (`DEVKITPRO`, `DEVKITARM`) set in `~/.zshrc` and verified.
- Repo cloned to: `/Users/wyattjohnson/Documents/Creative/ROM_Hacks/pokeemerald`
- First build succeeded using **modern mode** (`make modern`); the ROM runs in OpenEmu and Visual Boy Advance.
- Clean baseline committed to git: *"Vanilla pokeemerald baseline; builds and runs."*
- **Off-site backup done.** Created a private GitHub repo, repointed `origin` from pret's repo to it, and pushed `master`. The baseline now lives off-machine, not only locally.
- **Branch cleanup done.** An accidental "Add a README" at repo creation spawned a stray `main` branch (README-only). Set `master` as default on GitHub, deleted remote `main`, and deleted local `main`. `master` is the sole branch, locally and on GitHub. *(Why master: pokeemerald tutorials assume it, and it already held all files + history.)*
- **Removed pret's inherited CI** (`.github/workflows/build.yml`) so pushes no longer trigger failing GitHub Actions runs. *(Those failures were cosmetic — pret's build running in GitHub's environment, unrelated to local `make modern`.)*
- Tooling added: **GitHub Desktop** (visual git for commits/pushes/branches) installed + connected; **Claude Code** desktop app downloaded, ready for on-disk editing.
- **Baseline edit test DONE — Phase 0 complete.** Renamed Littleroot Town's
  location banner via `src/data/region_map/region_map_sections.json` (the `name`
  field; it's generated into `region_map_entries.h` at build time — edit the JSON,
  never the generated file). Rebuilt with `make modern`, confirmed the new banner
  in VBA, committed + pushed. The full edit→rebuild→test→commit loop is now proven
  end to end. *(The test name is cosmetic — revert anytime.)*
- **Primary dev emulator: VBA.** Chosen over OpenEmu for the dev loop because VBA
  keys save states to the ROM *filename* (constant across rebuilds), so slots
  survive rebuilds; OpenEmu keys to the ROM *hash*, which changes every rebuild
  and orphans the slots.
- **Save-file handling sorted.** Emulator saves (`.sgm`, `.sav`, `.ss*`) are
  already gitignored by pret's rules. Added `/save_backups/` to `.gitignore` so an
  entire personal save-backup folder is ignored no matter what's inside it.
- Five-phase roadmap defined (see "Roadmap" below).
- **CLAUDE.md added to repo root** so Claude Code auto-loads project context each session.
- **VS Code error highlighting disabled** for this project — the editor's language server doesn't understand devkitARM macros (e.g. `EWRAM_DATA`). Errors shown are false positives; `make modern` is the source of truth. (`compile_commands.json` deferred to Phase 2+.)
- **Running indoors enabled** — removed `!gMapHeader.allowRunning ||` from `IsRunningDisallowed()` in `src/bike.c`. The metatile-level check (`IsRunningDisallowedByMetatile`) is preserved, so ice/long grass/hot springs still block running. Per-map control can be re-added later via a deliberate flag check. Tested in VBA, confirmed working.

## Decisions Made
- **Vanilla over expansion:** want to learn by fixing real vanilla bugs rather than inheriting a heavily-modified base like pokeemerald-expansion, working towards the features I want rather than having them already handed to me like in pokeemerald-expansion.
- **Modern build (devkitARM GCC) over agbcc matching build:** simpler, and byte-matching isn't needed for personal use.
- **Renamed the working folder** to remove the space (was "ROM Hacks" → "ROM_Hacks") to avoid `make` choking on spaces in the path.
- **No bug chosen as "the first fix" yet:** will survey the landscape of known vanilla bugs before committing to one. The roamer bug is a candidate, not a commitment.
- \*\*Kept the branch named \`master\`\*\* (didn't rename to \`main\`): tutorials assume it, and it already held everything. - \*\*Removed pret's CI workflow\*\* rather than tolerate failing runs. \*(Future option: add pret back as an \`upstream\` remote to pull their fixes — a Phase 1+ nicety, not needed for a backup.)\* - \*\*Three surfaces, three jobs:\*\* GitHub Desktop for visual git, Claude Code for on-disk edits, project chat for planning.


## Goals Ordered by Priority
1. **Bug Fixes** — survey known vanilla bugs first, then pick which to tackle.
2. **Quality of Life Features** — a curated set; running wishlist lives in the Idea Backlog.
3. **Story-Focused Hack** — as rich as I can manage, once the base is solid. Map scripts live under `data/`.

## Roadmap (foundation → playable hack)
Three jobs stacked: get *safe*, get *fluent*, get *building*. Order isn't arbitrary — each phase hands the next the skill it needs.

- **Phase 0 · Foundation** *(get safe — hours)* — Trustworthy edit→rebuild→test→commit loop + off-site backup. *Why first: once nothing can be permanently broken, everything after gets braver and easier.*
- **Phase 1 · Codebase literacy** *(get fluent — a few short sessions, then ongoing)* — Learn where things live (`src/` = C behavior, `data/` = content like maps/scripts/text/tables, `include/` = headers tying it together) via small contained edits, each a full loop. *Why: you can't fix or build what you can't navigate, and small edits with an obvious right answer are the safest practice.*
- **Phase 2 · First bug fix** *(get fluent — an afternoon to a stubborn week)* — Reproduce → trace → fix → verify → commit, ideally on a git branch. *Why before features: fixing has a known-correct target; features make you design the right behavior yourself, which is harder.*
- **Phase 3 · QOL features** *(get building — an afternoon to weeks each)* — Add behavior the game never had. Scope varies wildly (forgettable HMs is contained; IV/EV management may mean a whole new menu/UI). *Why here: features lean on Phase 1–2 muscles, and you want stable systems before content sits on them.*
- **Phase 4 · Story & content** *(get building — open-ended; likely dwarfs the rest)* — Maps, scripts, dialogue, trainers, possibly a designed region. More authoring than programming. *Why last: content is the most expensive thing to redo, so lock systems and fixes first. Intentionally the fuzziest phase — sharpen it only as you approach it.*

## Idea Backlog (capture anytime — order and completeness don't matter)
*A parking lot, not a plan. Dump ideas here the moment they float in, even mid-task. Triage and feasibility come later.*

### Bugs to investigate
- Roaming legendaries possibly despawning when they use Roar/Whirlwind. Roamer logic lives in `src/roamer.c`. *(candidate — not committed as first fix)*
- *(survey for more known vanilla bugs; add candidates here)*

### QOL & feature wishlist

**Friction removal (small, contained, near-vanilla feel)**
- Forgettable HMs.
- Running indoors.
- A "use another Repel?" prompt, or bulk Repel use capped at ~10 at once.
- Decapitalization of Pokémon names and other text.
- Easy/cheap move relearner.
- A PC accessible almost anywhere (with story-driven lockouts — see Story ideas).

**HMs & field traversal**
- Use field moves without the move being learned: walk up to water, press A, and if a party member *could* learn Surf and you own the HM, it offers to surf (HM-as-license / Ride-Pokémon style). Open question: require only that a party member is *able* to learn the move, vs. require it to actually know it. Leaning toward "able to learn + own the HM."
- More Pokémon moves/abilities interacting with the overworld: Levitate to cross a gap, Pickup surfacing a buried item, a Fire-type melting an ice block. (Team as a traversal toolkit.)
- Routes with capability-gated branches: a fire route, a water route, etc., depending on party composition.

**Battle mechanics & move pools**
- Physical/Special move split. *(Note: more a deliberate balance change than pure QOL — touches the battle engine. Decide consciously.)*
- Reusable TMs. *(Quietly changes the item economy — decide consciously.)*
- Fairy typing and other current-gen mechanics; wider move pools.
- Move mastery: use a move enough and it earns a small permanent perk (accuracy bump, +1 PP, a variant, etc.). Pairs naturally with crafting as a permanent micro-upgrade.

**Pokémon management — abilities, natures, IVs/EVs**
- An IV/nature checker item for wild encounters (late-game tool for finding the Pokémon you need). Could also surface held item and ability.
- More abilities available to all Pokémon, plus an Ability Capsule item.
- Nature Mints.
- Choose a Pokémon's nature after it reaches a bond threshold (~10 levels with you) — framed as bonding shaping its behavior.
- **IV management:** items like Bottle Caps that adjust *permanent* IVs (including for breeding), raising/lowering at will or via item/end-game currency. Should get easier as the game scales. Vanilla has zero IV tooling, so this is all-new surface (safest, highest-novelty piece of the stat layer).
- **EV management — two competing models, pick one (they don't compose):**
  - *Model A — manual allocation on level-up:* leveling grants a pool of points to assign to stats (JRPG-style), replacing automatic EV gain from defeating Pokémon. Pair with a free respec.
  - *Model B — EVs stay battle-earned; crafting is the management layer:* crafted "Pokéblocks" raise/lower/respec EVs (leans on vanilla's existing EV-lowering berries + vitamins). This is the model that lets crafting own EVs.
- *Note:* if EVs become level-up points (Model A), crafting should NOT touch EVs — it takes IVs/nature/build tuning instead.

**Crafting & economy** *(developed; see direction below)*
- **Core principle:** common materials drip in passively; rare materials are worth leaving home for. Crafting must never be tedious, and craftables should never duplicate what the Poké Mart already sells (that's what keeps the money economy intact).
- **Material sources:**
  - *Passive drops* from battling/catching (money-system style) — common materials only.
  - *Dex-first catch bonus:* the catch reward fires the **first** time a species is registered, not every catch (kills the catch-and-release exploit, rewards Dex completion).
  - *Targeted spots* (ore veins, rare nodes, some gated by a field capability) — the scarce materials the good recipes gate on.
  - *Berry farm* as a renewable base input (see below); apricorns grow here too.
- **Materials kept short, on ~3 axes:** a common catch-all "essence/residue," terrain-themed forageables (shards/ore, petals/herbs, scales/pearls, ash/embers), and a small set of rare inputs. A recipe = common + themed (+ rare for the good stuff). Three material types per recipe, max.
- **Bulk crafting** is essential (a "make as many as materials allow" option), flat cost rate.
- **What crafting makes (none of it sold by the shop):**
  - Stat-tuning items (IV/EV/nature) — the backbone sink.
  - Permanent micro-upgrades (PP boosts, ability-swap item, move mastery).
  - **Apricorns → Apriballs** — instant, in bulk, no overnight wait. High-volume, balance-safe material sink.
  - Encounter consumables / incenses (see below).
  - The means to confer the Delta capacity onto a Pokémon (see Battle gimmick).
- **Encounter-affecting consumables (incense / bait / call / essence × effect × target):**
  - *Attraction* (what appears): type bias, rarity-tier bias, species/family lure, swarm trigger/relocation.
  - *Refinement* (quality of what appears): held-item bias, IV/quality bias, nature bias, gender bias.
  - Guardrail: don't let "attraction" effects stack into a guaranteed perfect encounter — one "what appears" effect at a time, allow "quality" effects to layer on top.

**Berry farm**
- Replace scattered berry soil with a single **berry farm**: a fixed number of plots, more unlocked as the game progresses. Auto-harvests once per "day" into a collection box — no manual tree-clicking, no real-time-clock babysitting.
- **Load-bearing open question:** what unit of progress drives the harvest tick? Options: steps taken, Pokémon Center visits, or a tamed RTC that doesn't punish absence. Decide this first — everything else hangs off it.
- Apricorns are a farm crop (feeds the Apriball supply), and the farm is the renewable input that feeds the crafting economy.

**Battle Gimmick — Delta Pokémon** *(developed; the hack's signature mechanic)*
- A Tera-style invoked gimmick, NOT a permanent retype. Pokémon stay pure-vanilla species; Delta is a state you induce.
- Two Layers: *assignment* is permanent (crafting confers the capacity onto a specific Pokémon, a one-time material cost); *activation* is temporary (once per battle, free, lasts the rest of the fight, with a flash/spectacle).
- **Type mapping is a fixed rule, not per-species choice:** the Delta type is a deterministic function of the original type (~18 relationships total, e.g. Ghost→Psychic, Psychic→Fairy). No hunting for a "good" delta; every Gastly is identical. Bounds the balancing to ~18 cases.
- The Delta type **replaces** one of the Pokémon's types rather than adding a third — keeps the two-type cap and makes it a trade-off, not a pure upgrade.
- A few legendaries/bosses could have a fixed, uncraftable Delta as lore.
- **Open creative decision:** the *fiction* behind the mapping (ascension? corruption? a "shadow" type?) — this choice IS the lore, and the story should explain why Deltas exist and why the player learns to induce them.
- *Build note:* battle-engine work; slot it after a crafting system ships end-to-end (the Delta-capacity recipe is then just one more recipe).

**Battle Gimmick — Move Chaining / Combos**
- Single battle, on the field. Two chain modes that trade off against each other:
  - Stay-In Ramp: repeating/related moves build power or accuracy (Ember, Ember, stronger fire move). Rewards commitment to one Pokémon. Resets on switch.
  - Switch Combos: a type sequence across mons triggers an effect (Water → Electric = paralysis, etc.). Rewards setup across the team.
- Conditional Free Switchign: a switch that continues a valid combo is free and triggers the effect; a switch for any other reason still costs a turn (vanilla). This kills willy-nilly switching, makes the chain the point of swapping, and preserves difficulty.
- The turn-by-turn tension — keep ramping vs. switch to cash a combo — is the tactical heart.
- Keep the combo table small and intuitive (~6–10 elemental pairings a player discovers without a manual).
- Variant: two-active "Double Dash" framing (one forward, one in reserve, swap which is active) — bigger build, edges toward a tag-battle.

**Team synergy — species affinity / ecology** *(ambient; stacks with the above)*
- Emerges from team composition, no activation — rewards who you raised together rather than in-battle execution. Fits the "natural bonding" feel.
- Terrain Affinity: Grass-types stronger in a forest, Water-types in water, etc. Hooks into the battle-terrain variable vanilla already tracks (Secret Power / Nature Power / Camouflage use it).
- Species Relationships: rivals (Zangoose/Seviper) heighten Defense; symbiotic pairs (Plusle/Minun) boost Offense; predator/prey shift Speed/accuracy. Asymmetry by design — both rivals on *your* team is upside; one on each side is a wash.
- Guardrail: keep affinities *lateral* (reward different thematic teams) so there's no single "correct" team.
- Note: "check who's in the party, apply stat mods" leans on existing stat machinery — more tractable than the invoked gimmicks. Ships as a clean second-phase, ambient layer.
- Earlier note this supersedes: the simple "two Pokémon raised together get a small on-field synergy bonus" is the bonded-pair subset of this system.

**Pokémon identity & bonding**
- Nicknames and epithets that can be earned for Pokémon.
- NPCs recognize a famous Pokémon you use/nickname heavily ("is that the Marill that won at Slateport?").

**Encounters & catching**
- Increased catch rate for shiny Pokémon.
- Idle Pokémon in the PC assigned to a "training ground" for passive XP/EV gain (Sun & Moon did a form of this). Makes the box matter instead of being a graveyard.

**World, exploration & story-adjacent**
- An objective/quest log.
- More dungeons.
- Camping/resting in extensive dungeons.
- Lore told through artifacts (a Key-Item-like category) placed in a museum to uncover the world's story.
- Wager battles — bet money against an NPC who bets back.
- A town (or several) that visibly develops based on your contributions: new shops, shop upgrades, visible changes.
- Pokédex completion unlocks rewards along the way; possibly per-species Dex challenges (Legends: Arceus style).

### Story Ideas
- The PC is accessible almost anywhere, but story plot elements lock it during stretches where the player must clear a dungeon or gym with their current party — no switching until they complete it (or leave and restart it). Modern games keep the PC always available; deliberately cutting it off here raises the stakes.
- If Delta becomes the signature mechanic, the plot is partly about discovering the phenomenon, learning to induce it, and confronting someone misusing it and/or something more related to environmental havoc, similar to many Pokemon Ranger games — the gimmick as the story's spine.

## Next Steps (pick up here)
1. **Commit and push today's running indoors change** (`src/bike.c`).
2. **A few more small Phase 1 edits** to build rhythm — good candidates: edit a starter's base stat (`src/data/pokemon/base_stats/`), edit NPC dialogue (`data/maps/*/scripts.inc`), or change a wild encounter (`data/wild_encounters.json`).
3. **Survey known vanilla bugs** (don't pre-commit to the roamer); log candidates in the Idea Backlog.
4. **Keep capturing** feature/story ideas in the Idea Backlog.


## Open Questions to Define
- Which specific QOL features will be implemented.
- Content of the ROM hack (will it be close to a base Emerald, or a brand new ROM hack in a brand new region that I design?)
- For the stat systems: pick the EV model (manual level-up points vs. crafted management). Determines what crafting's recipe list contains.
- For the berry farm/crafting: what unit of progress drives the daily harvest tick.

## Testing & Save Conventions
- **Two save types, two jobs.** Save states (`.sgm`/`.ss*`) are instant snapshots —
  reliable across *data* edits, but can desync after *code* edits (they photograph
  memory). In-game battery saves (`.sav`) are the game's own format, re-read cleanly
  every boot, so they survive *any* rebuild. Use save states for the fast active-test
  loop; keep a `.sav` for checkpoints that must outlast bigger code changes.
- **Slot map (fill in as I go — slots are just numbers):**
  - Slot 1 = fresh start, standing outside Littleroot (banner / early-game tests)
  - (add scenarios here as they come up — e.g. "in front of a roamer")
- **Keeping saves long-term:** copy important state files into `/save_backups/` with
  descriptive names (e.g. `outside_littleroot.sgm`). Gitignored, so it never touches
  version control.
- **Later:** enable the debug menu (warp anywhere + set story flags) for testing
  things buried deep in the game. Phase 1+ setup, not needed yet.
  
## Useful Commands
- Go to project: `cd /Users/wyattjohnson/Documents/Creative/ROM_Hacks/pokeemerald`
- Build: `make modern -j$(sysctl -n hw.ncpu)`
- Output ROM: `pokeemerald.gba` (load in OpenEmu)
- Save a snapshot: `git add -A` then `git commit -m "message"`
- See history: `git log` (press `q` to exit) · Check current state: `git status`
\- Push to backup: GitHub Desktop "Push origin", or \`git push\` - Repoint remote (one-time, already done): \`git remote set-url origin \<url>\` - Delete a local branch: \`git branch -D \<name>\`
- Check what git ignores: `git status` (saves should NOT appear) ·
  `git check-ignore -v <file>` (prints the rule ignoring a file, or nothing)
  
