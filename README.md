# AI Hub continuation survey (Elisa DII, October 2026)

One static page, `index.html`, built from the August survey (`../dii-ai-survey/`, commit
2628252). Content from the assistant repo, `drafts/night-2026-10-02/01c-elisa-survey.md`,
as settled with Sami on 7.10.2026. Answers by Fri 16.10.

Answers go to Firestore project `book-club-e4916`, collection `dii_survey_2026_10`
(August used `dii_survey_2026`, which this page does not touch).

## What one answer looks like

```json
{
  "name": "...",
  "group": "Oskar (EID)",
  "moment": "...",
  "slows": "...",
  "triads": {
    "decided": [40, 30, 30],
    "help":    [20, 60, 20]
  },
  "slot": "yes, half a day every other week",
  "ts": "2026-10-08T09:00:00.000Z"
}
```

Triangle weights sum to about 100 and follow the corner order:

- `decided`: our experience with AI, the problem itself, the conditions around us (time, tools, access)
- `help`: learning (someone showing me how), doing (time to try it on real work), sharing (seeing what others did)

## Firestore rule (published 7.10.2026)

Firebase Console, project `book-club-e4916`, Firestore Database, Rules. Paste this block
inside `match /databases/{database}/documents { ... }`, next to the August
`dii_survey_2026` block, and press Publish. Do not replace the rest of the rules document.

```
    // AI Hub continuation survey, October 2026: anyone may add an answer,
    // nobody may read, change or delete one from the browser.
    match /dii_survey_2026_10/{doc} {
      allow create: if request.resource.data.keys().hasOnly(
                         ['name', 'group', 'moment', 'slows', 'triads', 'slot', 'ts'])
                    && request.resource.data.name is string
                    && request.resource.data.name.size() < 200
                    && request.resource.data.moment.size() < 2000
                    && request.resource.data.slows.size() < 2000;
      allow read, update, delete: if false;
    }
```

Answers are read the same way as August's, with admin access outside the browser.

## How to publish

Like August: its own public GitHub repo with Pages on `main`, served by the custom domain
of skallinen.github.io at `https://1-bit-wonder.net/dii-ai-hub-survey/`.

```sh
cd ~/common/projects/dii-ai-hub-survey
gh repo create skallinen/dii-ai-hub-survey --public --source . --push
gh api -X POST repos/skallinen/dii-ai-hub-survey/pages -f 'source[branch]=main' -f 'source[path]=/'
```

Live since 7.10.2026: Pages on, rule published through the Firebase Rules API (the rest of
the rules document unchanged), one test answer sent through the live page, found in
Firestore and deleted. A new rule can take a minute or two before writes pass.
