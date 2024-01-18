## Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.


# Chapter 6. Settin Up Your Database

## Create GibHub repository

* create repository & git setting

```bash
git remote add origin https://github.com/jackpark-dev/jackpark-dev-nextjs-dashboard.git
git branch -M main
git push -u origin main
```

## Create a Vercel account

Visit [vercel.com/signup](https://vercel.com/signup) to create an account. Choose the free "hobby" plan. Select Continue with GitHub to connect your GitHub and Vercel accounts.

## Connect and deploy your project

## Create a Postgres database

* create "nextjs-dashboard-postgres"

* Once connected, navigate to the .env.local tab, click Show secret and Copy Snippet. Make sure you reveal the secrets before copying them.

* Navigate to your code editor and rename the .env.example file to .env. Paste in the copied contents from Vercel.

* Important: Go to your .gitignore file and make sure .env is in the ignored files to prevent your database secrets from being exposed when you push to GitHub

* npm i @vercel/postgres in your terminal to install the Vercel Postgres SDK.

## Seed your database

* Edit package.json

```json
"scripts": {
  "build": "next build",
  "dev": "next dev",
  "start": "next start",
  "seed": "node -r dotenv/config ./scripts/seed.js"
},
```

* Run seeding command

Populating the database with an initial set of data

```bash
npm run seed
```
