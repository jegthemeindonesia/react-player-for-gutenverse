# Implementation Plan: Make Repo Installable via Git

## Objective
Configure the repository `jegthemeindonesia/react-player-for-gutenverse` so it can be installed as a dependency in other projects using a Git URL (e.g., `"react-player": "git+https://..."`).

## Current State Analysis
- **Root Directory**: Contains a placeholder `package.json` with an invalid `main` path (`dist/index.js` does not exist in root).
- **`source/` Directory**: Contains the actual library source code, including `src`, `scripts`, and a valid `package.json`.
- **`2.14.1/` Directory**: Contains a pre-built version of the library.

## Recommended Strategy: Promote Source to Root
To function as a standard NPM package, the repository root must contain the valid `package.json` and source files.

### Step-by-Step Plan

1.  **Preserve History (Optional)**
    - *Action*: Create a backup branch or tag for the current state if `2.14.1` folder is non-recoverable elsewhere.

2.  **Restructure Repository**
    - *Action*: Delete the current root `package.json`.
    - *Action*: Move all contents from `source/` to the root directory (`./`).
    - *Action*: (Optional) Remove `2.14.1/` and empty `source/` directories to clean up.

3.  **Update `package.json`**
    - *Action*: Open the newly moved `package.json` (now in root).
    - *Action*: Update `name` to `react-player-for-gutenverse` (optional, but clarifies it's a fork).
    - *Action*: **CRITICAL**: Add a `prepare` script.
      ```json
      "scripts": {
        ...
        "prepare": "npm run build:lib"
      }
      ```
      *Reason*: When installing from Git, NPM automatically runs the `prepare` script. This causes the consumer's machine to build the `lib/` folder from `src/` upon installation, ensuring `main` points to a valid file.

4.  **Validate Local Dependency**
    - *Action*: Verify the `"builder": "file:./scripts/builder"` dependency in `package.json` resolves correctly after moving files. It should work as `scripts/` moves with `package.json`.

5.  **Commit and Push**
    - *Action*: Commit the changes to the `main` branch.

### Alternative Strategy: Commit Build Artifacts (Simpler for Consumer)
If you prefer not to force consumers to build the project (which requires installing `devDependencies` and running build scripts):

1.  **Restructure**: Move `source/` to root as above.
2.  **Build Locally**: Run `npm install && npm run build:lib`.
3.  **Update `.gitignore`**: Remove `lib/` (and `dist/` if needed) from `.gitignore`.
4.  **Commit Artifacts**: Commit the generated `lib/` folder to the repository.
5.  **No Prepare Script**: Do not add `npm run build:lib` to `prepare`.

*Recommendation*: The **Standard Strategy** (Step 3) is cleaner and standard for open-source. The **Alternative Strategy** is often more reliable for private/internal git dependencies to standardise the artifact. This plan assumes Standard Strategy first.
