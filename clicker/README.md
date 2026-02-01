# Clicker App Deployment Setup

This directory contains the deployed Angular clicker app, which is automatically published from the [clicker repository](https://github.com/ajb142/clicker).

## Deployment Process

The clicker app is deployed to this location via a GitHub Actions workflow in the clicker repository. The workflow:

1. Builds the Angular application with `--base-href=/clicker/`
2. Pushes the built files to the `/clicker` directory in this repository
3. The GitHub Pages site automatically serves the updated files

## Setup Instructions for Clicker Repository

To enable automatic deployment from the clicker repository to this location, the following workflow file should be added to the clicker repository at `.github/workflows/deploy-to-github-io.yml`:

```yaml
name: Deploy to ajb142.github.io

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout clicker repository
        uses: actions/checkout@v4
        with:
          path: clicker-repo
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: clicker-repo/package-lock.json
      
      - name: Install dependencies
        working-directory: clicker-repo
        run: npm install --legacy-peer-deps
      
      - name: Build Angular app
        working-directory: clicker-repo
        run: npm run build -- --base-href=/clicker/
      
      - name: Checkout github.io repository
        uses: actions/checkout@v4
        with:
          repository: ajb142/ajb142.github.io
          path: github-io-repo
          token: ${{ secrets.GH_PAT }}
      
      - name: Clear and update clicker directory
        run: |
          # Remove old files (except .gitkeep and README.md)
          find github-io-repo/clicker -mindepth 1 -type f ! -name '.gitkeep' ! -name 'README.md' -delete
          find github-io-repo/clicker -mindepth 1 -type d -exec rm -rf {} + 2>/dev/null || true
          
          # Copy new build files
          cp -r clicker-repo/docs/browser/* github-io-repo/clicker/
          
          # Create .nojekyll to prevent Jekyll processing
          touch github-io-repo/clicker/.nojekyll
      
      - name: Commit and push to github.io repository
        working-directory: github-io-repo
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add clicker
          git diff --staged --quiet || git commit -m "Deploy clicker app from ajb142/clicker@${{ github.sha }}"
          git push
```

## Required Permissions and Setup

The workflow requires a Personal Access Token (PAT) to push to the github.io repository:

1. **Create a Personal Access Token**:
   - Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
   - Generate a new token with `repo` scope (full control of private repositories)
   - Copy the token value

2. **Add the token as a secret in the clicker repository**:
   - Go to the clicker repository settings
   - Navigate to Secrets and variables → Actions
   - Click "New repository secret"
   - Name: `GH_PAT`
   - Value: paste your token
   - Click "Add secret"

3. **Workflow permissions**:
   - The workflow uses `contents: write` permission for git operations
   - The `GH_PAT` secret provides authentication to push to ajb142.github.io

## Manual Deployment

To manually trigger a deployment, go to the clicker repository's Actions tab and use the "workflow dispatch" option.

## Viewing the App

Once deployed, the clicker app will be available at:
https://ajb142.github.io/clicker/
