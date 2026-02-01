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
          token: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Clear and update clicker directory
        run: |
          # Remove old files (except .gitkeep)
          find github-io-repo/clicker -mindepth 1 ! -name '.gitkeep' -delete
          
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

## Required Permissions

The workflow requires:
- `contents: write` permission to push to the target repository
- The `GITHUB_TOKEN` automatically provided by GitHub Actions has the necessary permissions

## Manual Deployment

To manually trigger a deployment, go to the clicker repository's Actions tab and use the "workflow dispatch" option.

## Viewing the App

Once deployed, the clicker app will be available at:
https://ajb142.github.io/clicker/
