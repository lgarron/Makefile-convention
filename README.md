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
| `clean` | Clean build artifacts so that the next build happens "from scratch". | Remove builds, caches, dependency directories, etc. May undo some or all of `make setup` depending on the nature of the project. Usually does **not** remove `./node_modules` in JS/TS projects (can be expensive to re-download on a slow connection) or `./target` in Rust projects (can be kept without causing correctness issues). This should preserve any uncommitted changes to the source code (i.e. the output of `git status` should not be affected). | |
| `reset` | Reset as much of the project to a clean checkout as possible. | Remove all project-scoped dependencies (e.g. `node_modules`), tooling, credentials, etc. This should preserve any uncommitted changes to the source code (i.e. the output of `git status` should not be affected). | `clean` |
| `deploy` | Deploy or upload a website project. | | `clean`, `build` |
| `publish` | Publish a library on the relevant package manager. | | `build`, `test` |
| `install` | Install the project locally. | | `build` |
