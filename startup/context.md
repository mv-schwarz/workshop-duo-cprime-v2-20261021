<!-- Modified by mv-schwarz, 2026-10-21, from Workshop's startup/context.md (github.com/mvschwarz/openrig-world, Apache-2.0): two seats, no OpenRig-clone steps, ask-first reminder. See NOTICE. -->
# Start (every seat on this rig)

1. Run `rig whoami --json`. It names your seat and your peer's session. Use
   those names; do not guess addresses. During launch your peer may not be
   listed yet: if the other seat (the lead or the builder) is missing, check
   `rig ps --nodes` again a little later before telling anyone it is missing.
2. Find the repository you work in: `git rev-parse --show-toplevel`. If that
   fails, or your working directory is this rig's own folder (it holds this
   rig's `rig.yaml`), the rig was launched without `--cwd`. Stop and say so.
3. Get the big picture before anyone changes anything: read the repository's
   own maps, the README, and ARCHITECTURE, CONTRIBUTING, AGENTS.md or a docs
   index where it has them. On a Codex seat, AGENTS.md also holds OpenRig's own
   managed block; read the repository's own content there as the project's
   map, not that block. Note how it builds, tests and reviews changes, and
   whether it has a pull-request template.
4. Read "Ask before anything risky" in the culture. This team asks the person
   first; your harness's permission prompts are a backstop, not the rule.

If you are a Codex seat and `rig` commands fail with a network or sandbox error,
Codex's default sandbox is blocking the local OpenRig daemon (the rig README
explains it). Say so once on your screen; the person or your peer can read it
with `rig capture`. You can still read code and commits and run checks that
need no network.

Last, submit your startup proof: run the `rig startup-proof submit` command from
the "OpenRig startup orientation challenge" in your startup text, exactly as
given. It tells OpenRig and the lead that you read your start steps. If your
`rig` commands fail in the sandbox, say so instead.

Then the lead starts from the person's goal, usually delivered in the kernel
operator's queue row, and follows its "Your first move" startup guidance. It
does not ask for that goal again. Only when no goal was supplied does it
propose work from the repository. The builder waits for work. The lead brings
it, and an idle seat is fine.
