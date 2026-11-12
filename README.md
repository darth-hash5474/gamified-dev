# Gamified Dev Planner

Type in a project idea and your tech stack. Get back an ordered, bite-sized task list — a locked
skill-tree path, not just a flat checklist — so you always know exactly what the next concrete
step is, without the overwhelm of seeing the whole project at once.

## The problem this solves

Going from "I have an idea" to "here's the first file I'm writing" is the part that stalls a lot
of projects before they start. The usual fix is asking an AI to write a to-do list and then just
working off it by hand. This builds that step into the tool itself, and adds the structure that
actually helps: you can't skip ahead to task 4 before finishing task 3. The choice to context-switch
or jump around is removed, not just discouraged. Immediate feedback (XP, visible progress, a payoff
at the end) and a visual path you can see at a glance are part of the same idea — external
structure and clear feedback loops, not willpower, are what keep a project moving.

## How it works (v1 scope)

1. Enter a project idea + tech stack.
2. An AI call breaks it into an ordered list of concrete tasks, each with a short description and
   clear "done" criteria.
3. Tasks render as a locked path: the current task is active, everything after it is locked and
   greyed out, completed tasks are checked off.
4. Completing a task awards XP and unlocks the next one. You cannot jump ahead.
5. Completing the final task unlocks free navigation back through earlier tasks (to redo or
   review them) — and triggers a small celebration (a kitten gif, because why not).
6. A toggle switches the whole gamified layer off in favor of a plain checklist, for anyone who
   just wants the task breakdown with none of the game dressing.

**Explicitly cut from v1:** sharing a project/task list with other accounts. Single-user only for
now — adding real multi-user sharing (invites, permissions) is a meaningfully bigger feature and
would double the auth surface for not much payoff at this stage.

## The five pillars this is built to demonstrate

- **Persistence:** projects, their generated task lists, completion/lock state, and XP, stored in
  a real database — not just rendered and discarded.
- **Auth:** single-user accounts so a project is yours and survives across sessions. Passwords
  hashed, never stored plain.
- **API:** a clean contract for generating a plan, fetching a project's current state, and marking
  a task complete — consistent response shapes, real validation, real status codes.
- **Testing:** the lock/unlock progression logic is pure, deterministic business logic with zero
  AI involved, and is the most thoroughly tested part of the app for exactly that reason.
- **Deployment:** a real deploy (Fly.io), with the database persisting correctly across redeploys
  (a known gotcha with SQLite specifically) and secrets kept out of the repo.

## Tech stack

- Backend: Python / FastAPI
- Database: SQLite (same pattern as DeskFlyAI's account store)
- AI: Gemini (`google-genai`), same setup as Reverse ATS
- Frontend: plain HTML/CSS/JS — no framework needed for a single locked-path view
- Tests: pytest

## Status

In progress — being hand-built (not AI-generated end to end) specifically so every piece of it can
be explained and defended in a technical interview. See `TODO.md` for the build order.
