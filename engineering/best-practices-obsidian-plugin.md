# Creatng an Obsidian Plugin

To create an Obsidian plugin that users can install directly from the Community Plugins window, you must follow a particular repository structure and provide specific metadata files. Here is a formal overview of the requirements:



## Git Repository Structure

Your Git repository should contain at least the following elements:

```
root/
│
├── main.ts (or main.js)          # Your plugin's main source code
├── manifest.json                 # Metadata describing the plugin
├── versions.json (optional)      # Used for version-specific updates (advanced)
├── README.md                     # Clear instructions and description
├── LICENSE                       # Open-source license file
├── .github/
│   └── workflows/
│       └── build.yml             # GitHub Actions workflow for automatic builds (if using TypeScript)
└── (optional) src/               # If using a src directory for organized source files
```

If you are using TypeScript (which is highly recommended for Obsidian plugins), your workflow typically includes:

	- `src/` directory with your TypeScript code.
	- A `tsconfig.json` configuration file.
	- A build process (usually via GitHub Actions) to compile .ts to .js.

## Required Files

### File: manifest.json

This file must be present in the root directory. It provides metadata used by Obsidian to identify and load your plugin.

Example structure:

```json
{
  "id": "your-plugin-id",
  "name": "Your Plugin Name",
  "version": "1.0.0",
  "minAppVersion": "0.12.0",
  "description": "A short description of your plugin.",
  "author": "Your Name",
  "authorUrl": "https://yourwebsite.com",
  "isDesktopOnly": false
}
```

Important:

	- id must be unique and lowercase, often matching the GitHub repository name.
	- version must match your release version tag.

### File: main.js

This is the compiled JavaScript file that Obsidian will load. If you write your plugin in TypeScript, you must compile it.

Note: If your repository contains only TypeScript and no compiled main.js, it will not be accepted into the community plugins list.

### File: README.md

This file should:
	- Explain the plugin’s purpose.
	- Include clear installation and usage instructions.
	- Optionally provide screenshots, links, etc.

### File: LICENSE

You must provide an open-source license file (e.g., MIT License). Obsidian requires all community plugins to be open-source.



## Recommended Files (Optional but Encouraged)

### File: versions.json

If you expect to make complex versioning adjustments for Obsidian compatibility, you can use this, but it is not required initially.

### File: .github/workflows/build.yml

A GitHub Actions workflow can automate the build process whenever you push changes. This helps ensure that the latest main.js is always generated without manual builds.

Example basic build.yml for TypeScript:

```yaml
name: Build Plugin

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 16
      - run: npm install
      - run: npm run build
      - name: Commit built files
        run: |
          git config --global user.name 'GitHub Actions'
          git config --global user.email 'actions@github.com'
          git add main.js manifest.json
          git commit -m "Build plugin"
          git push
```

## Typical Development Process

	1. Write your plugin code in TypeScript.
	2. Create manifest.json and README.md.
	3. Set up package.json and tsconfig.json if using TypeScript.
	4. Compile to JavaScript (main.js).
	5. Test the plugin locally by copying your repository to .obsidian/plugins/your-plugin-id/.
	6. Push your repository to GitHub.
	7. Submit a pull request to the Obsidian Community Plugins GitHub repository.



## Key Considerations

	- Your plugin must not rely on proprietary or non-open libraries without compliance.
	- Follow semantic versioning (major.minor.patch) in your GitHub releases and manifest.json.
	- Tag releases properly on GitHub (e.g., v1.0.0).
	- Minimize plugin size by excluding unnecessary files using .gitignore.


# 📜 Golden Reference for Obsidian Plugin Structure and Naming Rules

## Error and Debug Information

Obsidian doesn't write out dedicated plugin log files to disk. Instead, errors and diagnostic messages from plugins are logged to the developer console. To view these messages, open Obsidian and then open the developer tools (for example, on macOS you can press Cmd+Option+I) and check the `Console` tab for error details.

## 🗂 1. Correct Folder Structure

```
your-vault/
└── .obsidian/
    └── plugins/
        └── always-close-sidebars/
            ├── manifest.json
            ├── main.js
            ├── main.js.map
            ├── styles.css (optional)
```

**Key**:  
The folder name (`always-close-sidebars`) must match the `"id"` field in your `manifest.json`.

---

## 📄 2. Correct `manifest.json`

```json
{
  "id": "always-close-sidebars",
  "name": "Always Close Sidebars",
  "version": "1.0.0",
  "minAppVersion": "0.15.0",
  "description": "A plugin that always closes the left and right sidebars and hides the Properties view.",
  "author": "Your Name",
  "authorUrl": "https://github.com/yourusername",
  "isDesktopOnly": false
}
```

✅ **Notes**:
- `"id"` **MUST** match the plugin folder name exactly.
- `"version"` must be **SemVer** format (e.g., `1.0.0`).
- `"minAppVersion"` must match the earliest Obsidian version you support.
- `"authorUrl"` is optional but **recommended** for public profiles.
- `"isDesktopOnly"` is `true` if your plugin doesn't work on mobile (set `false` if it works everywhere).

---

## 📄 3. Correct `package.json`

```json
{
  "name": "always-close-sidebars",
  "version": "1.0.0",
  "description": "Obsidian plugin to close sidebars and hide Properties view.",
  "main": "main.js",
  "scripts": {
    "dev": "tsc --watch",
    "build": "tsc"
  },
  "author": "Your Name",
  "license": "MIT",
  "devDependencies": {
    "typescript": "^5.0.0",
    "obsidian": "latest"
  }
}
```

✅ **Notes**:
- `"name"` should match `"id"` from `manifest.json` for consistency (though technically not enforced).
- `"main"` points to the compiled JavaScript entrypoint (usually `main.js`).
- `"scripts"` allow quick build commands.
- `"license"` should be a clear OSI-approved license (MIT is recommended).

---

## 🛠 4. Compilation

- Write your plugin in **TypeScript** (`main.ts`).
- Compile with `tsc` (TypeScript compiler).
- Deliver **only**:
  - `manifest.json`
  - `main.js`
  - `main.js.map`
  - (`styles.css` if you use any styling)

Never deliver `.ts` files to users unless it’s a development version.

---

## 📐 5. Best Practices Checklist

| Rule | Status |
|:-----|:-------|
| Folder name = `id` in `manifest.json` | ✅ Required |
| Use Semantic Versioning in `version` fields | ✅ Required |
| Keep only runtime files in plugin directory | ✅ Required |
| `README.md` with clear description and instructions | ✅ Strongly Recommended |
| `LICENSE` file (MIT recommended) | ✅ Strongly Recommended |
| Use `tsconfig.json` to enforce strict TypeScript typing | ✅ Recommended |
| Optional `styles.css` should be minimal and scoped | ✅ Recommended if needed |
| Use `this.addCommand` to register commands cleanly | ✅ Required |
| Avoid breaking Obsidian core behavior | ✅ Required |
| Respect mobile/desktop flags if platform-specific | ✅ Required |

---

## 📋 6. Optional Advanced Best Practices

| Practice | Why |
|:---------|:----|
| Add a `CHANGELOG.md` | Track plugin versions and features |
| Add GitHub Actions for auto-building releases | Save time for public plugins |
| Include typed API dependencies for Obsidian (`obsidian.d.ts`) | Stronger type checking |
| Use ESLint and Prettier | Maintain code quality and formatting |
| Add Plugin Settings (if applicable) | Allow users to customize behavior |
| Add i18n support (translations) if targeting wide users | Optional but appreciated |

---

# 🎯 Final Summary

| Element | Example |
|:--------|:--------|
| Folder name | `always-close-sidebars` |
| manifest.json `"id"` | `always-close-sidebars` |
| package.json `"name"` | `always-close-sidebars` |
| Compile entry | `main.ts` → `main.js` |
| Output files | `manifest.json`, `main.js`, `main.js.map`, (optional) `styles.css` |
| License | MIT |
| Required format compliance | Semantic Versioning, API compatibility, minimal side effects |

---

# ✅ If you follow this reference:

- Your plugin will be **valid**, **load properly**, **pass community plugin reviews**, and **look professional**.
- You will be ready to submit to the [Obsidian Community Plugins](https://publish.obsidian.md/plugins/02+-+Community+plugin+submission+guide).

