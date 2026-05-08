# STYLE.md
# belt.works development standards

## Accessibility

All public-facing belt.works projects must meet WCAG 2.2 AA.

In practice that means:
- Semantic HTML — use the right element for the job
- Color contrast minimum 4.5:1 on body text, 3:1 on large text
- Every interactive element is keyboard navigable
- Visible focus states — never suppress outline without replacing it
- Alt text on every image that carries meaning
- Form inputs have explicit labels (not just placeholders)
- No autoplay audio or video
- ARIA only where native HTML semantics aren't enough — don't ARIA your way
 out of bad markup

Test with: axe DevTools, keyboard-only navigation, VoiceOver spot check
before shipping any public UI.

## Code comments

Comment the why, not the what.

Bad:
// increment i
i++

Good:
// Stalwart returns 1-indexed UIDs; subtract 1 to align with our 0-indexed
// message store before writing to the DB
const adjustedUid = uid - 1

Rules:
- If you had to think about it for more than 30 seconds, leave a comment
- If you're working around a bug, a library quirk, or a non-obvious constraint,
 leave a comment with enough context that future-you can understand it cold
- If you're doing something that looks wrong but is intentional, say so
- Don't comment obvious things — trust the reader

## Verbose inline comments

Dense or tricky sections get extra treatment:
- Multi-step logic gets a comment at the top explaining the overall approach
 before the implementation
- Regex patterns get a comment explaining what they match and why
- Any section where the order of operations matters gets a comment saying so
- Async flows with non-obvious timing get a sequence note

If you're reading a section and thinking "I'd have to trace through this to
understand it" — that section needs a comment.

## Error messages and 404s

Errors should sound like a person wrote them, not a framework.

Rules:
- Never expose stack traces to end users
- Never say "An error occurred" — say what actually happened
- 404s get a voice-consistent message. Dry, a little funny, never cute.
 Examples of the right energy:
 "This page doesn't exist. Maybe it never did."
 "Nothing here. Check the URL or go back."
 "404. You found the void."
 Wrong energy:
 "Oops! Looks like this page went on vacation! 🏖️"
- Error messages in the UI should tell the user what to do next, not just
 what went wrong
- System errors that users can't fix should say so: "Something went wrong on
 our end. Try again in a minute."

## Test coverage

We test critical paths. Everything else is optional.

Critical path = if it breaks and you don't notice, real damage happens.

Always test:
- Anything that touches money (Stripe, payment confirmation, pricing logic)
- Anything that writes to the database (form submissions, lead capture,
 user data mutations)
- Anything that sends external messages (SMS, email, webhooks)
- Auth flows
- Anything that would silently corrupt or lose data if it broke

Skip tests for:
- UI rendering (eyeball it)
- Static content
- One-off scripts
- Anything you can verify manually in 30 seconds

The bar: if it breaks silently and you find out later from a user,
it needed a test. If it breaks loudly and you'd know immediately, it can wait.

No coverage percentage targets. Ship tests that catch real problems,
not tests that hit a number.
