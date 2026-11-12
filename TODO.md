# TODO — Gamified Dev Planner

Tasks are ordered on purpose — same rule the app itself will enforce on its users. Don't skip
ahead; each step assumes the ones before it are done and working, not just written.

## 1. Project skeleton
- [X] FastAPI app with a single `GET /health` endpoint returning `{"status": "ok"}`
- [X] `requirements.txt`, `.gitignore`, `tests/` folder
- [X] One passing test for `/health`
- **Done when:** `uvicorn app.main:app` runs locally and the health check test passes.

## 2. Data model & persistence
- [ ] SQLite schema: `projects` (id, idea, tech_stack, created_at) and `tasks` (id, project_id,
  order_index, title, description, status: locked/current/done)
- [ ] A `Store` class to create a project, insert its tasks, fetch a project with its tasks, and
  update one task's status
- [ ] Test: create a project with 3 tasks, mark one done, re-fetch, confirm it persisted
- **Done when:** project + task state survives a process restart (re-run the test after
  restarting the SQLite connection, not just in the same in-memory run).

## 3. AI task-breakdown endpoint
- [ ] `POST /api/projects` — body: `{idea, tech_stack}` — calls Gemini, gets back an ordered list
  of tasks (title + description + done-criteria per task), stores them as `locked` except the
  first (`current`)
- [ ] Structured output schema for the AI response, same pattern as Reverse ATS's `schema.py`
- [ ] Test: mock the Gemini call, confirm the returned tasks get persisted in order
- **Done when:** a real call with a real idea produces a sane-looking ordered task list (sanity
  check this one by hand, not just with the mocked test).

## 4. Progression / locking logic (no AI — pure domain logic)
- [ ] Given a project's tasks, enforce: only the task immediately after the last completed one
  can be marked done; every later task stays locked
- [ ] After the final task is marked done, all tasks become freely revisitable (can redo any of
  them)
- [ ] Test thoroughly: can't complete task 3 before task 2; completing task 2 unlocks task 3, not
  task 4; completing the last task unlocks free navigation backward
- **Done when:** this logic has zero dependency on the AI call and is the most heavily tested
  part of the app — it's the part you should be able to explain line by line in an interview.

## 5. Auth
- [ ] Single-user accounts: signup/login, passwords hashed (never stored plain)
- [ ] Session or token tied to a logged-in user; all `/api/projects*` routes require it
- [ ] Test: hitting a project route without a valid session returns 401, not a crash
- **Done when:** two different accounts can't see or modify each other's projects.

## 6. API polish
- [ ] Consistent JSON error shape across every endpoint
- [ ] Input validation (empty idea, missing tech stack) returns 400 with a clear message, never
  a 500
- [ ] Test: bad input, missing auth, and a project id that doesn't exist all return the right
  status code with a useful message
- **Done when:** every failure mode you can think of returns something a frontend could actually
  show a user, not a stack trace.

## 7. Frontend — the locked path
- [ ] A form: idea + tech stack in, submits to the AI endpoint
- [ ] Render the task list as a vertical path: locked tasks greyed out, the current task
  highlighted and clickable to mark done, completed tasks checked off
- [ ] XP counter that increments when a task completes
- **Done when:** clicking through a generated plan in the browser visibly locks/unlocks tasks in
  the right order, matching what the backend enforces (try to cheat it from the browser console —
  it should still be blocked server-side).

## 8. Gamification toggle + final payoff
- [ ] A toggle that switches between the gamified path view and a plain checklist (same
  underlying data either way)
- [ ] On completing the final task: unlock free navigation + show the kitten gif
- **Done when:** toggling off gamification changes only the UI, never the underlying task/lock
  state.

## 9. Deployment
- [ ] Fly.io config, same pattern as DeskFlyAI
- [ ] `GEMINI_API_KEY` and any session secret set as environment variables, never committed
- [ ] Confirm the SQLite file actually persists across a redeploy (this is the gotcha — a naive
  Fly.io setup can wipe an on-disk SQLite file on every deploy; needs a persistent volume)
- **Done when:** you can redeploy and your existing projects/tasks are still there afterward.

## 10. Wrap-up
- [ ] Full test suite green
- [ ] README screenshot/demo of a real generated project run end to end
- [ ] One real project of your own run through it, start to finish, as the demo case study
