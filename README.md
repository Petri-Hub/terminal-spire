# 🃏 Terminal Spire

> A card roguelike that runs in the terminal, written in plain TypeScript to stress object orientation. No game engine, no framework — two small libraries for colour and input, and nothing else.

I had just finished a book on OO and wanted to apply it somewhere inheritance alone wouldn't survive. A deck-builder is a good target for that: cards, enemies, rooms and effects each vary on their own axis, so the naive class tree explodes fast.

## What's in it

A game loop walks a dungeon of floors. Each floor is a room — battle, choice or shop — and a battle puts the player against an enemy, both of them entities with health and defense.

Cards are what the player spends turns on. Playing one produces actions, actions produce results, and presenters turn those results into text. Values like damage, defense and cost don't hold numbers directly; they hold a calculation and ask it.

Actions are the middle of everything. What's above decides *what* happens, what's below decides *how much* and *how it reads*.

## The three decisions that mattered

**🎲 Randomness had to be a strategy, not a subclass.** The obvious move is a damage card and a random damage card. Then you want random cost, and random defense, and the class tree doubles on every axis. So values delegate the number to a calculation instead — static and random are two implementations of the same thing, and no card class ever knows the difference.

**🧩 A group of actions is an action.** A card that deals damage *and* grants defense isn't a special case. The group implements the same contract as a single one, and returns the same kind of result, so nothing above ever branches on it.

**🖨️ Nothing in the combat layer writes to the screen.** Actions return results, presenters decide how to describe them. That's what makes the fight testable and the output swappable — moving off the terminal means writing new presenters and touching nothing else.

## Stack

|  | What | Why |
|---|---|---|
| 🟦 | **TypeScript 5.5** | Interfaces are the whole design here. Without them this project has no point |
| 🟩 | **Node** with `tsx` | Runs TypeScript directly, so there's no build step between an edit and a run |
| 🎨 | **chalk 5** | Colour in the terminal. Health bars and card rarity need to be readable at a glance |
| ⌨️ | **readline-sync** | Blocking prompts. A turn-based game waits for input anyway, so async here would be ceremony |
| 🧪 | **Jest** with `ts-jest` | Only the draw pile is covered so far |
| 🧹 | **ESLint 9 + Prettier** | Formatting stays out of the diff |

No game engine, no ECS, no state library, no rendering library.

## If you're poking around

The interesting parts are `app/interfaces/` — that's where the whole design is declared in about six small files — and `app/actions/`, where the composite lives.

`app/rooms/ShopRoom.ts` and `app/behaviors/` are the thinnest parts. The shop works but barely, and enemies pick their move from a loop rather than anything you'd call AI. If you were going to extend it, that's where the room is.

## Running

```sh
# install dependencies
npm install

# play — nodemon restarts on every change
npm run dev

# run the tests in watch mode
npm test
```

## License

MIT — see [LICENSE.md](LICENSE.md).
