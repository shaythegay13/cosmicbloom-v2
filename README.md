# Cosmic Bloom

Cosmic Bloom is Shay Bouchles’s second iteration of a farming game. This repository is that iteration: a Unity 6 project named **Cosmic Bloom**, created from Unity’s Universal Render Pipeline (URP) empty template. It does not yet contain gameplay.

The playable game is the first iteration, [cosmicbloom-v1](https://github.com/shaythegay13/cosmicbloom-v1). In that repository the product is **Farm & Give** (npm package `farm-and-give`, Capacitor app id `com.farmandgive.app`). This README describes what is in each repository after reading the code. It does not add features that are not implemented.

## What this repository is

Player Settings set the product name to **Cosmic Bloom**, the company name to **DefaultCompany**, and the bundle version to **0.1.0**. The editor version pinned in `ProjectSettings/ProjectVersion.txt` is **6000.5.1f1** (changeset `0d9463e84828`).

The only scene enabled for builds is `Assets/Scenes/SampleScene.unity`. That scene contains three objects:

- a Main Camera
- a Directional Light
- a Global Volume using `Assets/Settings/SampleSceneProfile.asset`

There is no player, crop, inventory, save file, network client, or game-specific script. The C# in the repository is the URP template readme (`Assets/TutorialInfo`) plus `Assets/Editor/HubForceResolve.cs`, an editor script that resolves packages once and then deletes itself.

`Assets/InputSystem_Actions.inputactions` is the Input System asset shipped with the template. Editor Build Settings point the project at it. Nothing in the sample scene reads it. The asset defines two maps:

| Map | Actions |
| --- | --- |
| Player | Move, Look, Attack, Interact, Crouch, Jump, Previous, Next, Sprint |
| UI | Navigate, Submit, Cancel, Point, Click, RightClick, MiddleClick, ScrollWheel, TrackedDevicePosition, TrackedDeviceOrientation |

Active input handling is the new Input System (`activeInputHandler: 1`). Color space is Linear. The current quality level is **PC** (index 1), which uses `Assets/Settings/PC_RPAsset.asset`. The **Mobile** quality level uses `Assets/Settings/Mobile_RPAsset.asset`. The graphics settings also assign the PC pipeline as the custom render pipeline.

## How it plays

Pressing Play in the Unity Editor runs `SampleScene`. The view is the empty URP scene: a camera, a light, and a post-processing volume. There is no objective, control scheme wired to a character, or win condition in this repository.

`HubForceResolve` does not run in a player build. It runs in the Editor, calls the Package Manager resolve API on the first idle pass, and on the next idle pass deletes its own asset. Its asset paths use forward slashes so that delete works with `AssetDatabase` on Windows, macOS, and Linux.

## Stack

| Piece | What the project uses |
| --- | --- |
| Engine | Unity 6000.5.1f1 |
| Render pipeline | Universal Render Pipeline 17.5.0 |
| Input | Input System 1.19.0, template action asset only |
| UI package | uGUI 2.5.0, declared but unused by the scene |
| IDE support | Visual Studio Editor 2.0.26, JetBrains Rider Editor 3.0.38, `.vsconfig` workload `Microsoft.VisualStudio.Workload.ManagedGame` |
| Solution | `Cosmic Bloom.slnx`, which points at generated `Assembly-CSharp.csproj` and `Assembly-CSharp-Editor.csproj` files. Those projects are gitignored and appear after the Editor generates them. |

`Packages/manifest.json` also declares these packages. No script in this repository calls into them:

- AI Assistant 2.13.0-pre.2
- AI Inference 2.6.1
- AI Navigation 2.0.13
- Version Control 2.12.4
- Multiplayer Center 1.0.1
- Test Framework 1.7.0
- Timeline 1.8.12
- Visual Scripting 1.9.11

The rest of the manifest is the standard set of built-in Unity modules (`com.unity.modules.*`). Locked versions are in `Packages/packages-lock.json`.

There is no `package.json`, test suite, or player-build script in this repository.

## Architecture

```
Assets/
  Editor/HubForceResolve.cs     Editor-only package resolve, then self-delete
  InputSystem_Actions.inputactions
  Readme.asset                   In-Editor “URP Empty Template” page
  Scenes/SampleScene.unity       The only enabled scene
  Settings/                      PC and Mobile URP assets, renderer data, volume profiles
  TutorialInfo/                  Readme ScriptableObject, custom editor, template icon
Packages/manifest.json
Packages/packages-lock.json
ProjectSettings/                 Player, quality, graphics, input, and build settings
Cosmic Bloom.slnx
```

Text serialization is forced (`m_SerializationMode: 2` in Editor Settings). Unity YAML and the Input System JSON use LF via `.gitattributes`. Generated folders (`Library/`, `Temp/`, `Logs/`, `UserSettings/`, `Build/`, `Builds/`) are gitignored, as are `*.csproj` and `*.sln`.

The template icon `Assets/TutorialInfo/Icons/URP.png` is a normal Git blob. `.gitattributes` otherwise marks `*.png` for Git LFS. That file is excluded from the LFS filter so a checkout does not show it as modified and a later add does not replace the image with an LFS pointer.

## Setup

1. Install **Unity 6000.5.1f1** (changeset `0d9463e84828`) with Unity Hub, including the platform modules you want to build. A different editor version will offer to upgrade the project.
2. Open this folder as a Unity project. The Editor imports packages from `Packages/manifest.json` using `Packages/packages-lock.json`.
3. Open `Assets/Scenes/SampleScene.unity`.
4. Enter Play mode.

No environment variables are required. This project does not load a `.env` file. `.env` is gitignored. `.env.example` is tracked and currently lists no names, because nothing in the project reads one. If a variable is added later, put the name in `.env.example` with an empty value and keep the real value local.

There is no build script in the repository. A player build is made from the Unity Editor’s build window. The enabled scene list contains only `Assets/Scenes/SampleScene.unity`.

## Screenshots

This repository has no screenshots. Unity 6000.5.1f1 was launched in batch mode against this project and exited before it imported assets or entered Play Mode, because the machine had no Unity Editor license. No player build or Play Mode capture exists.

## Comparison with v1

Source: the public repository [shaythegay13/cosmicbloom-v1](https://github.com/shaythegay13/cosmicbloom-v1), default branch `main`, read from the files cited below. v1’s own README describes the product as “Farm & Give — Rewind the Planet.”

| | v1 (`cosmicbloom-v1`) | v2 (this repository) |
| --- | --- | --- |
| Product name in code | Farm & Give (`client/index.html` title, `capacitor.config.json` `appName`) | Cosmic Bloom (`ProjectSettings` `productName`) |
| Package / app id | npm `farm-and-give` 1.0.0; Capacitor `com.farmandgive.app`; Android package `com.farmandgive.app` | Unity bundle version 0.1.0; company `DefaultCompany`; no application identifier set |
| What you can play | A 2D farm in the browser, with Capacitor `ios/` and `android/` projects | The empty URP sample scene |
| Client | Vanilla JavaScript ES modules and an HTML5 canvas, built with Vite (`client/`, `vite.config.js`) | Unity scene; no game scripts |
| Server | Node.js (`.nvmrc` is `20`) and Express, MongoDB via Mongoose (`server/`) | None |
| Tests | Vitest unit tests and a supertest API test; GitHub Actions in `.github/workflows/ci.yml` | None |
| License file | No `LICENSE` file. `package.json` and `README.md` say MIT | No license file |

### How v1 plays

Boot (`client/js/main.js`) shows login and registration. With a stored token it loads the account, and if the request fails but a local save exists it continues offline. A new account picks one of five farmers from `GET /api/game/characters` (`server/routes/game.js`): Sunny, Luna, Terra, River, and Sage. The game then starts a fixed-timestep canvas loop (`client/js/game.js`, 60 Hz, at most five steps per frame).

A new save (`client/js/player.js`) starts at level 1 with 100 coins, on the Prairie Heartland, with 5 sunflower seeds and 3 corn seeds. The farm is a tile world built from the biome and a per-farm seed (`client/js/scenes/farm.js`). Keyboard movement uses WASD or the arrow keys. A tap pathfinds the farmer to a walkable tile, or to a tile beside a farm action (`client/js/engine/input.js`, `client/js/engine/pathfinding.js`). Dragging pans the camera. Space or Enter acts on the tile the farmer is facing.

Farming (`client/js/farming.js`) is one contextual tap on grass: till, plant the selected seed, water, or harvest. A planted crop is thirsty. Watering starts a real-time stage timer (`growMs` on the crop, 20–58 seconds in `client/js/data/biomes.mjs`). When that timer elapses, the crop advances one stage and becomes thirsty again, until stage 4, which is ready to harvest. On load or resume, a watered crop whose timer already elapsed advances that one stage. It does not skip ahead through later stages that were never watered.

Harvest pays the crop’s `sellPrice` and XP, then adds restoration points (`client/js/restoration.js`). Native crops add 1.5× their `restore` value. The score is clamped to 0–100. Crossing 25, 50, 75, and 100 grants 60, 150, 300, and 600 coins, labeled Seedlings of Hope, Roots Take Hold, Wild Return, and Fully Rewound. Tiers change at scores 34 and 67. The farm scene uses the tier for ground color, obstacle frames, and the biome’s ambient tint. Wildlife count is 0 below a restoration score of 40, then rises to at most 4 (`client/js/engine/creatures.js`). Tapping a creature plays a sound and a small heart burst.

Farmer effects implemented in `farming.js` (the API also stores a short bonus id for each character):

- Sunny: growth timers are multiplied by 0.9
- Luna: harvest coins are doubled when the local hour is 18–23 or 0–5
- Terra: harvest coins are multiplied by 1.15
- River: watering has a 20% chance to also water an orthogonally adjacent thirsty crop
- Sage: harvest XP is multiplied by 1.25

Seven regions are defined in `client/js/data/biomes.mjs`, in this order: Prairie Heartland (North America), Old World Countryside (Europe), Misty Rice Terraces (Asia), Golden Savanna (Africa), Emerald Rainforest (South America), Red Earth Outback (Australia), and Aurora Tundra (The Arctic). Each has four crops, two creatures, and an unlock cost. The world map (`client/js/scenes/worldmap.js`) opens a region only after the previous region’s restoration is at least 50. The unlock panel (`client/js/ui.js`) stays disabled until the player also has the listed coin cost and level. Confirming it spends the coins, grants 3 of that region’s native seeds, and travels there.

The shop sells the current region’s seeds for their `seedPrice`. A first-run tutorial (`client/js/tutorial.js`) walks through till, plant, water, harvest, buying a seed, and opening the map, and can be skipped.

Saves are written locally under the key `fag_save_v2` (`client/js/storage.js`). A change flushes about 800 ms later. The game loop requests another local save about every 30 seconds, and it flushes immediately on pause or page unload. About once a minute, and on pause, the client increments `saveVersion` and posts the snapshot. The server (`server/routes/game.js`) rejects a schema other than 2, out-of-range coins, level, or restoration, more than 1000 plots on a farm, or a snapshot larger than 300000 characters, and it ignores a snapshot whose `saveVersion` is older than the stored one. When both copies exist, the client keeps the one with the higher `saveVersion`.

Purchases are defined as four consumable coin products in `server/config/products.js`: `fag_coins_pouch` (500), `fag_coins_chest` (1500), `fag_coins_wagon` (4000), and `fag_supporter_pack` (2500). `initIap` (`client/js/iap.js`) returns without doing anything unless the app is a native Capacitor build and `VITE_REVENUECAT_KEY_IOS` or `VITE_REVENUECAT_KEY_ANDROID` is set for that platform. When both are present it configures RevenueCat with the Mongo user id, lists the current offering, and claims coins from `POST /api/payments/sync`. The server credits coins only from the RevenueCat webhook (`server/routes/payments.js`), after the `Authorization` header matches `REVENUECAT_WEBHOOK_AUTH`.

The charity UI says “We donate 10% of our proceeds to community gardens.” The server records that share: `CHARITY_PERCENTAGE` (default 10) times the webhook price is added to the user and to the selected charity’s `totalReceived`. Refund and cancellation events reverse that ledger entry. The code does not send money to an external organization. Players can select a verified charity and query nearest gardens with latitude and longitude (`server/routes/charity.js`).

v1’s `.env.example` names these variables and no others: `PORT`, `NODE_ENV`, `MONGODB_URI`, `JWT_SECRET`, `REVENUECAT_WEBHOOK_AUTH`, `CHARITY_PERCENTAGE`, `CLIENT_URL`, `VITE_API_URL`, `VITE_REVENUECAT_KEY_IOS`, `VITE_REVENUECAT_KEY_ANDROID`. No `.env` file is present in that repository. Values are not copied here.

Sprite atlases for the seven biomes and a core atlas live in `client/public/atlases/`. `npm run art` runs `tools/art/build.mjs`. CI regenerates that art and fails if the atlas output differs from the commit.

## Contributors

From the git history of **this** repository (`git log`):

- **Shay Bouchles** — the only commit, `09011d7`, 2026-07-03, “Initial check-in.”

From the public history of **cosmicbloom-v1**:

- **shaythegay13** — 2025-10-08, “Initial commit: Farm & Give - 2D web version + Unity 3D planning.”
- **Claude** (git author `Claude <noreply@anthropic.com>`) — eight commits on 2026-06-10 covering the Vite conversion, generated art, the farm engine, backend, Capacitor projects, store pages, and tests.
- **Shay Bouchles** — 2026-06-11, merge of pull request #1 (`claude/session-q6x9es`).

## License

This repository has no license file. v1’s `package.json` and README name the MIT license, and v1 also has no `LICENSE` file.
