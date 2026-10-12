# German study project (Almanca)

Berkay is learning German (Goethe-Institut course, levels A1 to B1; Turkish is the first language). This folder holds their study materials. Claude's job here is to explain German sentences they paste in, and to keep their notes and flashcards up to date.

## Files

| File | What it is |
|---|---|
| `german-notes.html` | Personal notes. Three tabs: **Wortschatz** (vocabulary and phrases), **Grammatik** (grammar rules with tables), **Präpositionen** (detailed preposition guide). |
| `flashcards.html` | Single-file study app (no server). Flashcards by level, `Artikel üben` (der/die/das quiz), `Präpositionen üben` (multiple-choice exercises). |
| `quizlet-set-*.txt` | Raw Quizlet exports (tab-separated: term, definition). Source data, already merged into `flashcards.html`. Set 6 was empty at last check. |

`flashcards.html` is also published as a private claude.ai Artifact so it works on the phone:
https://claude.ai/artifact/N2gR2EsgWJiUgrj7X4G6mD
Republish to that same URL after every change (`Artifact` tool with the same `file_path`).

## How we work

1. **The user pastes a German sentence or a screenshot** (usually from the Goethe course) and asks what it means.
2. **Explain it** clearly: translation, part-by-part breakdown, why a word or case is used. Answer exactly what was asked and compare with English or Turkish only when it helps.
3. **End with a short numbered list of suggestions** for what could be noted (words, phrases, grammar points). Do not save anything yet.
4. **The user picks numbers** (or says "all"). Only add what they picked. Never add items on your own.
5. **For each picked item**, do all of these:
   - add a card to `german-notes.html` (Wortschatz for words and phrases, Grammatik for rules, with a table when it helps)
   - add a flashcard to `flashcards.html` in the right level and topic
   - add an entry to the `EXAMPLES` map for that card (see below)
   - validate (see below), then republish the Artifact
6. **Be specific when asked.** If the user says "make sure you mention X", state X explicitly in the notes (for example what *nur empfehlen* really means).
7. **Correct mistakes.** When the user writes German, correct it kindly and show the corrected version. When Quizlet data has wrong spelling or translations, fix them.
8. **Be honest when the exercise key is questionable.** The user thinks critically about the Goethe exercises and is sometimes right. Say so.

## Notes file conventions (`german-notes.html`)

- Plain, readable HTML (the user does not want Markdown notes). Cards use `class="card"`, with `h2` (the item), `.meaning`, optional `p` / `table` / `.rule`, and `.example` (German sentence plus English translation in `<i>`).
- Keep the user's own sentences as the examples whenever the item came from one of their texts.
- Bold the target word in the example sentence.
- New tab = new `<section class="page" id="...">` plus a button with `data-page="..."` in the `.tabs` div.

## Flashcard conventions (`flashcards.html`)

- Data lives in `SETS`: levels (`A1.1`, `A1.2`, `A2.1`, `A2.2`, `B1.1`, `B1.2`, `Unregelmäßige Verben`), each with `topics`, each with `cards`.
- A card is `[German, English, optionalNote]`. Nouns carry the article and plural: `"der Berg, die Berge"`. The `Artikel üben` quiz is built from these automatically.
- **Every card needs an entry in `EXAMPLES`**, keyed by the exact German side of the card: `[German example, English translation]`.
  - Words that came from the user's own texts (Freddy live chat, Firmenausflug email, the Lukas dialogue, Tom, Herr Augustin) use the **whole passage** (2 to 3 sentences), not just one sentence. The reusable passages are in `PASSAGES` / `FROM_TEXT`.
  - Words from Quizlet get a short example that Claude writes.
  - Cards that test endings (pronouns, `dieser`) use blanks in the example so it does not give the answer away.
- The **Beispiel zeigen** button shows the example before flipping, in the language of the front side.
- `PREP` holds the preposition exercises: `[category, "sentence with ___", [options], answer, why, English]`.
- Duplicate keys are fine only for irregular verbs that share an example with their A1.1 card.

## Validate after every change

From the project folder:

```bash
node -e "
const h=require('fs').readFileSync('flashcards.html','utf8');
const s=h.split('<script>')[1].split('</script>')[0];
new Function(s);
const r=new Function(s.split('const \$ =')[0]+';return {SETS,EXAMPLES}')();
const keys=new Set();let n=0;
for(const x of r.SETS)for(const t of x.topics)for(const c of t.cards){n++;keys.add(c[0]);if(!r.EXAMPLES[c[0]])console.log('MISSING',c[0])}
for(const k in r.EXAMPLES)if(!keys.has(k))console.log('UNUSED',k);
console.log('parses OK, cards',n);
"
```

It must print `parses OK` and no `MISSING` or `UNUSED` lines. Check that `german-notes.html` has as many `</div>` as `<div` after edits.

## Git

- Remote: https://github.com/BerkayElcik/deutschlernen.git (branch `main`). Commit identity is set locally for this repo only.
- **Push routinely without asking, but not after every small update.** Commit and push a batch of related changes (for example after several picked items are added, or at the end of a study session). A single new card on its own does not need its own push.
- Commit message: short summary line, bullet list of what changed, then the `Co-Authored-By` line from the harness.
- Never force-push.

## Things to remember

- The artifact page must stay theme-aware (light and dark tokens on `:root`), work at phone width, and use no `<html>` / `<body>` wrapper tags (the publish step adds them).
- Examples of preferences the user has confirmed: notes should be easy to read; ask before noting; explain unknown words in detail when asked; keep notes and flashcards in sync.
- Still open: the Goethe-Institut vocabulary PDF has not been added yet; `quizlet-set-6.txt` was empty; the user has not said whether `anschießen` was meant to be `anschließen`.
