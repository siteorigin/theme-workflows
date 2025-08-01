# SiteOrigin Theme Workflows

This repository contains reusable GitHub Actions workflows for building and releasing WordPress themes.

## Usage

To use this workflow in your theme repository, create `.github/workflows/release.yml`:

```yaml
name: Release Theme

on:
  push:
    tags: ['v*']

jobs:
  release:
    uses: siteorigin/theme-workflows/.github/workflows/build-release.yml@main
    with:
      theme-name: your-theme-name
      node-version: '18'
    secrets:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Requirements

Your theme repository must have:

1. **package.json** - Node.js dependencies and build scripts
2. **gulpfile.js** - Build process (uses existing `build-config.js`)
3. **.distignore** - Files to exclude from release ZIP
4. **build-config.js** - Theme build configuration (existing)

## How it works

1. Push a version tag (e.g., `v1.20.12`)
2. Workflow automatically:
   - Builds theme assets (SASS, JS)
   - Updates version in theme files
   - Creates clean distribution ZIP
   - Publishes to GitHub Releases

## Features

- ✅ No livereload or local dev features
- ✅ Uses existing `build-config.js` structure
- ✅ Automatic version detection from tags  
- ✅ Clean release files (excludes source/dev files)
- ✅ Reusable across all themes
- ✅ Centralized workflow updates

## File Structure

```
your-theme/
├── .github/workflows/release.yml    # Calls reusable workflow
├── package.json                     # Build dependencies
├── gulpfile.js                      # Build process
├── .distignore                      # Release exclusions
├── build-config.js                  # Theme config (existing)
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
   act push --env GITHUB_REF=refs/tags/v1.0.0
   ```

3. **Create test event file** (optional, in your theme repo):
   ```json
   {
     "ref": "refs/tags/v1.0.0",
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