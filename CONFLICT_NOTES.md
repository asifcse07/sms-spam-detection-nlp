# Merge Conflict Notes

## What happened

The same line of `README.md` (line 3, the project status line) was edited
differently on two branches after they diverged:

| Branch | Commit message | New line 3 |
|--------|----------------|-----------|
| `proposal` | Update README status to reference proposal.md | `Project status: Proposal written — see proposal.md` |
| `main` | Update README status to data collection | `Project status: Data collection in progress` |

Both edits replaced the original line `Project status: Proposal stage`, so Git
could not decide which version to keep. GitHub marked Pull Request #1
(`proposal` → `main`) as having conflicts.

## How it showed up

Running `git merge main` while on the `proposal` branch produced:

```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

and `README.md` contained the conflict markers:

```
<<<<<<< HEAD
Project status: Proposal written — see proposal.md
=======
Project status: Data collection in progress
>>>>>>> main
```

## How I resolved it (manually)

1. Opened `README.md` in an editor.
2. Decided both statuses were true and worth keeping: the proposal is written
   **and** data collection has started.
3. Deleted the `<<<<<<<`, `=======`, `>>>>>>>` markers and replaced both lines
   with a single combined line:

   ```
   Project status: Proposal written (see proposal.md) — data collection in progress
   ```

4. Checked no conflict markers remained: `grep -n '<<<<<<<\|>>>>>>>' README.md`
   (no output).
5. Staged and committed the resolution together with this file:

   ```bash
   git add README.md CONFLICT_NOTES.md
   git commit   # merge commit: "Merge main into proposal: resolve README status conflict"
   git push origin proposal
   ```

6. The Pull Request became mergeable again and was merged into `main`.

## Lesson learned

Conflicts happen when two branches change the same lines. Pulling `main` into a
feature branch regularly (and keeping shared files like the README edited in
one place) keeps conflicts small and easy to resolve.
