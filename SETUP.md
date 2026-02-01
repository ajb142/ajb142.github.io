# Quick Setup Guide for Clicker App Deployment

This guide explains how to enable automatic deployment of the clicker app to https://ajb142.github.io/clicker/

## Overview

The clicker app (from https://github.com/ajb142/clicker) will be automatically deployed to the `/clicker` directory of this repository whenever changes are pushed to the main branch of the clicker repository.

## Setup Steps

### 1. Create a Personal Access Token (PAT)

1. Go to your GitHub account settings: https://github.com/settings/tokens
2. Click on "Developer settings" → "Personal access tokens" → "Tokens (classic)"
3. Click "Generate new token" → "Generate new token (classic)"
4. Configure the token:
   - **Note**: "Clicker App Deployment to github.io"
   - **Expiration**: Choose an appropriate expiration (90 days, 1 year, or no expiration)
   - **Scopes**: Select `repo` (Full control of private repositories)
5. Click "Generate token"
6. **IMPORTANT**: Copy the token value immediately (you won't be able to see it again!)

### 2. Add the Token as a Secret in the Clicker Repository

1. Go to the clicker repository: https://github.com/ajb142/clicker
2. Click on "Settings" → "Secrets and variables" → "Actions"
3. Click "New repository secret"
4. Enter the secret details:
   - **Name**: `GH_PAT`
   - **Secret**: Paste the token you copied in step 1
5. Click "Add secret"

### 3. Copy the Workflow File to the Clicker Repository

1. Copy the workflow file from this repository:
   - Source: `.github/workflows/deploy-clicker-template.yml`
   - Destination: `.github/workflows/deploy-to-github-io.yml` (in the clicker repository)

2. You can do this by:
   - Cloning both repositories locally and copying the file, OR
   - Creating a new file directly in the clicker repository's GitHub interface with the content from the template

### 4. Verify the Setup

1. After adding the workflow file, go to the clicker repository
2. Navigate to the "Actions" tab
3. You should see the new workflow "Deploy to ajb142.github.io"
4. You can manually trigger it using "Run workflow" button, or it will run automatically on the next push to main

### 5. Check the Deployment

After the workflow runs successfully:
1. The `/clicker` directory in this repository will contain the built app
2. The app will be accessible at: https://ajb142.github.io/clicker/
3. The main landing page at https://ajb142.github.io/ includes a link to the clicker app

## Troubleshooting

### Workflow fails with "Permission denied"
- Verify that the `GH_PAT` secret is correctly set in the clicker repository
- Ensure the PAT has the `repo` scope
- Check that the token hasn't expired

### App doesn't load at /clicker/
- Verify that the workflow completed successfully in the Actions tab
- Check that the files were committed to the `/clicker` directory in this repository
- Ensure GitHub Pages is enabled for this repository (Settings → Pages)

### Build fails
- Check that the Angular app builds successfully in the clicker repository
- Verify Node.js version compatibility (workflow uses Node 20)

## Files Modified in This Repository

- **`/clicker/.gitkeep`**: Placeholder to keep the directory in git
- **`/clicker/README.md`**: Detailed deployment documentation
- **`/index.html`**: Added link to clicker app in Projects section
- **`/README.md`**: Updated with repository structure and deployment info
- **`.github/workflows/deploy-clicker-template.yml`**: Workflow template to copy to clicker repo

## Additional Resources

- GitHub Actions documentation: https://docs.github.com/en/actions
- GitHub Pages documentation: https://docs.github.com/en/pages
- Angular deployment guide: https://angular.dev/tools/cli/deployment
