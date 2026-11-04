# Copilot instructions

Single-file space exploration quiz: everything (HTML, CSS, JS) lives in `index.html`. No build, no dependencies, no server, no package manager, no test or lint tooling.

## Running and verifying

- Open `index.html` directly (`file://` URL) in a browser; reload after edits.
- There is no test suite. Verify behavior in a real browser by driving the DOM (click options, click `#next`, check `#score`, `#fill`, `#results`) — a full playthrough of all 10 questions plus "Play again" is the smoke test.

## Architecture

- **Data**: the `Q` array holds questions as `[prompt, options[], correctIndex, fact]`. Add or edit questions there; the UI, progress bar (`scaleX(i / Q.length)`) and results screen derive from `Q.length`.
- **State**: module-level `i` (current question), `score`, `locked` (blocks double answers). `render()` draws question `i`; `pick(k)` grades; `finish()` shows results and picks the emoji/message by score ratio; `#again` resets.
- **Three cards**: `#start` (name form), `#quiz` and `#results` are toggled with the `.hidden` class. The player name is trimmed, saved to `localStorage` (`quizName`) and shown in `#player` and the results greeting. "Play again" returns to question 1 without re-asking the name.

## Conventions

- **Theming**: colors come only from CSS variables on `:root`, overridden in a single `prefers-color-scheme: dark` block. Use the variables (`--accent`, `--good`, `--bad`, etc.) rather than hardcoded colors; the few intentional hardcoded exceptions (`.score` is `#000` light / `#fff` dark) must be mirrored in the dark media query.
- **Animation**: animate only `transform`/`opacity` (the progress fill uses `transform: scaleX`, not `width`); no bounce/elastic easing; no glowing colored shadows. A `prefers-reduced-motion` rule disables all animations/transitions.
- **Accessibility**: options are native `<button>`s; the progress element has `role="progressbar"`; feedback text is in an `aria-live` region. Focus is moved deliberately (to `#next` after answering, to the first option on each new question, to `#again` on results) because `#next` is hidden via `visibility` — preserve this when changing flow.
- **Layout/typography**: single centered column (`max-width: 480px`), system sans-serif stack, 15px body.
- Feedback classes: `.correct` (green, `pop` animation) and `.wrong` (red, `shake` animation) are applied to option buttons; the correct option is always highlighted after an answer.
