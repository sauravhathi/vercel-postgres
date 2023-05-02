# Vercel Postgres

Getting Started with Vercel Postgres and Next.js

## Prerequisites

Install the Vercel Postgres package
    
```bash
npm install @vercel/postgres

# or

yarn add @vercel/postgres

```

Install the Vercel CLI

```bash
npm install -g vercel

vercel login
```

## Quickstart

### Create a Postgres database

Navigate to the Project you'd like to add a Postgres database to. Adding the database at the project-level means that Vercel will automatically create the environment variables for you.

Select the **Storage** tab, then select the **Connect Database** button. Under the **Create New** tab, select **Postgres** and then the **Continue** button.

To create a new database, do the following in the dialog that opens:

1. Enter **pets_postgres_db** (or any other name you wish) under **Store Name**. The name can only contain alphanumeric letters, "_" and "-" and can't exceed 32 characters.
2. Select a region. We recommend choosing a region geographically close to your Edge and Serverless Function regions for reduced latency
3. Click **Create**

### Review what was created

Your empty database is created in the region specified.

Because you created the Postgres database in a project, we automatically created and added the following environment variables to the project for you. Later in this quickstart, we'll pull them locally so we can use them with the project.

- POSTGRES_URL
- POSTGRES_PRISMA_URL
- POSTGRES_URL_NON_POOLING
- POSTGRES_USER
- POSTGRES_HOST
- POSTGRES_PASSWORD
- POSTGRES_DATABASE

### Preparing your local project

When you created your Postgres database, your API URL and credentials were created as environment variables automatically. You'll need to pull down the latest environment variables to get your local project working with the Postgres database.

```bash
vercel env pull .env.development.local
```

### Alternatively, you can manually add the environment variables

![image](https://user-images.githubusercontent.com/61316762/235773472-4d75d5a7-9fbf-456e-b619-44d8074d93e9.png)

### Populate your database

We're going to add a function to your project through an API route. Add the following file and code:

`pages/api/pets.ts`
```bash
import { db } from '@vercel/postgres';
import { NextApiRequest, NextApiResponse } from 'next';
 
export default async function handler(
  request: NextApiRequest,
  response: NextApiResponse,
) {
  const client = await db.connect();
 
  try {
    await client.sql`CREATE TABLE Pets ( Name varchar(255), Owner varchar(255) );`;
    const names = ['Fiona', 'Lucy'];
    await client.sql`INSERT INTO Pets (Name, Owner) VALUES (${names[0]}, ${names[1]});`;
  } catch (error) {
    return response.status(500).json({ error });
  }
 
  const pets = await client.sql`SELECT * FROM Pets;`;
  return response.status(200).json({ pets });
}
```

While it might look we are just hardcoding variables into the query, sql is a function that translates your query into a native Postgres parametrized query to help prevent SQL injection.

### Run your application locally

Run your application locally and visit `/api/pets` to see your data output. The function intercepts requests to `/api/pets` and responds with the data from your Postgres database.

```bash
http://localhost:3000/api/pets
```

### Data
Browse the tables in this database and run SQL queries against the data.

![image](https://user-images.githubusercontent.com/61316762/235773601-6413e675-dabd-4bf3-84bd-11aa1deb7b50.png)
