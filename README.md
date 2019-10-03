# Makefile Convention

There are many build systems out there, but `make` has the benefits that 1) it's
installed everywhere, and 2) it provides a good balance of functionality for
small-to-medium projects. Here are common `Makefile` targets that I use so that
I can context switch between my projects without thinking about the build
system.

| Target | Effect | Common Features |
|--------|--------|-----------------|
| `all` | Usually an alias for `build`. | |
| `setup` | Prepare the local repository for development. | Install dependencies, create files/folders, run immediately after `git checkout`. Usually not a dependency of `build`/`dev` (for performance). |
| `build` | Build the entire project. | Optimized code, build artifacts (e.g. `zip` files). |
| `dev` | Build the project in development mode. | Local server, rebuild on source file change, unminified code. |
| `test` | Run the main project tests. | Minimal side effects. |
| `lint` | Run the source code through a formatting/style linter. | Automatically fix code when possible. |
| `clean` | Reset the project for development so that the next build happens "from scratch". | Remove build artifacts, dependency directories, etc. May undo some or all of `make setup` depending on the nature of the project. |
| `deploy` | Deploy or upload a website project. | Depends on `build`. |
| `publish` | Publish a library on the relevant package manager . | Depends on `build` and `test`. |
| `install` | Install the project locally. | Depends on `build`. |
