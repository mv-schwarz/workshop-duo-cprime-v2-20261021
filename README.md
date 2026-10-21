# workshop-duo

A two-seat OpenRig team that works in any repository, and asks you before
anything risky. It is a smaller version of OpenRig's
[Workshop](https://github.com/mvschwarz/openrig-world/tree/fa38c9b55e013e600a3472d2f7561df32df79ce8/rigs/workshop)
team: a lead and a builder, without Workshop's separate reviewer and QA seats.

| Seat | Runtime | Does |
|---|---|---|
| `orch-lead@workshop-duo` | Codex | Starts from your goal, scopes it, hands it to the builder, checks the result and owns the PR description. Talk to this seat. |
| `dev-build@workshop-duo` | Claude Code | Makes the change on its own branch and worktree, and runs the repository's build and tests. |

These are the recommended runtimes. `configurations.yaml` also offers
`all-claude` and `all-codex`. Every choice keeps the same seat names and rig
name.

`CULTURE.md` holds the team's values, including "Ask before anything risky".
Each seat's role is in `agents/workshop/guidance/`.

## Based on Workshop

workshop-duo is a derivative of Workshop by Mike Schwarz, from
[mvschwarz/openrig-world](https://github.com/mvschwarz/openrig-world), folder
`rigs/workshop`, at commit `fa38c9b55e013e600a3472d2f7561df32df79ce8`,
licensed under the Apache License 2.0. What changed:

- **Two seats instead of four.** The code reviewer and QA seats are removed;
  the lead checks the builder's commit itself.
- **Ask first instead of no prompts.** Workshop launches every seat with
  `permission_policy: builtin:yolo` and `non_interruptive: true`. This team
  drops both, so OpenRig's team launch default applies (see "Permissions"),
  and its culture and roles tell both seats to ask you before risky actions.
- **No Pi configuration,** and the skills only Pi used are removed.
- Guidance specific to working on OpenRig's own repository is removed.

Every file changed from Workshop says so in a note at its top. Files under
`agents/workshop/skills/` and `agents/workshop/bundle-skills/` are copied
unmodified. `NOTICE` lists the credits and licenses.

## Before you start

- OpenRig 0.6.6 or later (`rig --version`), Node 22 or 24, and tmux.
- Claude Code and Codex installed and logged in for the recommended mix; a
  single-provider configuration needs only that provider.
- A clone of the repository you want to work on, set up the way its README or
  CONTRIBUTING says.

Keep OpenRig's files out of your commits. From the root of your clone:

```sh
printf '%s\n' CLAUDE.local.md AGENTS.md /.openrig/ .claude/settings.local.json >> .git/info/exclude
```

If you skip this, the lead adds any missing entries when it starts.

**What it writes.** Installing copies this bundle into the `--target` folder.
In your clone, OpenRig writes its instruction blocks into `CLAUDE.local.md`
(Claude Code seats) or `AGENTS.md` (Codex seats), activity hooks into
`.claude/settings.local.json`, helper files under `/.openrig/`, and the seats'
skills under `.claude/skills/` and `.agents/skills/`; the builder makes git
worktrees under `.worktrees/`. Starting OpenRig also records Claude Code
workspace trust in `~/.claude.json`, puts discovery skills in `~/.claude/skills`
and `~/.agents/skills`, and, for Codex seats, writes hooks and settings to
`~/.codex/config.toml`. The lead writes the team's roster under OpenRig's
workspace (`rig config get workspace.root`).

**If you ask Claude Code to install this for you,** it may decline the
`rig up` line because it starts new agents. Run it yourself in a terminal,
from the root of your clone.

## Run it

From the root of your project's clone, preview, then install and launch:

```sh
rig bundle install https://github.com/mv-schwarz/workshop-duo-cprime-v2-20261021/tree/50f8a2018121623d06111fb221e43afa06f981fc --preset recommended --target ~/rigs/workshop-duo --cwd . --plan
rig bundle install https://github.com/mv-schwarz/workshop-duo-cprime-v2-20261021/tree/50f8a2018121623d06111fb221e43afa06f981fc --preset recommended --target ~/rigs/workshop-duo --cwd .
```

For one provider, use `--preset all-claude` or `--preset all-codex` with the
same target. `--cwd .` selects your project.

The link is pinned to the first published commit; a commit can't name itself,
so later commits that only change this README keep it. For the version
reviewed for openrig.dev/rigs, use the commit its listing shows.

Talk to the lead: `rig capture orch-lead@workshop-duo` reads its screen and
`rig send orch-lead@workshop-duo "<message>"` writes to it, or attach with
`tmux attach -t orch-lead@workshop-duo`, or run `rig` for OpenRig's TUI.

Stop the team with `rig down workshop-duo`. The builder's git worktrees under
`.worktrees/` stay; remove them with `git worktree remove` when you are done.

## Permissions

Both seats keep their permission prompts. `rig.yaml` sets no
`permission_policy`, so OpenRig gives each seat its team launch default:

- **Claude Code seats** launch in `acceptEdits`: file edits go ahead, ordinary
  `rig` commands, project reads and common test commands are allowed, and
  OpenRig's lifecycle commands (`rig up`, `rig down`, seat stop and handover)
  ask. Other commands, including `git push` and `gh`, follow your Claude Code
  rules and prompts.
- **Codex seats** launch with `-s workspace-write` and Codex's approval policy:
  they can write in the project and OpenRig's workspace, and need your approval
  to go outside the sandbox. The sandbox has no network.

Harness warning screens are not accepted for you (`non_interruptive` is off),
so a seat may wait on one at its first launch: answer it in that seat's
terminal.

**Expect to answer each prompt.** With prompts on, a seat stops at every
command its harness asks about and waits until someone answers in that seat's
terminal, even for routine steps such as reading git status, creating its
worktree or committing. A small all-Claude trial on OpenRig 0.6.14 stopped at
eight prompts. Watch both seats (`rig ps --nodes --rig workshop-duo` and
`rig capture <seat>@workshop-duo`), or attach to them; a seat waiting on a
prompt does no work, and OpenRig's stall check may flag its task meanwhile.
Choosing "don't ask again" or auto mode in a seat removes the prompt for that
seat, so decide that deliberately.

On top of the prompts, `CULTURE.md` tells both seats to ask you first before
deleting or overwriting work, reaching outside the repository, pushing or
publishing anything, starting or stopping agents, or anything else hard to
undo. That rule rests on the seats following their instructions; the harness
prompts are the backstop. Neither stops a command you approve.

### Codex seats and the sandbox

Codex's `workspace-write` sandbox blocks network access, including localhost
and so the local OpenRig daemon. In the recommended mix the Codex lead's `rig`
commands then fail, or wait for your approval in its terminal. To give the
lead network access while keeping approvals, opt in before launch:

1. Create a Codex named profile called `workshop-duo-net`, as described under
   "Codex: select sandbox and approvals together" in OpenRig's
   `docs/reference/getting-started.md`, with these settings:

   ```toml
   sandbox_mode = "workspace-write"
   approval_policy = "on-request"

   [sandbox_workspace_write]
   network_access = true
   ```

2. In `rig.yaml`, uncomment `codex_config_profile: workshop-duo-net` on
   `orch.lead` (and add it to `dev.build` if you use `all-codex`).
3. Launch, then check `/status` in each Codex seat before giving it work.

This grants those seats network access in general, not only to the daemon.

## Limits

- **A starter, not a process.** The team prepares one pull request. Merging and
  releases stay with you and the repository's maintainers.
- **No independent reviewer.** The lead checks the builder's work; for changes
  where a second pair of eyes matters, use Workshop's four seats.
- **Every map here is incomplete.** These files and the repository's own maps
  can be stale. The code and its behaviour decide.

## License

Apache-2.0, the same as Workshop. See [LICENSE](LICENSE) and [NOTICE](NOTICE).
`agents/workshop/bundle-skills/ponytail/` is MIT-licensed, with its license
beside it.
