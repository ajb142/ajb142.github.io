# ajb142.github.io

Personal website hosted on GitHub Pages.

## Structure

- **`index.html`** - Main landing page
- **`/clicker`** - Angular clicker app (deployed from [ajb142/clicker](https://github.com/ajb142/clicker))

## Clicker App Deployment

The clicker app is automatically deployed to the `/clicker` directory via a GitHub Actions workflow in the clicker repository. See `/clicker/README.md` for details on the deployment setup.

To set up automatic deployment from the clicker repository:
1. Copy `.github/workflows/deploy-clicker-template.yml` to the clicker repository as `.github/workflows/deploy-to-github-io.yml`
2. The workflow will automatically run on pushes to the main branch

## Live Site

- **Main site**: https://ajb142.github.io/
- **Clicker app**: https://ajb142.github.io/clicker/

