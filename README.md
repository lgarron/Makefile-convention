# Makefile Convention

There are many build systems out there, but `make` has the benefits that 1) it's
installed everywhere, and 2) it provides a good balance of functionality for
small-to-medium projects. Here are common `Makefile` targets that I use so that
I can context switch between my projects without thinking about the build
system.

| Target | Effect | Common Features | Commonly depends on |
|--------|--------|-----------------|---------------------|
| `setup` | Prepare the local repository for development. | Install dependencies, create files/folders, run immediately after `git checkout`. Usually not a dependency of `build`/`dev` (for performance). | |
| `build` | Build the entire project. | Optimized code, build artifacts (e.g. `zip` files). | `clean` |
| `dev` | Build the project in development mode. | Local server, rebuild on source file change, unminified code. |
| `test` | Run the main project tests. | Minimal side effects. | `lint` |
| `lint` | Run the source code through a formatting/style linter. | Do not modify code. Should catch everything that `make format` would fix (and usually more). | |
| `format` | Run the source code through a formatting/style linter. | Automatically fix code when possible. | |
| `check` | Verify all aspects of code health. | Run all lints, tests, and builds (often similar in scope to CI). | `lint`, `test`, `build` |
| `clean` | Clean build artifacts so that the next build happens "from scratch". | Remove builds, caches, dependency directories, etc. May undo some or all of `make setup` depending on the nature of the project. Usually does **not** remove `./node_modules` in JS/TS projects (can be expensive to re-download on a slow connection) or `./target` in Rust projects (can be kept without causing correctness issues). This should preserve any uncommitted changes to the source code (i.e. the output of `git status` should not be affected). | |
| `reset` | Reset as much of the project to a clean checkout as possible. | Remove all project-scoped dependencies (e.g. `node_modules`), tooling, credentials, etc. This should preserve any uncommitted changes to the source code (i.e. the output of `git status` should not be affected). | `clean` |
| `deploy` | Deploy or upload a website project. | | `clean`, `build` |
| `publish` | Publish a library on the relevant package manager. | | `check`, `build` |
| `install` | Install the project locally. | | `build` |

## `.PHONY`

In a lot of projects it can be tricky to keep target dependencies perfectly in sync with source files and configuration files. If these are kept *nearly* but not *perfectly* in sync, this can cause lots of debugging pain. Ideally, something like a [`ninja`](https://ninja-build.org/) generator handles this problem well but such tools can be complicated to install across operating systems/ecosystems, and can be time-consuming to maintain in their own way.

In practice, it is much more effective for some projects to mark most targets as [`.PHONY`](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html). This reduces the purpose of `make` to a pre-installed task runner, but this is still very effective (particularly when using fast tools).

## `setup` in JavaScript projects

When using `bun`:

- Any dependency that invokes `node` or `bun run` on a file should depend on the `setup` target.
- The `setup` target should be `.PHONY` and contain: `bun install --frozen-lockfile`

This ensures that `node_modules` are always fresh in any checkout state (especially after branch switches), bringing the JavaScript ecosystem more inline with others (e.g. Rust using `cargo`, Python using `uv`). When dependencies are already up-to-date, it runs an *extremely* fast no-op check.[^1] In particular, it can avoid a *lot* of pain on projects with multiple collaborators that otherwise arises from inconsistent dependency state.

[^1]: For example, `make setup` in `cubing.js` takes 0.02 seconds to check about 500 dependencies. This is literally an order of magnitude faster than the blink of an eye, in a fairly large project.

(Also note how this is an example of how a fast `.PHONY` command can be significantly more ergonomic than a lot of other options.)
