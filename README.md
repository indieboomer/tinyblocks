# tinyblocks
**Project Brief: A Living Voxel Island**

Build a cozy procedural construction sandbox where the player shapes an island and its buildings using simple additive and subtractive brushes.

The core experience is:

**Make a small change → watch the world adapt intelligently → discover an interesting result → keep experimenting.**

Take inspiration from the immediate satisfaction of Townscaper, the adaptive architecture of Tiny Glade, and the living settlement concept of Brushland. Develop an original visual identity and procedural implementation.

The project has three sequential milestones:

1. Add and dig terrain.
2. Add and destroy procedural buildings.
3. Add autonomous inhabitants.

Each milestone must produce a playable, enjoyable build before proceeding.

**Core design principles**

- The world uses small, visible voxels, with attractive lighting, coherent colors, and readable silhouettes.
- Players work with brushes affecting meaningful volumes, rather than placing individual blocks.
- Procedural systems interpret player edits and generate appropriate details.
- Preserve deliberate cuts and recognizable existing structures.
- Changes should remain local unless wider changes are necessary.
- Undo and redo must restore both player edits and their procedural consequences.
- No resource gathering, combat, failure conditions, or complex economy in the initial prototype.

**Step 0 — Establish the technical foundation**

Inspect the existing project, if one exists, before choosing technology. Reuse working rendering, input, and voxel infrastructure where practical.

Separate authoritative world data from generated geometry:

- Player-authored terrain.
- Player-authored building volumes and intentional cuts.
- Semantic building data: floors, rooms, entrances, connections, and structural elements.
- Generated voxel materials, architectural details, collision, and navigation.

Use chunked world storage and localized regeneration. Keep procedural choices stable through persistent identifiers and deterministic seeds.

Implement an orbit camera, brush preview, adjustable brush size, save/load, and undo/redo. Provide debug views for authored volumes, generated geometry, and affected regions.

Keep the first scene small. Prioritize responsiveness and edit stability over world size.

**Step 1 — Add / Dig Terrain**

Create a small island surrounded by water.

The player can add and remove terrain with a volumetric brush. Support hills, cliffs, terraces, tunnels, caves, and overhangs; terrain must not be limited to a heightmap.

After each edit, automatically update local surface materials using contextual rules:

- Exposed, sufficiently gentle ground receives grass.
- Steep slopes expose rock.
- Appropriate low shoreline surfaces receive sand.
- High, exposed surfaces can receive snow.
- Cave ceilings and enclosed underground surfaces remain rock or soil.

Use altitude, slope, exposure, water proximity, and neighborhood information. Avoid grass on underground ceilings and snow appearing solely because an enclosed surface is high.

Begin with deterministic rules and smooth transitions. WFC is optional and should only be introduced if it solves a demonstrated problem.

Use a fixed sea level initially. Full fluid simulation is outside this milestone.

Add satisfying edit feedback: clear previews, subtle particles, and material-sensitive sounds.

**Acceptance criteria**

- Sculpting is enjoyable without buildings or inhabitants.
- Terrain updates remain localized and visually coherent.
- Tunnels and overhangs survive regeneration and save/load.
- Undo/redo restores the exact previous result.
- Large edits do not freeze interaction; measure regeneration cost.

**Step 2 — Add / Destroy Procedural Buildings**

Introduce a building brush that adds or removes architectural volume.

Adding volume means “extend the building into this space,” not “fill this space solid with bricks.” Generate usable interiors within the volume.

Removing volume expresses a deliberate cut. Automatic regeneration must respect it. A later explicit additive stroke may overwrite that cut.

Use a hybrid architecture:

- Parametric construction rules generate geometry.
- A semantic graph represents spaces, connections, and structural relationships.
- Local repair logic preserves valid existing elements.
- A constraint solver may select between candidate repairs when simpler rules are insufficient.

Do not make a catalog of complete prefabricated buildings the foundation. Generate walls, openings, stairs, beams, and roofs from dimensions and relationships. Reusable decorative assets are acceptable.

Implement this milestone incrementally:

**2A. Adaptive shell**

Generate walls, floor slabs, and roofs from a simple building volume. Support additions, corner removal, and extensions.

For suitable roof footprints, investigate straight-skeleton algorithms. Start with a limited, coherent architectural style and robust fallback roofs.

**2B. Meaningful cuts**

Recognize useful configurations such as recesses, passages, courtyards, and covered outdoor spaces.

Examples:

- Removing ground-floor volume beneath an upper floor can create a supported passage.
- Cutting through the center can create a courtyard.
- Adding beside an existing floor can extend a room or form a new wing.

These are context-dependent interpretations, not mandatory outcomes for every cut.

**2C. Functional interiors**

Maintain floors, rooms, entrances, doors, and vertical connections.

Reserve enough width and headroom for inhabitants. Account for the actual space needed by stairs and future usable furniture.

Preserve surviving room identities and connections where possible.

**2D. Local repair**

After an edit:

1. Identify affected geometry and semantic dependencies.
2. Retain still-valid elements.
3. Generate candidate repairs.
4. Choose a valid repair that minimizes unnecessary changes.
5. Regenerate affected geometry and validate access.

Priorities:

1. Respect the latest explicit player edit.
2. Preserve unaffected architecture.
3. Restore useful access where feasible.
4. Improve appearance.

Never silently refill an intentional passage to satisfy a structural rule. If a valid repair is impossible, allow an attractive ruin or temporarily unusable space.

Use simplified structural rules rather than full engineering simulation.

If introducing a solver, operate on rooms, openings, stair candidates, and supports—not individual visual voxels. Enforce a computation budget and handle timeout without presenting an invalid result as functional.

**Acceptance criteria**

Repeatedly edit the same building through:

- Corner removal.
- Passage creation.
- Courtyard excavation.
- Upper-floor addition.
- Stair removal.
- Splitting and reconnecting two sections.

Verify that the building remains recognizable, intentional cuts persist, remote details remain stable, and accessibility is reported correctly.

This milestone must be enjoyable before inhabitants are added.

**Step 3 — Add Autonomous Inhabitants**

Introduce a small population that uses the actual generated environment.

Start with simple activities:

- Walk and explore.
- Return home.
- Rest at a usable bed.
- Work at a designated workstation.

Represent usable objects with interaction positions and clearance requirements. A bed or workstation only functions when an inhabitant can physically reach and use it.

Navigation must support terrain, bridges, tunnels, interiors, and multiple floors. Update affected navigation after edits.

Handle world changes explicitly:

- Replan when a route changes.
- Stop using destroyed objects.
- Reassign activities when a room becomes inaccessible.
- Recover gracefully if terrain or a floor is removed beneath an inhabitant.

Begin with simple state machines or utility-based decisions. No language model is required for routine behavior.

Once basic behavior is reliable, add restrained environmental evolution: worn paths, small gardens, or minor decorative additions. Major construction remains player-controlled.

**Acceptance criteria**

Opening a shortcut visibly changes routes. Removing stairs makes the upper floor unavailable. Adding an accessible room allows it to be occupied. Inhabitants do not walk through walls or use unreachable furniture.

**Implementation workflow**

Deliver working increments in milestone order. Make routine engineering decisions autonomously and record important tradeoffs.

For each increment, provide:

- A runnable build.
- Brief controls and reproduction instructions.
- What changed and known limitations.
- Relevant performance measurements.
- Focused verification of edit persistence, geometry validity, and accessibility.

Start by implementing Step 0 and Step 1. Establish a convincing terrain-editing toy before expanding into architecture.
