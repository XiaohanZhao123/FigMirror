# MANIFEST — Claude-side FigMirror bundle provenance

Source: `.codex/skills/figmirror` in this repo, at commit `1f2478acdb5e132711c0a0cf5298102505f5c9ef`.
Copied 2026-08-17. `__pycache__` excluded.

## Ported byte-for-byte

Identical to their Codex counterparts. Verify with:

```
diff -r --exclude=__pycache__ \
  --exclude=MANIFEST.md --exclude=SKILL.md \
  --exclude=orchestrator-claude.md --exclude=fit_images.py \
  --exclude=agents \
  .codex/skills/figmirror .claude/skills/figmirror
```

It must print nothing.

| sha256 | file |
|---|---|
| `e464be689dad19e19a0b6eca58bfc5d1b30bdcb2eac380f6186c06b5192e2c0d` | `references/aesthetic-library.md` |
| `0805d5c19b20604db739869fdf9c386e6cc7038c573120fc7e5b6ec73c2efd5a` | `references/drawer.md` |
| `4d25e9addbda05c54eb6e1bef094f7ba66a01557e04a92d24bae0a4d83b401f1` | `references/orchestrator-codex.md` |
| `534297b10db767c1f3d45d06452abb71af8e789cbb3d80cb8c77a672140e6f94` | `references/preprocessor.md` |
| `342cb1c9e66ac6b6a7df4b1a676cf6fb331a109f733df12bc5c1bad4786c6a1f` | `references/reviewer.md` |
| `3e25ed3fbf490d389b62b22b8ef2a3ddce2adaf78ac56aa9f6e350788bddcd4c` | `references/three-d/candidate-selection.md` |
| `8ce47378537fcade3148456e7c131ca655ab20a058811e78d178cf08b7440923` | `references/three-d/core.md` |
| `7d30e282607643dca9801e44cfed4c3e20bf0ffdb67427a80d2c2d8d2a1e428d` | `references/three-d/extrema-fold-network.md` |
| `9ee241c5d6ce01db2fdefb5219229e18dd6110c72cc53a77aaec3b2bb4b01ba6` | `references/three-d/fractured-surfaces.md` |
| `55d64622feb65d5d2c887e5ae49714570c886d518c0a3c17a1dd6e02ddaa0e72` | `references/three-d/marks-and-panels.md` |
| `83812855c73dedba30982605d6df45639321fa7354bc7f67e81572a95f0b028f` | `references/three-d/material-lighting.md` |
| `8c7a902b7e0a26c6d1474ab802407068dee3c01f7e8af0c3a07e1119dcfd764a` | `references/three-d/patch-composition.md` |
| `3d26bd3440cabc8d19befb19af6fa9d540b2ca065f430f73a2747ecf083fb84e` | `references/three-d/repair-feedback.md` |
| `87771110e46af4c8222c00213a979659814e87d56fa9557481a714570a2f256d` | `references/three-d/reviewer-scorecard.md` |
| `2a9ac9b5f6c15e4ddabe193e74198098966043a7fb17b9f1531d887cc29be91d` | `references/three-d/scale-occupancy.md` |
| `ee124c42df7bac2fecdb87ea064df71e1fede1e0d6f9d81d89ad9708995daaec` | `references/three-d/strict-reproduction.md` |
| `96f2e3d3cba7a3bd12df8009788dbcdbed6d4c04852fa21818074b1e4c6e4858` | `references/three-d/style-transfer.md` |
| `863faeaabacb531b6758ac5e2ccd3e3534fc72c20da56e8d9c0af17f610f0da5` | `references/three-d/surfaces.md` |
| `8058c83f8e6453e39ed0e08c62f45288eb1b9d8c57733e2676d53e45f162fd21` | `references/three-d/volumetric-surfaces.md` |
| `2f402f63996f3eb4f477c308185bf16fa43323b424d60eb84f6783dea2c36239` | `references/three-d-prompting.md` |
| `bad12db349a7385b58e73bac65c4b8683d24f6d346777bbdee91f1b0531fa2ec` | `scripts/figannot.py` |
| `2c96431c71edbc458107f9fcdb2b848d25dc58334512dfb214abd9a2ce77c31d` | `scripts/score_3d_candidates.py` |

`references/orchestrator-codex.md` is in that list on purpose. It is not
followed at runtime; it is the diff baseline that makes every change in
`orchestrator-claude.md` reviewable.

## Not carried across

`agents/openai.yaml` — the Codex UI manifest (`display_name`,
`short_description`, `default_prompt`). It configures how the skill is
presented in the Codex client and nothing here reads it, so carrying it
would be dead weight that implies a Claude-side agents layer exists.

## Adapted for this harness

`SKILL.md` is the entry point Claude actually loads, so it cannot stay
byte-identical: the Codex version names `orchestrator-codex.md`,
`spawn_agent`, `fork_context`, and the `items` image-attachment channel,
none of which exist here. Changed lines, and nothing else:

- "main Codex process" / "top-level Codex process" -> "main `claude` process"
- Drawer and Reviewer dispatch: `spawn_agent` + `fork_context=false` ->
  `Task` with `subagent_type` + `run_in_background=false`
- Reviewer image delivery: one structured `items` payload of attached pixels
  plus a no-`view_image` rule -> an ordered list of absolute paths the
  Reviewer opens with `Read`, once each
- Loop-wiring reference and staged prompt filename: `orchestrator-codex.md` ->
  `orchestrator-claude.md`
- Step 6 additionally names `scripts/fit_images.py`; the artifact layout adds
  `tools/fit_images.py` and `image_fit_<N>.json`

No step, budget, artifact name, or decision rule changed.

## Added for this harness

| sha256 | file | why |
|---|---|---|
| `0076e07bb5181d4652fc5d44fd91e9a8803b8e7cafab06fca947b690a09e795e` | `references/orchestrator-claude.md` | Dispatch-mechanism port of orchestrator-codex.md. Algorithm, decision state machine, iteration budget and fail-closed rules unchanged. |
| `121b4880c7c1d88a9b42925846f7301866f389ed82ddb5b6c57996a463950201` | `scripts/fit_images.py` | Fits staged audit-view images to the 2000px delivery limit so the delivered pixels are a recorded property of the run. Refuses any path outside audit_view_<N>/, and composite.png inside it. |
