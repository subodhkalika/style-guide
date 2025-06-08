# Chapter 7: Linting and Formatting

## 🛠️ Linting

- We enforce strict linting using **gts (Google TypeScript Style)** to maintain consistent code quality.
- The following scripts are included in every project's package.json.
---

### Run linting
Use the following command to automatically correct most linting issues:
```bash
npm run fix
```
This script will apply fixes to your code where possible, ensuring it meets the defined style guidelines and linting rules.

---

### Configure
Our linting configuration is based on tsconfig-google.json. Extend this configuration in your tsconfig.json:

```
{
  "extends": "./node_modules/gts/tsconfig-google.json"
}
```
---

### Usage
The following scripts are included in the package.json to maintain consistent code quality and formatting:

```json
"scripts": {
  "lint": "gts lint",          // Run the linter to check code style
  "check": "gts check",        // Verify formatting and linting
  "clean": "gts clean",        // Remove temporary and generated files
  "fix": "gts fix"             // Automatically fix linting issues
}
```

> ⚠️ **Important:** All code must be linted and formatted before being committed and merged. Any pull request (PR) that fails lint checks will be rejected until all issues are resolved. This ensures that the main branch remains clean, consistent, and production-ready.

