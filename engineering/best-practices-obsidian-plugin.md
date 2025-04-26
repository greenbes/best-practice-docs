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

