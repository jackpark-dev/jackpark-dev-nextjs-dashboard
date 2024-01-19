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


## Making the dashboard dynamic


# Chapter 9. Streaming

## What is streaming?

Streaming is a data transfer technique that allows you to break down a route into smaller "chunks" and progressively stream them from the server to the client as they become ready.


There are two ways you implement streaming in Next.js:

At the page level, with the loading.tsx file.
For specific components, with <Suspense>.

Streaming works well with React's component model, as each component can be considered a chunk.

There are two ways you implement streaming in Next.js:

At the page level, with the loading.tsx file.

1. For specific components, with <Suspense>.
2. Let's see how this works.

What is one advantage of streaming?
> Chunks are rendered in parallel, reducing the overall load time

## Streaming a whole page with loading.tsx

In the /app/dashboard folder, create a new file called loading.tsx:
```javascript
export default function Loading() {
  return <div>Loading...</div>;
}
```

A few things are happening here:
1. loading.tsx is a special Next.js file built on top of Suspense, it allows you to create fallback UI to show as a replacement while page content loads.

2. Since <SideNav> is static, it's shown immediately. The user can interact with <SideNav> while the dynamic content is loading.

3. The user doesn't have to wait for the page to finish loading before navigating away (this is called interruptable navigation).

## Adding loading skeletons

* /app/dashboard/loading.tsx
```javascript
import DashboardSkeleton from '@/app/ui/skeletons';
 
export default function Loading() {
  return <DashboardSkeleton />;
}
```

## Fixing the loading skeleton bug with route groups

Right now, your loading skeleton will apply to the invoices and customers pages as well.

Since <mark>loading.tsx</mark> is a level higher than `/invoices/page.tsx` and /customers/page.tsx in the file system, it's also applied to those pages.

We can change this with [Route Groups](https://nextjs.org/docs/app/building-your-application/routing/route-groups). Create a new folder called /(overview) inside the dashboard folder. Then, move your loading.tsx and page.tsx files inside the folder:

Now, the loading.tsx file will only apply to your dashboard overview page.

Route groups allow you to organize files into logical groups without affecting the URL path structure. When you create a new folder using parentheses (), the name won't be included in the URL path. So `/dashboard/(overview)/page.tsx` becomes `/dashboard`.

Here, you're using a route group to ensure `loading.tsx` only applies to your dashboard overview page. However, you can also use route groups to separate your application into sections (e.g. `(marketing)` routes and `(shop)` routes) or by teams for larger applications.

## Streaming a component

So far, you're streaming a whole page. But, instead, you can be more granular and stream specific components using React Suspense.

Suspense allows you to defer rendering parts of your application until some condition is met (e.g. data is loaded). You can wrap your dynamic components in Suspense. Then, pass it a fallback component to show while the dynamic component loads.

If you remember the slow data request, fetchRevenue(), this is the request that is slowing down the whole page. Instead of blocking your page, you can use Suspense to stream only this component and immediately show the rest of the page's UI.

To do so, you'll need to move the data fetch to the component, let's update the code to see what that'll look like:

Delete all instances of fetchRevenue() and its data from /dashboard/(overview)/page.tsx:

* /dashboard/(overview)/page.tsx
```javascript
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data'; // remove fetchRevenue
 
export default async function Page() {
  const revenue = await fetchRevenue // delete this line
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    // ...
  );
}
```

Then, import <Suspense> from React, and wrap it around <RevenueChart />. You can pass it a fallback component called <RevenueChartSkeleton>.

* /app/dashboard/(overview)/page.tsx
```javascript
import { Card } from '@/app/ui/dashboard/cards';
import RevenueChart from '@/app/ui/dashboard/revenue-chart';
import LatestInvoices from '@/app/ui/dashboard/latest-invoices';
import { lusitana } from '@/app/ui/fonts';
import { fetchLatestInvoices, fetchCardData } from '@/app/lib/data';
import { Suspense } from 'react';
import { RevenueChartSkeleton } from '@/app/ui/skeletons';
 
export default async function Page() {
  const latestInvoices = await fetchLatestInvoices();
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Card title="Collected" value={totalPaidInvoices} type="collected" />
        <Card title="Pending" value={totalPendingInvoices} type="pending" />
        <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
        <Card
          title="Total Customers"
          value={numberOfCustomers}
          type="customers"
        />
      </div>
      <div className="mt-6 grid grid-cols-1 gap-6 md:grid-cols-4 lg:grid-cols-8">
        <Suspense fallback={<RevenueChartSkeleton />}>
          <RevenueChart />
        </Suspense>
        <LatestInvoices latestInvoices={latestInvoices} />
      </div>
    </main>
  );
}
```
Finally, update the <RevenueChart> component to fetch its own data and remove the prop passed to it:

* /app/ui/dashboard/revenue-chart.tsx
```javascript
import { generateYAxis } from '@/app/lib/utils';
import { CalendarIcon } from '@heroicons/react/24/outline';
import { lusitana } from '@/app/ui/fonts';
import { fetchRevenue } from '@/app/lib/data';
 
// ...
 
export default async function RevenueChart() { // Make component async, remove the props
  const revenue = await fetchRevenue(); // Fetch data inside the component
 
  const chartHeight = 350;
  const { yAxisLabels, topLabel } = generateYAxis(revenue);
 
  if (!revenue || revenue.length === 0) {
    return <p className="mt-4 text-gray-400">No data available.</p>;
  }
 
  return (
    // ...
  );
}
 
```
Now refresh the page, you should see the dashboard information almost immediately, while a fallback skeleton is shown for <RevenueChart>:

## Practice: Streaming <LatestInvoices>

## Grouping components

Great! You're almost there, now you need to wrap the <Card> components in Suspense. You can fetch data for each individual card, but this could lead to a popping effect as the cards load in, this can be visually jarring for the user.

So, how would you tackle this problem?

To create more of a staggered effect, you can group the cards using a wrapper component. This means the static <SideNav/> will be shown first, followed by the cards, etc.

In your page.tsx file:

1. Delete your <Card> components.
2. Delete the fetchCardData() function.
3. Import a new wrapper component called <CardWrapper />.
4. Import a new skeleton component called <CardsSkeleton />.
5. Wrap <CardWrapper /> in Suspense.

* /app/dashboard/page.tsx

```javascript
import CardWrapper from '@/app/ui/dashboard/cards';
// ...
import {
  RevenueChartSkeleton,
  LatestInvoicesSkeleton,
  CardsSkeleton,
} from '@/app/ui/skeletons';
 
export default async function Page() {
  return (
    <main>
      <h1 className={`${lusitana.className} mb-4 text-xl md:text-2xl`}>
        Dashboard
      </h1>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        <Suspense fallback={<CardsSkeleton />}>
          <CardWrapper />
        </Suspense>
      </div>
      // ...
    </main>
  );
}
```

Then, move into the file /app/ui/dashboard/cards.tsx, import the fetchCardData() function, and invoke it inside the <CardWrapper/> component. Make sure to uncomment any necessary code in this component.

* /app/ui/dashboard/cards.tsx
```javascript
// ...
import { fetchCardData } from '@/app/lib/data';
 
// ...
 
export default async function CardWrapper() {
  const {
    numberOfInvoices,
    numberOfCustomers,
    totalPaidInvoices,
    totalPendingInvoices,
  } = await fetchCardData();
 
  return (
    <>
      <Card title="Collected" value={totalPaidInvoices} type="collected" />
      <Card title="Pending" value={totalPendingInvoices} type="pending" />
      <Card title="Total Invoices" value={numberOfInvoices} type="invoices" />
      <Card
        title="Total Customers"
        value={numberOfCustomers}
        type="customers"
      />
    </>
  );
}
```

