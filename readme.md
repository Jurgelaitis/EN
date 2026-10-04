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
- **Grammar & Nuance:** 26 lessons with short Lithuanian explanations, construction patterns, comparisons, multiple-choice checks, and speaking/writing prompts with revealable model answers. Search and topic filters help find rules; a five-topic practice session prioritizes lessons not yet answered correctly.
- **Phrases & Idioms:** contextual cards for phrasal verbs, executive expressions, idioms, discourse markers, and selected academic expressions, with register filters.
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

The app contains **8,529 entries**, including **180 curated study cards**, after merging overlapping terms from the curated set and the supplied files:

- `Išsaugoti vertimai.xlsx`
- `EN words.docx`

Unedited imported entries remain under **Personal collection** and are labeled **Unrated** and **Nepatikrinta**. Edited entries carry **Redaguota**, with original wording preserved in a details disclosure where changed. **Reikia konteksto** identifies ambiguous fragments whose intended meaning cannot be established safely without the original sentence; these cannot be marked as learned and are excluded from Morning Blitz. Most imported entries still lack edited examples or IPA. CEFR labels are indicative study bands; B2 items are included where correcting a common usage error is valuable, and some expressions remain Unrated.

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
  "curated": true,
  "reviewStatus": "edited",
  "register": "Formal"
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
| `curated` | Set to `true` to make the entry eligible for Morning Blitz; entries marked needs-context are excluded. |
| `reviewStatus` | `edited`, `unreviewed`, or `needs-context`; this is separate from mastery. |
| `original` | Previous `word` and `meaning`, retained when correcting a source entry. |
| `register` | `Neutral`, `Formal`, `Informal`, or `Business informal`, when supplied. |
| `references` | Optional array of `{ title, url }` reference links. |

Use these category names to match the existing filter buttons: `Executive`, `Academic`, `Nuanced Synonyms`, `Phrasal Verbs`, `Executive Phrases`, `Idioms`, `Discourse Markers`, and `Personal collection`. New categories require adding corresponding filter buttons in the application code.

For a complete recall card, provide IPA, a study band, a category, context, and a nuance note as well as the required fields. Keep IDs unchanged when correcting an entry so its learning history remains attached. Do not reuse an old ID for a different word.

Reload after editing the HTML. Newly eligible words enter future daily queues; the current day's saved queue is not regenerated automatically. With fewer than 100 eligible entries, the daily target is the number available.

## Edit grammar lessons

Find `const RULES = [...]` after the vocabulary block. Each rule contains an `id`, `title`, `tag`, `topic`, `quick` construction reminder, Lithuanian `lt` explanation, optional English `explain`, `bad`, `good`, `question`, `options`, `answer`, and `why`. Productive practice uses `task` and `model`; optional `references` contain a title and HTTPS URL. The `answer` field is the zero-based index of the correct option. Use stable, unique rule IDs for saved quiz results.

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
| An entry is absent from Phrases & Idioms | Use `Phrasal Verbs`, `Executive Phrases`, `Idioms`, or `Discourse Markers`. Selected academic entries are also included through `USER_DATA.review.expandedIds`. |
| Search returns nothing | Clear category/status filters and try a shorter term. |
| The app stops rendering after a data edit | Check commas, quotes, and brackets in the browser console or restore the previous HTML file. |

The daily selection is a simple priority-based review queue, not an adaptive spaced-repetition algorithm. Mastery is self-reported rather than inferred from recall accuracy.

## Language revision — 4 October 2026

This revision is a targeted editorial pass, not a completed semantic audit of every entry. It includes:

- 270 corrected or clarified source entries, with their original wording retained.
- 71 expanded expression cards, including 49 newly added entries and enriched existing entries.
- 17 uncertain fragments explicitly held for context rather than assigned a guessed meaning.
- Eight imported grammar headings or mixed term lists moved to `USER_DATA.sourceNotes`, visible in the vault's imported-notes disclosure.
- 26 grammar/usage lessons in total, including 20 new topics.

The 429 entries labeled **Redaguota** include the earlier curated material, revised source entries, and additions. **8,083 entries remain labeled unreviewed**. Editorial review is AI-assisted; selected meanings and rules were cross-checked against linked Cambridge, British Council, and WordReference resources. Lithuanian translations and new examples are editorial, not official translations from those publishers. A reference attached to one entry does not certify the entire dataset.

`language-review.md` records each source correction and the fragments that still need context. Original XLSX and DOCX files were not changed. Existing vocabulary IDs, the storage key, and the original six grammar IDs remain stable to preserve saved progress. Corrections to English spelling can leave separately imported entries with the same displayed term; counts are entry counts, not guaranteed unique headword counts.

The current day's saved review queue remains stable. Newly curated items become eligible for future queues. Practice examples are self-check tasks, not automated free-text grading. This app supports vocabulary and grammar practice; achieving C2 also requires sustained reading, listening, speaking, and writing practice.
