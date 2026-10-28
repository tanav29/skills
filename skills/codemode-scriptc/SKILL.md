---
name: codemode-scriptc
description: Use when general tool calls cant do that work
compatibility: Requires the `scriptc` CLI, Node.js 20+, and a working C/Clang toolchain. The generated program may use the filesystem, subprocesses, or network only when that access is clearly within the user’s request.
license: Apache-2.0
---

# ScriptC Code Mode

Use this skill when a small generated program is the best interface between the user’s request and the local workspace. The point is to give the model a compact, inspectable execution loop: write TypeScript, compile/run it with ScriptC, parse the result, and use that result to finish the task.

## Operating loop

1. Translate the request into a small program with explicit inputs and a machine-readable result. Keep orchestration in the agent and domain logic in TypeScript.
2. Inspect the workspace and identify the narrowest input paths and output paths needed. Never sweep unrelated directories or secrets into the program.
3. Write the program to a temporary, task-scoped `.ts` file. Prefer a workspace-local temporary directory when the result or source should be reviewable; otherwise use the platform temp directory.
4. Before execution, review the generated source for unintended writes, destructive operations, credential access, broad network calls, and shell interpolation. Ask the user if the requested operation is materially ambiguous or destructive.
5. Run it with no program arguments using:

   ```text
   scriptc run <program.ts>
   ```

   ScriptC's `run` command does not forward extra CLI arguments. When arguments are needed, build and invoke the temporary executable instead:

   ```text
   scriptc build <program.ts> -o <temporary-binary>
   <temporary-binary> <program arguments>
   ```

   Use `scriptc coverage <program.ts>` first when the program is non-trivial or when static/native execution matters. Use `--dynamic` only when a dependency or construct requires it and explain that choice briefly.
6. Make the program print one final JSON value on stdout. Send progress and diagnostics to stderr so stdout remains parseable. If JSON is not practical, use a clearly delimited result section.
7. Check the exit code, parse the result, and validate the shape before acting on it. Treat compiler errors, runtime errors, malformed output, and partial results as failures that need repair or a clear handoff.
8. If the task needs a reusable executable, use `scriptc build <program.ts> -o <output>` and verify the produced binary. Otherwise clean up temporary files after the result has been consumed, unless the user asks to keep them.
9. Report what was executed, the meaningful result, any dynamic/coverage caveat, and artifacts created. Do not claim success from a successful compile alone; verify behavior.

## Program conventions

Prefer ordinary TypeScript and supported Node APIs. Keep arguments explicit and guard indexed argv reads because ScriptC arrays are dense. For example:

```ts
type Result = { ok: true; items: unknown[] } | { ok: false; error: string };

function emit(result: Result): void {
  process.stdout.write(JSON.stringify(result) + "\n");
}

try {
  const inputPath = process.argv.length > 2 ? process.argv[2] : "";
  if (!inputPath) throw new Error("missing input path");
  // Do focused work here; send human-readable progress to stderr.
  emit({ ok: true, items: [] });
} catch (error) {
  emit({ ok: false, error: error instanceof Error ? error.message : String(error) });
  process.exitCode = 1;
}
```

For large results, write a task-scoped JSON/CSV artifact and print only its path plus summary metadata. Avoid putting binary data or unbounded logs on stdout.

## Safety boundaries

- Generated code is executable code. Review it before running it, especially if it can write files, invoke processes, contact external services, or handle tokens.
- Scope file access to explicitly named inputs and output directories. Do not read environment variables wholesale; access only a named variable when the user’s task requires it.
- Do not delete, overwrite, move, publish, or send data without clear user authorization. Add a dry-run or preview path for consequential operations.
- Do not install npm packages or use `--dynamic` merely to avoid understanding a compiler error. Prefer a static rewrite; if a package is necessary, disclose the dependency and dynamic-engine tradeoff.
- Keep secrets out of generated source, command-line arguments, logs, and result files.
- If `scriptc` is unavailable, report the missing prerequisite and either ask to install it or use a clearly disclosed fallback only if the user’s request permits one.

## Useful command shapes

```text
scriptc coverage task.ts
scriptc run task.ts
scriptc build task.ts -o task-bin
task-bin input.json
scriptc build task.ts -o task-bin
scriptc build task.ts --dynamic -o task-bin
```

The `--` separates ScriptC options from program arguments. Confirm the exact CLI behavior with `scriptc --help` if a version differs from these examples.

## Completion checklist

- The generated program is narrowly scoped and was inspected before execution.
- The command exited successfully and its output was parsed or otherwise validated.
- The requested files or external effects were verified.
- Temporary artifacts were removed or their paths were reported intentionally.
- The final response distinguishes computed results, created artifacts, and caveats.
