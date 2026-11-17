# Graham's T3 Code fork

Personal fork of [pingdotgg/t3code](https://github.com/pingdotgg/t3code) (MIT) at
[lordydord/t3code](https://github.com/lordydord/t3code). Checkout on the Mac mini:
`~/Documents/Technical/T3 Code Fork`.

- `main` mirrors upstream. Do not commit to it.
- `graham-tweaks` holds the custom changes, one commit per tweak, rebased on top of upstream.
- Remotes: `origin` = the fork, `upstream` = pingdotgg/t3code.

## Tweaks (branch `graham-tweaks`)

| Tweak                                                                                                                                                                   | Where                                                                                                                                                                                                                              | Status                                         |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| **Response font**: separate font family and size for assistant replies; the sidebar keeps the interface font                                                            | `packages/contracts/src/settings.ts` (`fontFamilyResponse`, `fontSizeResponse`), `apps/web/src/appearanceFonts.ts` (`--font-response`), `TypographySettings.tsx`, `TimelineAssistantRows.tsx` (`chat-response` class), `index.css` | Done, 16 Nov 2026                              |
| **Cowork theme**: Claude Cowork dark greys (chat `#191919`, sidebar `#262627`, composer `#303030`, selected row `#464647`), clay orange accent `#d97757`, light variant | `packages/shared/src/themePalettes.ts` (`COWORK_THEME`), registered in `apps/web/src/themePalette.ts` and `ThemeSettings.tsx`. Web/desktop only, deliberately not in `BUILT_IN_THEME_IDS` so mobile is untouched                   | Done, 16 Nov 2026                              |
| Model + reasoning slider in the composer model picker                                                                                                                   |                                                                                                                                                                                                                                    | Planned                                        |
| Per-scheduled-task model that agents can change                                                                                                                         |                                                                                                                                                                                                                                    | Planned (also worth offering upstream as a PR) |

Settings to pick in the app: Appearance → Theme **Cowork**, Interface font **Anthropic Sans**,
Response font **Anthropic Serif** (16 px).

The Anthropic fonts are **not** in the repo (they are Anthropic's). They were copied for
Graham's user only from `/Applications/Claude.app/Contents/Resources/fonts/` into
`~/Library/Fonts/`. On another Mac, copy them the same way.

## Pulling official updates

```sh
cd ~/Documents/Technical/"T3 Code Fork"
. ~/.config/vite-plus/env
git fetch upstream
git checkout main && git merge --ff-only upstream/main && git push origin main
git checkout graham-tweaks && git rebase main     # resolve any conflicts, then:
vp i
vp run check:changed
git push --force-with-lease origin graham-tweaks
vp run dist:desktop:dmg:arm64                     # DMG lands in release/
```

Keep only the newest DMG in `release/` (disk rule: one previous version at most).

## Notes

- Tooling: Vite+ (`vp`) installed at `~/.local/share/vite-plus`; the installer added
  `. "$HOME/.config/vite-plus/env"` to `~/.zshrc` (puts `vp`, `pnpm`, `bun` shims on PATH;
  installed with `VP_NODE_MANAGER=no`, so the system `node` is unchanged).
- Rust (needed for the Mac build) installed with rustup, minimal profile, `--no-modify-path`:
  run `. ~/.cargo/env` before `vp run dist:desktop:*`.
- Local builds are unsigned and have no update feed, so the built app's auto-updater is off and
  official releases cannot overwrite it. Updates come only through the steps above.
- The fork and the official app share `~/.t3/userdata`. Run only one of them at a time.
- Never run `vp run dev` here without `--home-dir <folder outside the repo>`: without it the dev
  server uses `~/.t3/dev`. Seed a sandbox with
  `vp run migrate-dev-db --base-dir <sandbox>` (read-only copy of recent chats) and delete it after.
