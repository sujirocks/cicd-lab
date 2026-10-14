# CI/CD Lab Starter

A small Express and TypeScript app for the CI/CD Crash Course. Use this starting point to follow along with automated testing, GitHub Actions, and deployment to Render.

The starter includes a home page, a health endpoint, and a passing health endpoint test. You will add the combined `verify` script, GitHub Actions workflow, and version endpoint during the course.

## Requirements

- Node.js 24 and npm
- Git
- GitHub and Render accounts for the deployment sections

## Get started

Clone the starter into a new folder:

```bash
git clone https://github.com/bradtraversy/cicd-lab-starter.git cicd-lab
cd cicd-lab
npm ci
```

Start the development server:

```bash
npm run dev
```

Open [localhost:3000](http://localhost:3000). The [health endpoint](http://localhost:3000/api/health) returns HTTP 200 with:

```json
{"status":"ok"}
```

Stop the server with Ctrl+C.

## Scripts

- `npm run dev`: start the development server with automatic restarts.
- `npm run typecheck`: check TypeScript without generating output.
- `npm test`: run the health endpoint test. The test starts and stops its own server.
- `npm run build`: compile the app into `dist`.
- `npm start`: run the compiled app after building it.

## Start your own repository

From inside your newly cloned `cicd-lab` folder, remove the starter's Git history and remote connection, then initialize your own repository. This preserves the app files.

These commands use Bash or Zsh. On Windows, use Git Bash.

```bash
rm -rf .git
git init -b main
git add .
git commit -m "feat: add CI/CD lab starter"
```

Create an empty `cicd-lab` repository on GitHub without a README, license, or gitignore. Replace `YOUR_USERNAME` below with your GitHub username:

```bash
git remote add origin https://github.com/YOUR_USERNAME/cicd-lab.git
git push -u origin main
```

Continue with the course to add verification and deployment. Keep `package-lock.json` committed; dependencies, build output, and environment files are excluded by `.gitignore`.

## Configuration

The server uses port `3000` unless `PORT` is set. The home page displays `APP_VERSION`, defaulting to `development`. Neither variable is required to run the starter locally.


## Additional steps and configuration changes to deploy the same through github Pages on below URL:

```bash
sujirocks.github.io/cicd-lab/
```

A. A Quick Reality Check on GitHub F ys
GitHub Pages can only host static websites (m ıML, CSS, Client-side JavaScript). It cannot run
a live Node.js or Express backend server (like node dist/server.js ).
If your repository is meant to be a backend API or a Node application, deploying it to GitHub
Pages won't work out of the box because GitHub Pages doesn't execute Node.js code.
What are you trying to achieve?
Depending on what you want to do with this cicd-Lab project, here are the paths forward:

1. If you actually want to deploy a frontend or static site:
We need to change this project to include frontend files, or adjust your setup.

2. If you want to practice CI/CD pipelines (GitHub Actions) for a Node.js backend:
Your verify script( npm run verify whichruns typechecking, tests, and tsc to
build dist/server. js ) is fantastic for a Cl pipeline! However, instead of deploying to
GitHub Pages, you would typically deploy a backend app to a platform like Render,
Railway, Fly.io, or AWS. v

3. If you are following a specific tutorial or lab:
Let me know what the goal of the lab is, and | can guide you on the exact right setup!


To turn this Node.js/TypeScript project into a project that can also serve a static frontend (or
static files) so it works on GitHub Pages, wec „ idasimple public folder with an HTML file,
and configure your build script to copy those suatic files into your dist folder along with your
compiled TypeScript code.
Here is how you can adapt your existing project step-by-step:
Step 1: Create a public folder for your frontend
iL In the root directory of your repository, create a new folder named public .

2. Inside the public folder, createan index.html file.
Add some basic HTML to it:

```<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CI/CD Lab</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <main>
        <h1>Welcome to my CI/CD Lab!</h1>
        <p>This static page is hosted on GitHub Pages via GitHub Actions.</p>
    </main>
</body>
</html>
```

Step 2: Update your package. json to copy static files during build
Since your TypeScript compiler ( tsc ) only compiles .ts filesinto dist/ , we needa small
commtoa conpy dyo ur public folder into dist/ whenever yourun npm run build.
Update your "scripts" sectionin package. json to look like this:

```
"scripts": {
    "dev": "tsx watch src/server.ts",
    "typecheck": "tsc --noEmit",
    "test": "node --import tsx --test test/app.test.ts",
    "build": "tsc && cp -r public/* dist/",
    "start": "node dist/server.js",
    "verify": "npm run typecheck && npm test && npm run build"
  }

```

Step 3: Update your GitHub Actions Workflow ( .github/workflows/ci.yml )
Now that your build step places all your static frontend files inside the dist folder, you can set
your deployment artifact path to ./dist !
Here is your complete .github/workflows/ci.yml file readyt o go:

```
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  verify:
    name: Verify
    runs-on: ubuntu-latest
    timeout-minutes: 10

    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Verify application
        run: npm run verify

  deploy:
    name: Deploy to GitHub Pages
    needs: verify
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install dependencies & Build
        run: |
          npm ci
          npm run build

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          # Now 'dist' will exist because npm run build just created it!
          path: './dist'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4

```

Step 4: Push and Verify
1. Commit these changes ( public/index.html ‚updated package. json ‚and updated
.github/workflows/ci.yml).
2. Push to your main branch.
3. Check your Actions tab on GitHub tov | it verify and deploy successfully.
4. Once finished, your static frontend will be live at
[https://{git_account_username}.github.io/cicd-lab/]

