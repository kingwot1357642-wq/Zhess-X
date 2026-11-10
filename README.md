# Zhess X

## Render settings
- Runtime: Node
- Build Command: `npm install`
- Start Command: `npm start`
- Root Directory: leave blank

## Online gameplay included
- Create/join a 6-character room code.
- Both players must mark Ready.
- Each player independently selects their own roster (no duplicate unit type within that roster; population cap 15).
- When both rosters are submitted, the server starts the match and randomly chooses the first turn.
- Both players see the same board state. Player 2 sees the board rotated 180 degrees so their own side is at the bottom.
- Moves, skill effects, cooldowns, HP/status changes, and turn changes are sent to the other player through Socket.IO.
- The previous bot is disabled during online matches; offline bot logic remains in the original game code.

## Art and effects
- All pictures are SVG files in `public/img/` (`char/<unit>.svg` for the 9 characters, `icon/<name>.svg` for skill/status/UI icons). To change a picture, replace the file with the same name (keep a 64x64 viewBox for characters, 24x24 for icons). The frame, team glow and skill effects are still drawn by script in `public/index.html`. Adding a new unit = add `img/char/<key>.svg` and its entry in the `R` table.
- Every skill has its own SVG effect (about 1-2 seconds). Effects are sent to the opponent together with the game state, drawn on the rotated board for Player 2, and effects of an invisible (Wind-Walk) unit's dash/teleport are not shown to the opponent.

## v3 additions
- New units: Red Scales (Epic, pop 5, always flying, lands after any skill until its next turn) and Juggerknight (Epic, pop 4, 50% parry vs physical, Crushing Leap).
- Tag system: skills carry tags (phys, magic, melee, ranged, ground, fire, water, sword ...) and units carry tags (dragon, human ...). Ranged = range > 1 or the ranged tag without melee. Ground-tagged skills cannot hit flying units.
- Arena: every match randomly gets one of 4 themes (ancient stone, night castle, lava, glowing forest); the server picks it so both players see the same one.
- Turns: a banner announces your turn, and each turn lasts 60 seconds (shown next to MP/AP). At zero the turn is ended automatically; if that player's device is stuck, the opponent's device ends it 6 seconds later.
- Tags: a skill counts as ranged only if it carries the `ranged` tag (reach above 1 cell does not count), so grounded Red Scales can still be hit by long-reach melee skills like Crushing Leap.
- Characters are faceless silhouettes on element-coloured backgrounds, in a metallic frame (shape by rarity, colour by element) with a blue/red team glow.

## Important limitations
- This is an early playable PvP implementation using client-generated state snapshots relayed by the server. It is suitable for private testing with trusted players, not cheat-resistant competitive play.
- Deployment: after both rosters are confirmed, each player secretly places their units on their own two back rows (20 cells) within 60 seconds (countdown shown). Both layouts are revealed together when both confirm; anyone who runs out of time gets the remaining units placed at random. Offline games use the same screen, and the bot places its army at random.
- Rooms and matches are stored in memory and are lost if the Render service restarts.
- Rematch: after a match ends, both players can press "เล่นอีกครั้ง" to return to roster selection in the same room. If someone leaves or loses connection, the other player goes back to the lobby; a dropped connection closes the old room (reconnecting into a running match is not implemented yet).
