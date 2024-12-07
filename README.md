## Next.js 12: The Big Picture by Cory House

- OVERVIEW:
  - Next.js is a popular, feature-rich, and mature React framework. This course will teach you why Next.js is useful, review its key features, and compare it to alternatives, so you can determine if it's a good fit for you.

- WHY NEXT.JS?
  - Builds. Bundling. Caching. Routing. Image optimization. Data fetching.
  - Bundle splitting. Statuc generation. Server rendering. Dev environment.
    ```javascript
      npx create-next-app my-app --ts
    ```
  - Creating a new next.js app and running it locally takes only about a minute.
    - Built and maintained by Vercel. $300 million in funding to date.
  - Key dependencies:
    - React: Components.
    - Node: APIs and tooling.
    - SWC & Babel: Compiling.
    - Webpack: Bundling.
      - Two hunderd dependencies. Next.js manages it all. 
      - And these dependencies are configured behind the scenes in the next package.
      - Netflix. Ticket Master. Door Dash. October 2016. Example projects.

- ROUTING FEATURES:
  - Built-in Routing:
    - File-based routes:
      - Each file created under the pages folder decalres a corresponding route.
      - index.js is the root route. about.js renders at /about.
      - Square brackets in the file name specificy a URL parameter:
        - pages/products/[id].js => /products/1
      -app.js: base page. header, site-wide styles.
      - Each page is automatically lazy-loaded. Pages are intelligently loaded in advance.
    - Dynamic routes:
      - API routes: api/products. And decalre a function:
        ```javascript
          export default function handler(req, res) : void {
            const products = [
              { id: 1, name: "product 1" },
              { id: 2, name: "product 2" },
            ];
            res.status(200).json(products);
          }
        ```
    - Automatic lazy loading.
    - Internationalization support.
      - Declare locales in next.config.js.
      - Subpath routing:
        - /blog. /fr/blog. nl-nl/blog.
      - Domain routing:
        - example.com. example.fr. example.nl
    - Redirects.
      - next.config.js:
        ```javascript
          module.exports = {
            async redirects() {
              return [
                {
                  source: "/about",
                  destination: "/",
                  permanent: true,
                },
              ];
            },
          };
        ```
    - Middleware. (Cross-cutting concerns.)
      - Beta. Auth. Redirects. Feature flags. A/B tests.
      - pages/_middleware.ts:
        ```javascript
          import type { NextFetchEvent, NextRequest } from 'next/server'
          export function middleware(req: NextRequest, ev: NextFetchEvent) {
            return new Response("Hello world.");
          }
        ```
      - Middleware can be scoped by placing the associated file within the directory with the revelant page(s).

- RENDERING APPROACHES:
  - Static-site generation:
  - Server-side rendering:
  - Incremental static regeeration:
  - Client-side rendering:

- AUTHENTICATION ECOSYSTEM:

- BUILDING & DEPLOYING NEXT.JS:

- TRADEOFFS & ALTERNATIVES: