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

# Chapter 7. Fetching Data

## What are request waterfalls?

When might you want to use a waterfall pattern?
> To satisfy a condition before making the next request

## Parallel data fetching

# Chapter 8. Static and Dynamic Rendering

## What is Static Rendering?

With static rendering, data fetching and rendering happens on the server at build time (when you deploy) or during revalidation. The result can then be distributed and cached in a Content Delivery Network (CDN).

* Faster Websites - Prerendered content can be cached and globally distributed. This ensures that users around the world can access your website's content more quickly and reliably.

* Reduced Server Load - Because the content is cached, your server does not have to dynamically generate content for each user request.

* SEO - Prerendered content is easier for search engine crawlers to index, as the content is already available when the page loads. This can lead to improved search engine rankings.

Why might static rendering not be a good fit for a dashboard app?
> Because the application will not reflect the latest data changes

## What is Dynamic Rendering?

With dynamic rendering, content is rendered on the server for each user at request time (when the user visits the page). There are a couple of benefits of dynamic rendering:

* Real-Time Data - Dynamic rendering allows your application to display real-time or frequently updated data. This is ideal for applications where data changes often.

* User-Specific Content - It's easier to serve personalized content, such as dashboards or user profiles, and update the data based on user interaction.

* Request Time Information - Dynamic rendering allows you to access information that can only be known at request time, such as cookies or the URL search parameters.

What kind of information can only be known at request time?
> Cookies and URL search params




