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
    - Set on a page-by-page basis. Prefer static or server-side rendering. Faster.
  - (Pre-rendering approaches:)
    - Render HTML at compile time. Served immediately. JS download reduced.
      - Static-site generation: Generate page at build time.
        - Generate page's HTML at build time. Prerender's every page.
        - ASK "Can I pre-render this page ahead of a user's request."
          - Marketing pages. Blog posts.
          - Renders within JS. Better performance and SEO.
          - CDN cacheable.
          - Fetch data during build. Not hard-coded.
      - Incremental static regeneration: Generate pages on-demand.
        - Data changes too often. Dataset too large.
        - Render on demand. Then cache.
          - Site is too large to pre-render.
          - Many dynamic pages.
          - products/1 => Generate. Cache. => Return page.
          - products/1 => returned cached record.
          - Specify how many seconds each page should be cached.
            ```javascript
              export async function getStaticProps() {
                const r = await fetch("http://localhost:3000/api/products);
                if (!r.ok) {
                  throw new Error("Fetch failure.");
                }
                return {
                  props: {
                    await: r.json(),
                  },
                revalidate: 10,
                }
              }
            ```
  - Server-side rendering:
    - Fetch function is declared in the same file as the page component.
    - A page is static unless it uses getServerSideProps() or getInitialProps().
      - Static is the default.
  - Client-side rendering:
    - Common. Simple. Download JS. Parse JS. Render HTML.
      - Slowness? Connections. Databases. APIs. Fetch waterfalls.
  - SUMMARY:
    - Static site generation: Same content for all users. 
      - Page rarely changes. Can redeploy when it does. (BLOG)
    - Incremental static regeneration: Too many pages. Frequent page changes. (E-COMMERCE)
    - Server side rendering: Different content for each user. SEO. 
      - Content shown on initial page load. (USER ACCOUNT PAGE)
    - Client side rendering: Portion of unique user content.
      - Lazy load a portion. Performance. Don't care about SEO. (USER-SPECIFIC)

- AUTHENTICATION ECOSYSTEM:
  - Low-level versus full-featured:
    - iron-session: low-level. encrypted and stateless session.
    - next-auth: Full-featured. Built-in providers: (GOOGLE) JWT/JWE.
  - Static and server-rendered:
    - Two options:
      - Fetch user data server-side.
      - Server-render a loading state, then fetch the user client-side.
        ```javascript
          import withSession from "../lib/session";
          import Layout from "../components/Layout";
          export const getServerSideProps = withSession(async function ({ req, res}) {
            const { user } = req.session;
            if (!user) {
              return {
                redirect: {
                  destination: "/login",
                  permanent: false,
                },
              }:
            }
            return {
              props: { user },
            }
          });
        ```

- BUILDING & DEPLOYING NEXT.JS:
  - Fast builds:
    - SWC. 17x faster builds. Enabled by default. 3x faster fast refresh.
    - NOTE: Babel: More configurable. Richer ecosystem.
  - Optimized images/fonts:
    - Auto optimized. Lazy loaded. Correctly-sized images.
    - Visual stability. COntent does not just around.
    - On-demand resizing. Preserves bandwidth.
  - Incremental adoption:
    - Deploy Next.js alongside your existing app. (Supply the base URL.)
    - Primamry app with rewrites.
    - "If no valid Next.js route is found, proxy to my old app."
      ```javascript
        module.exports = {
          async rewrites() {
            return {
              fallback: [
                {
                  source: '/:path*',
                  destination: `https://proxy.example.com/:path*`,
                },
              ],
            }
          },
        }
      ```
  - Scaling across teams:
    - Assure each app has unique routes.
    - Configure rewrites/use an HTTP proxy.
    - Deploy apps.
  - Hosting options: Sponsered by Vercel.
    - Static: Anywhere.
    - Server-side generated: Vercel. AWS. Azure. SOmething that supports Node.js.
    - Edge: Vercel. Netlify. Layer0.
      - New idea. Serve users worldwide. 31% slower tahn the speed of light.
        - CDN versus. CDN geographically nearby. Edge? Same. But can also serve up code.

- TRADEOFFS & ALTERNATIVES:
  - React. Big ecosystem. Tradeoffs:
    - Integrated routing: Using an alternative router may confuse.
    - Highly opinionated: Can't pick and choose your own tech stack.
    - Host and deploy: Few edge hosting sites. Lose some features if hosted serverless.
    - Server-side support: Can be confusing. Need to chach "Am I in the browser yet?"
    - Microfrontends: Can't decompose more granularly.
    - Edge support: Upsupported Node.js APIs.
  - Alternatives:
    - create-react-app:
      - Faster startup. Faster builds. Static site generation or server-side rendering.
      - Strong opinions. More active development. File-based routing.
      - Auto code splitting by route. Auto link prefetch.
      - Automatically rendered to avoid layout shift. Optimizes images. Built-in head component.
      - Eliminates usused CSS. Built-in scroll restoration. Built-in API routes. Middleware.
    - Vite:
      - Vite offers plug-ins. And faster builds and hot reloads.
    - Remix:
      - Consider Remix if: Web-standard APIs. Gracefully fallback if JS hasn't loaded.
      - App has many forms. Associated routes with data dependencies.
    - Gatsby:
      - Consider Consider if: GraphQL. Plug-in ecosystem. Build faster per templates.
    - RedwoodJS:
      - Consider RedwoodJS if: Startup? GraphQL. Prisma. Storybook. Move faster.
        - No static generation. No server-side rendering.
    - Hydrogen:
      - E-commerce site. Shopify storefront.