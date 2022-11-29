# Deploy Vite app to GitHub Pages using GitHub Actions


## Step-by-step instructions

### Scaffold a new Vite app and init git

```bash
# Create new Vite project using React template
npm create vite@latest vite-project -- --template react

# Install dependencies and start development server
cd vite-project
npm install
npm run dev
```

If the project is working fine, let's init a new git repository.

```bash
git init
git add .
git commit -m "init vite project"
```

* https://vitejs.dev/guide/#scaffolding-your-first-vite-project

### Create a new GitHub repository

Go to https://github.com/new and create a new repository.

❗️ Make sure **Public** is selected if you don't have a premium account. Otherwise, you won't be able to host your app using GitHub pages.
![Screen Shot 2022-05-15 at 16 19 34](https://user-images.githubusercontent.com/58401630/168477505-b3f2fc8f-a248-499c-9645-0c0d2bd5de35.png)

Once the repo is created, copy and paste the instructions similar to these to your terminal

```bash
git remote add origin git@github.com:sitek94/vite-deploy-demo.git
git branch -M main
git push -u origin main
```

### Create deployment workflow

Create a new file: `.github/workflows/deploy.yml` and paste the following code:

```yml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v2

      - name: Setup Node
        uses: actions/setup-node@v1
        with:
          node-version: 16

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - name: Build project
        run: npm run build

      - name: Upload production-ready build files
        uses: actions/upload-artifact@v2
        with:
          name: production-files
          path: ./dist

  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Download artifact
        uses: actions/download-artifact@v2
        with:
          name: production-files
          path: ./dist

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

This workflow will run on every push to the `main` branch. It will first build the project, and then deploy it to GitHub pages.

### Test deployment workflow

Commit deployment workflow and push the changes to GitHub.

```bash
git add .
git commit -m "add deploy workflow"
git push
```

### Final 

![Screen Shot 2022-05-15 at 17 31 23](https://user-images.githubusercontent.com/58401630/168480802-95978d6c-2532-49f0-b118-6a313286d512.png)

## Resources

- [Vite](https://vitejs.dev/)
- [GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site#creating-your-site)
- [GitHub Actions](https://docs.github.com/en/actions)
