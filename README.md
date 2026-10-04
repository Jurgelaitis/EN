# Lexicon C2

A single-file English–Lithuanian vocabulary app for learners moving from B2/C1 toward C2. Built for quick morning reviews, active recall, and exploring a large personal vocabulary collection.

## Run the app

Open `index.html` in a modern browser. No installation, account, backend, or build step is required.

For a consistent local address, you can also run this command from the project directory if Python 3 is installed:

```sh
python3 -m http.server 8765 --bind 127.0.0.1
```

Then visit <http://127.0.0.1:8765>. Use the same browser and address each time to retain access to the same saved progress. Opening the file directly and using the local server use separate browser storage locations.

Tailwind CSS loads from a CDN. Core styling, application logic, and vocabulary are embedded in the HTML, but this is not a fully bundled offline distribution. Pronunciation depends on the voices available in your browser or operating system.

## Features

- **Morning Blitz:** up to 100 curated cards per day, with grid and single-card recall modes, Lithuanian meanings, IPA, examples, nuance notes, and audio.
- **Vocabulary Vault:** searchable table with 100 entries per page, status filters, categories, bookmarks, and word-detail dialogs. Search covers words, translations, categories, and context; Lithuanian diacritics are optional.
- **Grammar & Nuance:** six lessons covering inversion, mixed conditionals, the mandative subjunctive, punctuation, hedging, and concession, with practice questions and explanations.
- **Phrases & Idioms:** contextual cards for entries categorized as `Phrasal Verbs` or `Executive Phrases`.
- **Learning statistics:** daily reviews, mastered and pending totals, bookmarks, and an activity streak.
- **Appearance:** responsive layouts, dark/light themes, and reduced-motion support.

## Daily review workflow

1. Open **Morning Blitz** and choose grid view or **Start focused review**.
2. In recall mode, think of the meaning before revealing the card.
3. Choose an action:
   - **Mastered:** marks the entry as mastered.
   - **Review later:** bookmarks the entry; if it was mastered, returns it to pending.
   - **Needs work:** marks the entry for additional practice.
4. Review each card in the daily collection to complete the session.

The queue prioritizes curated entries marked **Needs work**, then other unmastered curated entries, and fills any remaining places with mastered curated entries. It is randomized within those priorities and saved for the day. Repeated actions on the same card count only once toward daily completion. Flipping or listening alone does not count as a review.

Only actions taken in Morning Blitz count toward its daily total. Marking vocabulary elsewhere or answering a grammar question still records an active day. Merely opening the app does not extend the streak. Dates follow the device's local calendar.

## Keyboard shortcuts

These shortcuts work in Morning Blitz **recall mode**, outside text fields and dialogs.

| Key | Action |
| --- | --- |
| Space | Reveal or hide the translation |
| Left / Right arrow | Previous / next card |
| M | Mark mastered and advance |
| B | Save for later and advance |
| N | Mark needs work and advance |
| A | Pronounce the current word |

When a standard button has keyboard focus, Space activates that button normally.

## Included vocabulary

The app contains **8,488 entries**, including **110 curated study cards**, after merging overlapping terms from the curated set and the supplied files:

- `Išsaugoti vertimai.xlsx`
- `EN words.docx`

Imported entries appear under **Personal collection**, retain their original translations, and are labeled **Unrated**. These translations have not been linguistically verified; some may need correction. Most imported entries do not have edited context sentences or IPA. Curated C1/C2 labels are indicative study bands, not certified CEFR ratings.

The original documents are not needed to run the app. Editing those documents does not update the embedded vocabulary automatically.

## Add or edit vocabulary

Open `index.html` in a text editor and find `const USER_DATA = { ... }`. Add objects to its `vocabulary` array, or update existing entries. For example:

```js
{
  "id": "personal-0001",
  "word": "circumspect",
  "meaning": "apdairus, atsargus",
  "ipa": "ˈsɜːkəmspekt",
  "level": "C2",
  "category": "Nuanced Synonyms",
  "context": "She was circumspect about endorsing a proposal whose costs remained unclear.",
  "nuance": "Careful to consider possible risks before acting or speaking.",
  "source": "Personal study collection",
  "curated": true
}
```

Keep commas between array entries and preserve valid JavaScript syntax.

| Field | Purpose |
| --- | --- |
| `id` | Required unique, stable identifier; saved progress refers to this value. |
| `word` | Required English word or expression. |
| `meaning` | Required Lithuanian translation. |
| `ipa` | Pronunciation without surrounding slashes. |
| `level` | Study band, such as `C1`, `C2`, or `Unrated`. |
| `category` | Category used by filters and the phrase section. |
| `context` | Example sentence illustrating the intended sense. |
| `nuance` | Usage, register, or meaning distinction. |
| `source` | Provenance shown in the detail dialog. |
| `curated` | Set to `true` to make the entry eligible for Morning Blitz. |

Use these category names to match the existing filter buttons: `Executive`, `Academic`, `Nuanced Synonyms`, `Phrasal Verbs`, `Executive Phrases`, and `Personal collection`. New categories require adding corresponding filter buttons in the application code.

For a complete recall card, provide IPA, a study band, a category, context, and a nuance note as well as the required fields. Keep IDs unchanged when correcting an entry so its learning history remains attached. Do not reuse an old ID for a different word.

Reload after editing the HTML. Newly eligible words enter future daily queues; the current day's saved queue is not regenerated automatically. With fewer than 100 eligible entries, the daily target is the number available.

## Edit grammar lessons

Find `const RULES = [...]` after the vocabulary block. Each rule contains an `id`, `title`, `tag`, `explain`, `bad`, `good`, `question`, `options`, `answer`, and `why`. The `answer` field is the zero-based index of the correct option. Use stable, unique rule IDs for saved quiz results.

## Saved progress

The app stores learning status, bookmarks, daily queues and reviews, active dates, theme, and quiz answers in `localStorage` under the key `lexicon-c2-v1`.

Progress belongs to the browser profile and origin; it is not written into `index.html` and does not sync between devices. Copying the HTML copies the vocabulary, not your progress. Browser storage for directly opened `file:` URLs can vary, so a consistent local server address is preferable for regular use.

Clearing browser storage or removing that key resets progress. Before doing so, you can manually copy the key's value from your browser's developer tools as a backup. There is no built-in import/export interface for progress. If storage is blocked or full, the app displays a warning and changes remain in memory for that session.

## Technical structure

- `index.html`: all vocabulary, HTML, custom CSS, and vanilla JavaScript.
- Tailwind CSS: loaded from `https://cdn.tailwindcss.com`.
- Web Speech API: pronunciation, preferring an available British English voice.
- Pagination: 12 cards per grid page and 100 rows per vault page to avoid rendering the entire collection at once.
- Search: precomputed normalized text with a short input debounce.

The app has no application backend or cloud synchronization. Tailwind is an external dependency, and speech processing may be local or remote depending on the selected browser voice.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| Progress seems missing | Use the same browser profile and exact address as before; check whether browser storage was cleared or blocked. |
| No pronunciation | Check audio output and available English voices. Speech synthesis support varies by browser and operating system. |
| A word is absent from Morning Blitz | Verify `curated: true`; only up to 100 eligible entries are selected, and today's queue stays fixed. |
| An entry is absent from Phrases & Idioms | Use the exact category `Phrasal Verbs` or `Executive Phrases`. |
| Search returns nothing | Clear category/status filters and try a shorter term. |
| The app stops rendering after a data edit | Check commas, quotes, and brackets in the browser console or restore the previous HTML file. |

The daily selection is a simple priority-based review queue, not an adaptive spaced-repetition algorithm. Mastery is self-reported rather than inferred from recall accuracy.
