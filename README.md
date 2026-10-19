# Weekly meal plan agent

A household meal planner that runs itself. Every Friday before anyone is awake, a scheduled AI agent reads what is already in the fridge and what the family ate the last few weeks, plans Friday dinner through Thursday lunch, writes a phone-friendly PDF with every recipe, its leftovers window, and its macros, puts every ingredient in the grocery cart, and leaves the checkout for a human. On Thursday evening a second, smaller agent asks what needs using up and writes the answer to a file the Friday run reads first.

No cloud storage. The state is three text files in one folder on the family computer, and the agent reaches them through the desktop app's file bridge. If the computer is off, nothing runs, which is the right failure mode for a grocery order.

This repository is the de-personalized version: the runbooks, the file formats, and the design, with the household's dietary specifics, favorite recipes, store account, and children's names taken out. Fill in the bracketed sections with your own.

## Files

| File | What it is |
|---|---|
| `agents/weekly-food-plan.md` | The Friday runbook: read state, plan, write the PDF, fill the cart, save, report |
| `agents/thursday-fridge-check.md` | The Thursday runbook: push a question, wait for the answer, write `fridge-leftovers.txt` |
| `formats/favorite-recipes.txt` | How the recipe file is laid out so the agent can parse it and credit sources |
| `formats/plan-history.txt` | How past weeks are logged so dinners do not repeat within three or four weeks |
| `formats/fridge-leftovers.txt` | The Thursday output, which the Friday plan uses first |

## Design notes

**Three files are the whole database.** `favorite-recipes.txt` is the family's loved recipes, already adapted to the house rules, with a source line the PDF credits. `plan-history.txt` is appended every week. `fridge-leftovers.txt` is overwritten every Thursday. The agent reads all three before planning and writes two of them after.

**The agent never checks out.** It adds to the cart, reads the cart back to confirm everything landed, and stops. A person picks the pickup time and pays. The runbook repeats this rule three times because it is the one that matters.

**Degrade gracefully.** If the browser, the store, or the computer is unreachable, the plan and the PDF are still produced and the message says exactly what did not happen and what the person needs to do.

**Report the judgment calls.** Nobody is awake to ask, so every substitution, out-of-stock swap, and "I moved this leftover because it would have been eaten on day five" goes in the summary.

**Safety is in the PDF.** Every recipe states how long its leftovers keep in the fridge and freezer, from USDA and FoodSafety.gov guidance, and the plan is checked against those windows before it is finalized.

## Running it

Paste `agents/weekly-food-plan.md` into your scheduled-agent system with a Friday early-morning schedule and give it a browser, a shell, and access to the folder. Do the same for the Thursday check with a push-notification channel. Put a starter `favorite-recipes.txt` in the folder in the format shown. The first run creates the other two files.

## License

CC BY 4.0.
