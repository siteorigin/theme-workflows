# SiteOrigin Theme Workflows

Reusable GitHub Actions workflows for building and releasing SiteOrigin WordPress themes.

## Quick Setup for New Themes

### Step 1: Create Workflow File

Create `.github/workflows/release.yml` in your theme repository:

```yaml
name: Release Theme

on:
  push:
    tags: ['[0-9]*.[0-9]*.[0-9]*']

jobs:
  release:
    uses: siteorigin/theme-workflows/.github/workflows/build-release.yml@master
    with:
      theme-name: your-theme-name  # Replace with actual theme name
      node-version: '18'
```

### Step 2: Create package.json

```json
{
  "name": "your-theme-name",
  "version": "1.0.0",
  "description": "Build tools for Your Theme Name",
  "scripts": {
    "build": "npm run build:css && npm run build:js",
    "build:css": "sass sass/style.scss:style.css --style=expanded --no-source-map",
    "build:js": "npm run minify:js",
    "minify:js": "find js -name '*.js' ! -name '*.min.js' -type f -exec sh -c 'terser \"$1\" --compress --mangle --output \"${1%.js}.min.js\"' _ {} \\;",
    "test-release": "act push --env GITHUB_REF=refs/tags/1.0.1"
  },
  "author": "SiteOrigin",
  "license": "GPL-3.0",
  "devDependencies": {
    "sass": "^1.69.0",
    "terser": "^5.24.0"
  }
}
```

### Step 3: Create .distignore

```
# Development files
.git*
.github/
node_modules/
package.json
package-lock.json
.distignore
bin/

# Documentation
*.md
CONTRIBUTING.md
CLAUDE.md

# Development assets
sass/
*.scss
*.map

# Testing files
tools/
test-*
*-test.php

# Training/documentation
theme-training/
.claude/

# OS files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Editor files
.idea/
.vscode/
*.swp
*.swo
*~

# Build directories
/dist
/build
```

### Step 4: Test the Setup

1. **Commit and push** the workflow files
2. **Create a test tag**: `git tag 1.0.0-beta && git push origin 1.0.0-beta`
3. **Check GitHub Actions**: Verify the workflow runs successfully
4. **Check Releases**: Confirm the release is created with proper naming

## Usage

## Requirements

Your theme repository must have:

1. **package.json** - Node.js dependencies and build scripts (sass, terser)
2. **.distignore** - Files to exclude from release ZIP
3. **SASS files** (optional) - Will be compiled to CSS if `sass/style.scss` exists
4. **JavaScript files** (optional) - Will be minified if `js/` directory exists

## How Releases Work

1. **Push a version tag**: `git tag 1.0.5 && git push origin 1.0.5`
2. **Workflow automatically**:
   - Compiles SASS to CSS (if `sass/style.scss` exists)
   - Minifies JavaScript files (if `js/` directory exists)
   - Updates version number in `style.css`
   - Creates clean distribution ZIP with excluded files
   - Generates changelog from git commits
   - Publishes GitHub release

### Release Types

- **Regular releases**: `1.0.5` → Published immediately
- **Beta releases**: `1.0.5-beta` → Marked as prerelease
- **Alpha releases**: `1.0.5-alpha` → Marked as prerelease  
- **Release candidates**: `1.0.5-rc1` → Marked as prerelease

### Release Naming

- **Release name**: Just the version number (e.g., `1.0.5`)
- **ZIP filename**: `theme-name.1.0.5.zip` (dot-separated)
- **Folder in ZIP**: `theme-name/` (matches repository name)

## Features

- ✅ **Clean release naming**: Release name = version number only (e.g., `1.0.5`)
- ✅ **Dot-separated ZIP files**: `theme-name.1.0.5.zip` format
- ✅ **Smart prerelease detection**: Auto-detects beta/alpha/rc versions
- ✅ **SASS compilation**: Automatic compilation if `sass/style.scss` exists
- ✅ **JavaScript minification**: Auto-minifies all JS files in `js/` directory
- ✅ **Version updating**: Automatically updates version in `style.css`
- ✅ **Clean release files**: Excludes source/dev files via `.distignore`
- ✅ **Local testing**: Test workflows locally with `act`
- ✅ **Reusable across themes**: Centralized workflow updates
- ✅ **Auto-generated changelogs**: Creates changelogs from git commits

## File Structure

```
your-theme/
├── .github/workflows/release.yml    # Calls reusable workflow
├── package.json                     # Build dependencies (sass, terser)
├── .distignore                      # Release exclusions
├── sass/style.scss                  # Main SASS file (optional)
├── js/                              # JavaScript files (optional)
└── ... (theme files)
```

## Local Testing with Act

You can test the workflow locally using [act](https://github.com/nektos/act) before pushing tags:

### Setup

1. **Install act:**
   ```bash
   # macOS
   brew install act
   
   # Other platforms: see https://github.com/nektos/act#installation
   ```

2. **Test in your theme repository:**
   ```bash
   # Test the workflow locally (simulates tag push)
   act push -e /path/to/test-event.json
   
   # Or test with a specific tag
   act push --env GITHUB_REF=refs/tags/1.0.0
   ```

3. **Create test event file** (optional, in your theme repo):
   ```json
  {
    "ref": "refs/tags/1.0.0",
     "repository": {
       "name": "your-theme-name"
     }
   }
   ```

### Benefits

- ✅ Test SASS compilation locally
- ✅ Debug workflow issues without creating releases  
- ✅ Validate build process before pushing tags
- ✅ Test `.distignore` file exclusions
- ✅ Verify ZIP creation process

### Configuration

The `.actrc` file in this repository provides default settings optimized for theme builds. You can override these in your theme repository if needed.

## Updating

To update the workflow for all themes, simply push changes to this repository. All themes using the workflow will automatically use the latest version on their next release.
