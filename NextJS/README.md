# Next.js (for obsidian menu)
<h2 style="background-color: red; color: white">I WILL NOT COVER NEXTJS FOR BACKEND</h2>

## create project

```bash
npx create-next-app@latest --typescript
# or
yarn create next-app --typescript
# or
pnpm create next-app --typescript
```

---

# folder structor
```
├── next.config.js
├── next-env.d.ts
├── package.json
├── pages
│   ├── api
│   │   └── hello.ts
│   ├── _app.tsx
│   └── index.tsx
├── public
│   ├── favicon.ico
│   └── vercel.svg
├── README.md
├── styles
│   ├── globals.css
│   └── Home.module.css
├── tsconfig.json
└── yarn.lock
```

---
# Pages 

`pages/index.tsx`
```tsx
import type { NextPage } from 'next'
import Link from 'next/link'

const Home: NextPage = () => {
  return (
    <div className="w-[960px] mx-auto">
      <div>Home Page</div>
      <Link href="/about">
        <a className="block py-2 px-4 border border-red-100 rounded-md">go to about</a>
      </Link>
    </div>
  )
}

export default Home

```

`pages/about.tsx`
```tsx
import { NextPage } from "next";
import { useRouter } from "next/router";

interface IAboutProps {
}

const About: NextPage<IAboutProps> = (props) => {
    const router = useRouter()
    return (
        <div className="w-[960px] mx-auto">
            <div>About</div>
            <button className="py-2 px-4 border border-blue-400 rounded-md"
                onClick={() => router.push("/")}
            >go to homepage</button>
        </div>
    );
};

export default About;

```

---
# Path params

`pages/[name].tsx`
```tsx
import { NextPage } from 'next';
import { useRouter } from 'next/router';

interface INameProps {
}

const Name: NextPage<INameProps> = (props) => {
    const router = useRouter()

    return (
        <>
            <div>Path name: {router.query.name}</div>
        </>
    );
};

export default Name;

```

---
# Nested params

`pages/[id]/[name].tsx`
```tsx
import { NextPage } from 'next';
import { useRouter } from 'next/router';

interface INameProps {
}

const Name: NextPage<INameProps> = (props) => {
    const router = useRouter()
    console.log(router.query)
    return (
        <>
            <div>Path name parent {router.query.id}</div>
            <div>Path name child: {router.query.name}</div>
        </>
    );
};

export default Name;

```

---

# Query param from URL
url: `http://localhost:3000/someid/customname?abc=123`

then result from `router.query` is 
```ts
{
	abc: "123",
	id: "someid",
	name: "customname"
}
```

---

What if we have another page inside folder [id] along with [name].tsx
what page will render

```
├── pages
    │   ├── about.tsx
    │   ├── api
    │   │   └── hello.ts
    │   ├── _app.tsx
    │   ├── [id]
    │   │   ├── my-page.tsx
    │   │   └── [name].tsx
    │   └── index.tsx
```

the answer is 
if we enter url: `/someid/my-page`
the `my-page.tsx` will render to the browser 
if we enter other url: `/someid/another-page-inside-id-param`
the `[name].tsx` will render to the browser

---

# 404 page

nextjs provided the 404 page for us
if you don't liket that design you can provide your 404 page by create file 
`404.tsx` inside `pages` folder

`pages/404.tsx`
```tsx
import { NextPage } from 'next';

interface INotFoundProps {
}

const NotFound: NextPage<INotFoundProps> = (props) => {
    return (
        <div>Not Found</div>
    );
};

export default NotFound;

```

---
# Layout

## Setup typescript for ***NextPageWithLayout***


`pages/_app.tsx`
```tsx
import '../styles/globals.css'
import type { AppProps } from 'next/app'
import { ReactElement, ReactNode } from 'react'
import { NextPage } from 'next'

export type NextPageWithLayout<P = {}, IP = P> = NextPage<P, IP> & {
  getLayout?: (page: ReactElement) => ReactNode
}

export type AppPropsWithLayout = AppProps & {
  Component: NextPageWithLayout
}

function MyApp({ Component, pageProps }: AppPropsWithLayout) {
  const getLayout = Component.getLayout ?? (page => page)
  return getLayout(<Component {...pageProps} />)
}

export default MyApp

```

---
## Create **_Layout component_**

`layout/layout.tsx`
```tsx
import { PropsWithChildren } from 'react';
import { NextPageWithLayout } from '../pages/_app';

interface ILayoutProps {
}

const Layout: NextPageWithLayout<PropsWithChildren<ILayoutProps>> = (props) => {
    return (
        <div className="p-4">
            <nav className="text-4xl bg-green-200">Logo</nav>
            <section className="w-full">
                <div className="grid overflow-hidden grid-cols-12 grid-rows-2 gap-2 py-8">
                    <div className="box row-span-2 col-start-1 col-span-2  bg-red-200">
                        <div>Menu1</div>
                        <div>Menu2</div>
                    </div>
                    <div className="box row-span-2 col-start-3 col-span-10 bg-blue-200">
                        {props.children}
                    </div>
                </div>
            </section>
            <footer className="bg-pink-200">Footer</footer>
        </div>
    );
};

export default Layout;

```

---
## use layout in pages

`pages/index.tsx`
```tsx
import type { NextPage } from 'next'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'

const Home: NextPageWithLayout = () => { // <--- change type of Home Component
  return (
    <div className="w-[960px] mx-auto">
      <div>Home Page</div>
      <Link href="/about">
        <a className="block py-2 px-4 border border-red-100 rounded-md">go to about</a>
      </Link>
    </div>
  )
}

Home.getLayout = (page) => { // <---- add this
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export default Home

```

`pages/about.tsx`
```tsx
import { useRouter } from "next/router";
import Layout from "../layout/layout";
import { NextPageWithLayout } from "./_app";

interface IAboutProps {
}

const About: NextPageWithLayout<IAboutProps> = (props) => {
    const router = useRouter()
    return (
        <div className="w-[960px] mx-auto">
            <div>About</div>
            <button className="py-2 px-4 border border-blue-400 rounded-md"
                onClick={() => router.push("/")}
            >go to homepage</button>
        </div>
    );
};

About.getLayout = (page) => {
    return (
        <Layout>
            {page}
        </Layout>
    )
}

export default About;

```

---
# Static side generate
-   `getStaticProps` : The page will be pre-rendered at build time
-   `getServerSideProps`:  The page will be pre-rendered at runtime
-   `getStaticPaths` : This function generates a list of pages that will be pre-rendered at build time

## getStaticProps() [SSG Static site generation]

this is a function that is called in server-side then store in Page Props 
client-side didn't know where is the source of those props

which page use **getStaticProps()**, NextJs will pre-rendered that page at **build time**

**use in Page only**

`pages/index.tsx`
```tsx
import type { GetStaticProps, InferGetStaticPropsType, NextPage } from 'next'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'

const Home: NextPageWithLayout<InferGetStaticPropsType<typeof getStaticProps>> = (props) => {
  return (
    <div className="w-[960px] mx-auto">
      <div>Home Page</div>
      <div className="flex flex-row gap-2 my-4">
        {
          props.footballTeams.map(f => (
            <div key={f.name} className="flex flex-col p-2 border border-blue-600">
              <div>{f.name}</div>
              <div>{f.coach}</div>
            </div>
          ))
        }
      </div>
      <Link href="/about">
        <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
      </Link>
    </div>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

interface IFootballTeam {
  name: string
  coach: string
}

export const getStaticProps: GetStaticProps<{ footballTeams: IFootballTeam[] }> = async (context) => {
  const result = await fetch("http://host.docker.internal:3001")
  const json: IFootballTeam[] = await result.json()
  return {
    props: {
      footballTeams: json
    }
  }
}

export default Home

```


when you run **build** command
you will see something like this

```
Route (pages)                              Size     First Load JS
┌ ● / (344 ms)                             2.72 kB        80.5 kB
├   /_app                                  0 B            77.7 kB
├ ○ /[id]/[name] (358 ms)                  354 B          78.1 kB
├ ○ /[id]/my-page                          274 B            78 kB
├ ○ /404                                   255 B            78 kB
├ ○ /about                                 611 B          78.4 kB
└ λ /api/hello                             0 B            77.7 kB
+ First Load JS shared by all              79.6 kB
  ├ chunks/framework-7751730b10fa0f74.js   45.5 kB
  ├ chunks/main-e7a7892cb0edc024.js        31 kB
  ├ chunks/pages/_app-9b63f6c1a3c7b9d2.js  528 B
  ├ chunks/webpack-8fa1640cc84ba8fe.js     750 B
  └ css/4fa0b4e37064a691.css               1.83 kB

λ  (Server)  server-side renders at runtime (uses getInitialProps or getServerSideProps)
○  (Static)  automatically rendered as static HTML (uses no initial props)
●  (SSG)     automatically generated as static HTML + JSON (uses getStaticProps)
```

the `● /`  is then `index.tsx` page that we use getStaticProps so it is SSG (Static side generation)

in this sutiation the `index.tsx` page will render at build time so if the football api changed value the index page will not update follow the api because page could not know when the football api value changed

---

## ISG (Incremental static generation)

for the above problem we can change from SSG to ISG (Incremental staic generate)
where it is the technique for create the pages that use **getStaticProps()** inside page.tsx in interval (this option you need to run NextJS in nodeJS for production)

_example_
`pages/index.tsx`
```tsx
import type { GetStaticProps, InferGetStaticPropsType, NextPage } from 'next'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'

const Home: NextPageWithLayout<InferGetStaticPropsType<typeof getStaticProps>> = (props) => {
  return (
    <div className="w-[960px] mx-auto">
      <div>Home Page</div>
      <div className="flex flex-row gap-2 my-4">
        {
          props.footballTeams.map(f => (
            <div key={f.name} className="flex flex-col p-2 border border-blue-600">
              <div>{f.name}</div>
              <div>{f.coach}</div>
            </div>
          ))
        }
      </div>
      <Link href="/about">
        <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
      </Link>
    </div>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

interface IFootballTeam {
  name: string
  coach: string
}
export const getStaticProps: GetStaticProps<{ footballTeams: IFootballTeam[] }> = async (context) => {
  const result = await fetch("http://host.docker.internal:3001")
  const json: IFootballTeam[] = await result.json()
  return {
    props: {
      footballTeams: json
    },
    revalidate: 5 // <--- in second
  }

}
export default Home

```

```
Route (pages)                              Size     First Load JS
┌ ● / (ISR: 60 Seconds) (363 ms)           2.72 kB        80.5 kB
├   /_app                                  0 B            77.7 kB
├ ○ /[id]/[name]                           354 B          78.1 kB
├ ○ /[id]/my-page                          274 B            78 kB
├ ○ /404                                   255 B            78 kB
├ ○ /about                                 611 B          78.4 kB
└ λ /api/hello                             0 B            77.7 kB
+ First Load JS shared by all              79.6 kB
  ├ chunks/framework-7751730b10fa0f74.js   45.5 kB
  ├ chunks/main-e7a7892cb0edc024.js        31 kB
  ├ chunks/pages/_app-9b63f6c1a3c7b9d2.js  528 B
  ├ chunks/webpack-8fa1640cc84ba8fe.js     750 B
  └ css/4fa0b4e37064a691.css               1.83 kB

λ  (Server)  server-side renders at runtime (uses getInitialProps or getServerSideProps)
○  (Static)  automatically rendered as static HTML (uses no initial props)
●  (SSG)     automatically generated as static HTML + JSON (uses getStaticProps)
   (ISR)     incremental static regeneration (uses revalidate in getStaticProps)
```

---
## getStaticProps() options

if there is error with our api or get not found from api how can we deal with this situation

### notFound option
we can use **notFound** option, then client will redirect to **404.tsx** page automaically

`pages/index.tsx`
```tsx
import type { GetStaticProps, InferGetStaticPropsType, NextPage } from 'next'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'

const Home: NextPageWithLayout<InferGetStaticPropsType<typeof getStaticProps>> = (props) => {
  return (
    <div className="w-[960px] mx-auto">
      <div>Home Page</div>
      <div className="flex flex-row gap-2 my-4">
        {
          props.footballTeams.map(f => (
            <div key={f.name} className="flex flex-col p-2 border border-blue-600">
              <div>{f.name}</div>
              <div>{f.coach}</div>
            </div>
          ))
        }
      </div>
      <Link href="/about">
        <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
      </Link>
    </div>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

interface IFootballTeam {
  name: string
  coach: string
}
export const getStaticProps: GetStaticProps<{ footballTeams: IFootballTeam[] }> = async (context) => {
  const response = await fetch("http://host.docker.internal:3001")
  console.log("status", response.status)
  if (response.status === 404) { // if status is 404
    return {
      notFound: true, // <---- notFound options

    }
  }
  const json: IFootballTeam[] = await response.json()
  return {
    props: {
      footballTeams: json
    },
    revalidate: 60 // <--- in second
  }
}
export default Home

```


### redirect option
`pages/index.tsx`
```tsx
import type { GetStaticProps, InferGetStaticPropsType, NextPage } from 'next'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'

const Home: NextPageWithLayout<InferGetStaticPropsType<typeof getStaticProps>> = (props) => {
  return (
    <div className="w-[960px] mx-auto">
      <div>Home Page</div>
      <div className="flex flex-row gap-2 my-4">
        {
          props.footballTeams.map(f => (
            <div key={f.name} className="flex flex-col p-2 border border-blue-600">
              <div>{f.name}</div>
              <div>{f.coach}</div>
            </div>
          ))
        }
      </div>
      <Link href="/about">
        <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
      </Link>
    </div>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

interface IFootballTeam {
  name: string
  coach: string
}
export const getStaticProps: GetStaticProps<{ footballTeams: IFootballTeam[] }> = async (context) => {
  const response = await fetch("http://host.docker.internal:3001")
  console.log("status", response.status)
  if (response.status === 404) {
    return {
      notFound: true,

    }
  }
  if (response.status !== 200) {
    return {
      redirect: {
        statusCode: 301,
        destination: "/about"
      }
    }
  }
  const json: IFootballTeam[] = await response.json()
  return {
    props: {
      footballTeams: json
    },
    revalidate: 60 // <--- in second
  }
}
export default Home

```

## getStaticProps() Context

in case the page is a dynamic route so how that we know 

`pages/[id]/[name].tsx`
```tsx
import { GetStaticProps, InferGetStaticPropsType } from 'next';
import { useRouter } from 'next/router';
import { NextPageWithLayout } from '../_app';

interface INameProps {
    name: string,
}

const Name: NextPageWithLayout<InferGetStaticPropsType<typeof getStaticProps>> = (props) => {
    const router = useRouter()
    console.log(router.query)
    return (
        <>
            <div>Path name parent {router.query.id}</div>
            <div>Path name child: {props.name}</div>
        </>
    );
};


export const getStaticProps: GetStaticProps<INameProps, { name: string, id: string }> = async (context) => {
    const { params } = context
    if (!params) {
        return {
            notFound: true
        }
    }

    return {
        props: {
            name: params.name,
        }
    }
}

export default Name;
```

in the example above we can use **path params** in server side then you may use those **path params** to fetch something from backend side then generate static page to client

### You will get an ERROR

from above example  you will get error if you try to open `[id]/[name]` page (**dynamic path**)

why 
because **getStaticProps** will generate page at build time but for the `[id]/[name]`  pages the server could not know what is the `[id]` and `[name]` so server could not generate the page at build time that why you get error

to solve this problem we have to use **getStaticPaths** along with **getStaticProps** for the **dynamic path**

`pages/[id]/[name].tsx`
```tsx
import { GetStaticPaths, GetStaticProps, InferGetStaticPropsType } from 'next';
import { useRouter } from 'next/router';
import { NextPageWithLayout } from '../_app';

interface INameProps {
    name: string,
}

const Name: NextPageWithLayout<InferGetStaticPropsType<typeof getStaticProps>> = (props) => {
    const router = useRouter()
    console.log(router.query)
    return (
        <>
            <div>Path name parent {router.query.id}</div>
            <div>Path name child: {props.name}</div>
        </>
    );
};

export const getStaticPaths: GetStaticPaths<{ id: string, name: string }> = async () => {
	// this is an example only 
	// normally all of the params will get from the backend data
    return {
        paths: [
            {
                params: {
                    id: "id-2",
                    name: "name-1"
                }
            },
            {
                params: {
                    id: "id-2",
                    name: "name-2"
                }
            }
        ],
        fallback: "blocking" // <--- rest of the pages is not generated in advance will generate when a user called
    }
}

export const getStaticProps: GetStaticProps<INameProps, { name: string, id: string }> = async (context) => {
    const { params } = context
    if (!params) {
        return {
            notFound: true
        }
    }

    return {
        props: {
            name: params.name,
        }
    }
}

export default Name;

```


```
Route (pages)                              Size     First Load JS
┌ ● / (ISR: 5 Seconds) (312 ms)            2.72 kB        80.5 kB
├   /_app                                  0 B            77.7 kB
├ ● /[id]/[name] (302 ms)                  375 B          78.1 kB
├   ├ /id-2/name-1
├   └ /id-2/name-2
├ ○ /[id]/my-page                          274 B            78 kB
├ ○ /404                                   255 B            78 kB
├ ○ /about                                 611 B          78.4 kB
└ λ /api/hello                             0 B            77.7 kB
+ First Load JS shared by all              79.6 kB
  ├ chunks/framework-7751730b10fa0f74.js   45.5 kB
  ├ chunks/main-e7a7892cb0edc024.js        31 kB
  ├ chunks/pages/_app-9b63f6c1a3c7b9d2.js  528 B
  ├ chunks/webpack-8fa1640cc84ba8fe.js     750 B
  └ css/4fa0b4e37064a691.css               1.83 kB

λ  (Server)  server-side renders at runtime (uses getInitialProps or getServerSideProps)
○  (Static)  automatically rendered as static HTML (uses no initial props)
●  (SSG)     automatically generated as static HTML + JSON (uses getStaticProps)
   (ISR)     incremental static regeneration (uses revalidate in getStaticProps)
```

we use **getStaticPaths()**
inside we provide which paths can be generated pages in advanced 
as you see above page `/id-2/name-1` and `/id-2/name-2` is generated in advanced
so when the user enters both paths in the browser then the user will get the generated page immediately

---


# getServerSideProps() [SSR Server side rendering]

using **getServerSideProps()** is same as **getStaticProps()**
the different is **getServerSideProps()** will execute on every incoming request 
there is no any pre-render page

`pages/index.tsx`
```tsx
import type { GetServerSideProps, GetStaticProps, InferGetServerSidePropsType, InferGetStaticPropsType, NextPage } from 'next'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'

const Home: NextPageWithLayout<InferGetServerSidePropsType<typeof getServerSideProps>> = (props) => {
  return (
    <div className="w-[960px] mx-auto">
      <div>Home Page</div>
      <div className="flex flex-row gap-2 my-4">
        {
          props.footballTeams.map(f => (
            <div key={f.name} className="flex flex-col p-2 border border-blue-600">
              <div>{f.name}</div>
              <div>{f.coach}</div>
            </div>
          ))
        }
      </div>
      <Link href="/about">
        <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
      </Link>
    </div>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

interface IFootballTeam {
  name: string
  coach: string
}

export const getServerSideProps: GetServerSideProps<{ footballTeams: IFootballTeam[] }> = async (context) => {
  const response = await fetch("http://host.docker.internal:3001")
  console.log("status", response.status)
  if (response.status === 404) {
    return {
      notFound: true,

    }
  }
  if (response.status !== 200) {
    return {
      redirect: {
        statusCode: 301,
        destination: "/about"
      }
    }
  }
  const json: IFootballTeam[] = await response.json()
  return {
    props: {
      footballTeams: json
    },
  }
}

export default Home
```
---
# Optimize Next App

## Head Content

in every page can add \<head> tag for the special custom \<head> like normal HTML

`pages/index.tsx`
```tsx
import type { GetServerSideProps, GetStaticProps, InferGetServerSidePropsType, InferGetStaticPropsType, NextPage } from 'next'
import Head from 'next/head'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'

const Home: NextPageWithLayout<InferGetServerSidePropsType<typeof getServerSideProps>> = (props) => {
  return (
    <>
      <Head> // <----- import from "next/head"
        <title>Next App Index Page</title>
        <meta
          name="description"
          content="this is a great Next app"
        />
      </Head>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <div className="flex flex-row gap-2 my-4">
          {
            props.footballTeams.map(f => (
              <div key={f.name} className="flex flex-col p-2 border border-blue-600">
                <div>{f.name}</div>
                <div>{f.coach}</div>
              </div>
            ))
          }
        </div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
      </div>
    </>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export default Home
```

---
## Custom Document

the custom document allows you to custom document as you need like a normal index.html file

but in nthe ormal app you might not need to create a custom document please try to use **_app.tsx_**

`pages/_document.tsx`
```tsx
import Document, { Html, Head, Main, NextScript } from "next/document";
export default class MyDocument extends Document {
    render() {
        return (
            <Html>
                <Head>
                </Head>
                <body>
                    <div>
                        Document
                        <Main></Main>
                        <NextScript />
                    </div>
                </body>
            </Html>
        )
    }
}


```

---
# Next Image

next.js is provide build-in image optimizer that make you app better performance

## use local image
to use local image 
that very easy

`pages/index.tsx`
```tsx
import type { GetServerSideProps, InferGetServerSidePropsType } from 'next'
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'
import Image from 'next/image'
import Haaland from "../public/haaland.png" // <--- image image like a library

const Home: NextPageWithLayout = (props) => {
  return (
    <>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
        <Image src={Haaland} alt="haaland" ></Image> // <--- use the imported image in src
      </div>
    </>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export default Home
```


## use remote image

remote image mean the source of image if not same url of website

but this method is needed a bit setting

you have to set source of images eg. hostname, port, protocol, and paths 
example is below

assume the one of image from source: `https://t2.gstatic.com/licensed-image?q=tbn:ANd9GcT79RpX9C733QHr1jxp_-wbJGlZF0IhswMPQb6ciZPsDFDB8huCSppbvtvJt9rm3Iqvmm3GNPPC4Qu-N5E`
```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "t2.gstatic.com",
        port: "",
        pathname: "/**"
      }
    ]
  }
}

module.exports = nextConfig

```

when using image you need to set **width** and **height**


`pages/index.tsx`
```tsx
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'
import Image from 'next/image'

const Home: NextPageWithLayout = (props) => {
  return (
    <>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
        <Image src="https://t2.gstatic.com/licensed-image?q=tbn:ANd9GcT79RpX9C733QHr1jxp_-wbJGlZF0IhswMPQb6ciZPsDFDB8huCSppbvtvJt9rm3Iqvmm3GNPPC4Qu-N5E" alt="haaland"
          width={300} // <--- required
          height={300} // <---- required
        ></Image>
      </div>
    </>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export default Home
```


---

# Deployment

## Run on nodejs server
very easy

***let's build next app***
```bash
npm run build
```

***run next app***
```bash
npm run start
```




## build for static site

if you prefered this method that mean you want to let NextJS generate html, css and js file and server them in another server like Nginx or Apache

this method you need to setup `next.config.js`
because next image optimization cannot do without nodejs

`next.config.js`
```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
  images: {
    unoptimized: true, // <---- this options
  }
}

module.exports = nextConfig

```

there are some scripts in package.json

`package.json`
```json
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "export": "next export"
}
```

_**let's build**_
```bash
npm run build
```

_**export to static files**_
```bash
npm run export
```

---

# Environment variable

## Server Env
can use env for server like normal nodejs app 

`.env.local`
```env
DB_USER=admin
```

and use in server only

`index.tsx`
```tsx
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'
import Image from 'next/image'
import haalandImage from "../public/haaland.png"
import { useEffect, useState } from 'react'
import { GetServerSideProps } from 'next'

const Home: NextPageWithLayout = (props) => {
  return (
    <>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
        <Image src={haalandImage} alt="haaland"></Image>
      </div>
    </>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export const getServerSideProps: GetServerSideProps = async (context) => {
  console.log(process.env.DB_USER) // <---- env
  return {
    props: {}
  }
}

export default Home
```

---
## Exposing Env to the browser

the env name should prefix with `NEXT_PUBLIC`

`.env.local`
```env
DB_USER=admin

NEXT_PUBLIC_API_URL=http://localhost:3001
```

`pages/index.tsx`
```tsx
const Home: NextPageWithLayout = (props) => {
  console.log("browser env")
  console.log(process.env.NEXT_PUBLIC_API_URL) // <---- env

  return (
    <>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
        <Image src={haalandImage} alt="haaland"></Image>
      </div>
    </>
  )
}
```

---
## env files

you can use other env file for the different server

`.env.development` -> for the development server
`.env.production` -> for production server

---
## runtime config

please check [nextjs.org docs](https://nextjs.org/docs/api-reference/next.config.js/runtime-configuration)

`next.config.js`
```js
module.exports = {
  serverRuntimeConfig: {
    // Will only be available on the server side
    mySecret: 'secret',
    secondSecret: process.env.SECOND_SECRET, // Pass through env variables
  },
  publicRuntimeConfig: {
    // Will be available on both server and client
    staticFolder: '/static',
  },
}
```

and use like this
```jsx
import getConfig from 'next/config'
import Image from 'next/image'

// Only holds serverRuntimeConfig and publicRuntimeConfig
const { serverRuntimeConfig, publicRuntimeConfig } = getConfig()
// Will only be available on the server-side
console.log(serverRuntimeConfig.mySecret)
// Will be available on both server-side and client-side
console.log(publicRuntimeConfig.staticFolder)

function MyImage() {
  return (
    <div>
      <Image
        src={`${publicRuntimeConfig.staticFolder}/logo.png`}
        alt="logo"
        layout="fill"
      />
    </div>
  )
}

export default MyImage
```

---

# Build indicator
`next.config.js`
```js
module.exports = {
  devIndicators: {
    buildActivityPosition: 'bottom-right',
	buildActivity: true,
  },
}
```

---

# Fetching Data

TLDR;
- React-Query from **TanStack**
- SWR from Vercel

**React-Query is better**

---
## TanStack React-Query v4

what can react-query do ?
-   Caching... (possibly the hardest thing to do in programming)
-   Deduping multiple requests for the same data into a single request
-   Updating "out of date" data in the background
-   Knowing when data is "out of date"
-   Reflecting updates to data as quickly as possible
-   Performance optimizations like pagination and lazy loading data
-   Managing memory and garbage collection of server state
-   Memoizing query results with structural sharing

React Query is hands down one of the _best_ libraries for managing server state. It works amazingly well **out-of-the-box, with zero-config, and can be customized** to your liking as your application grows.

---
## npm package
```bash
npm uninstall react-query
npm install @tanstack/react-query
npm install @tanstack/react-query-devtools
```

## setup
`_app.tsx`
```tsx
import '../styles/globals.css'
import type { AppProps } from 'next/app'
import { ReactElement, ReactNode } from 'react'
import { NextPage } from 'next'
import { QueryClientProvider, QueryClient } from "@tanstack/react-query"
import { ReactQueryDevtools } from "@tanstack/react-query-devtools"

export type NextPageWithLayout<P = {}, IP = P> = NextPage<P, IP> & {
  getLayout?: (page: ReactElement) => ReactNode
}

export type AppPropsWithLayout = AppProps & {
  Component: NextPageWithLayout
}

const queryClient = new QueryClient() // <--- client

function MyApp({ Component, pageProps }: AppPropsWithLayout) {
  const getLayout = Component.getLayout ?? (page => page)

  return getLayout(
    <>
      <QueryClientProvider client={queryClient}> // <--- use as HOC
        <Component {...pageProps} />
        <ReactQueryDevtools />
      </QueryClientProvider>
    </>
  )
}

export default MyApp

```

---
## Create fetch service
`services/football.service.ts`
```ts
interface IFootball {
    name:string
    coach:string
}
const url = "http://localhost:3001"
export async function getFootballByTeamName(teamName:string){
    const response = await fetch(url + `/${teamName}`)
    return response.json() as Promise<IFootball>
}
```

---

## Use React query for fetching

`pages/index.ts`
```tsx
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'
import Image from 'next/image'
import haalandImage from "../public/haaland.png"
import { useEffect, useState } from 'react'
import { GetServerSideProps } from 'next'
import { useQuery } from "@tanstack/react-query"
import { getFootballByTeamName } from '../services/football.service'

const Home: NextPageWithLayout = (props) => {

  const { data, isLoading, error } = useQuery(["getFootball by team name"], () => {
    return getFootballByTeamName("TOT")
  })

  useEffect(() => {
    (async () => {
      console.log("data", data)
      console.log("is loading", isLoading)
    })()
  }, [data, isLoading])

  return (
    <>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
      </div>
    </>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export const getServerSideProps: GetServerSideProps = async (context) => {

  console.log("server env", process.env.DB_USER)
  return {
    props: {}
  }
}

export default Home
```

## Mutation

when you POST something to API the data should be update
we can use **useMutation** to let react-query re-fetch the data in the specific key

`pages/index.tsx`
```tsx
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'
import { useEffect } from 'react'
import { GetServerSideProps } from 'next'
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { createFootballTeam, IFootball, getAll } from '../services/football.service'

const Home: NextPageWithLayout = (props) => {

  const queryClient = useQueryClient()
  const { data, isLoading, error } = useQuery(["get all"], () => {
    return getAll()
  })

  ////////// mutation ///////////
  const mutation = useMutation<IFootball, any, IFootball>((newData) => {
    console.log("new data", newData)
    return createFootballTeam({ name: newData.name, coach: newData.coach })
  }, {
    onSuccess(data, variable, context) {
      queryClient.invalidateQueries(["get all"])
    }
  })

  useEffect(() => {
    (async () => {
      console.log("data", data)
      console.log("is loading", isLoading)
    })()
  }, [data, isLoading])

  return (
    <>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
        <button onClick={() => mutation.mutate({ name: "Aston Villa", coach: "STVG" })}>Create Villa</button>
      </div>
    </>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export const getServerSideProps: GetServerSideProps = async (context) => {

  console.log("server env", process.env.DB_USER)
  return {
    props: {}
  }
}

export default Home
```

---

## pagination example

the service code is 

`services/rickandmorty.service.ts`
```ts
import axios from "axios"

const url = "https://rickandmortyapi.com/api/character?page="

interface Info {
    count: number;
    pages: number;
    next: string;
    prev?: any;
}

interface Origin {
    name: string;
    url: string;
}

interface Location {
    name: string;
    url: string;
}

interface Result {
    id: number;
    name: string;
    status: string;
    species: string;
    type: string;
    gender: string;
    origin: Origin;
    location: Location;
    image: string;
    episode: string[];
    url: string;
    created: Date;
}

export interface IRickAndMorty {
    info: Info;
    results: Result[];
}

export const GET_RICK_AND_MORTY_QUERY_KEY = "GET_RICK_AND_MORTY_QUERY_KEY"

export async function getRickAndMorty(page: number) {
    const urlWithPage = `${url}${page}`

    const res = await axios.get<IRickAndMorty>(urlWithPage)
    return res.data
}
```

and Page is
`pages/index.tsx`
```tsx
import Link from 'next/link'
import Layout from '../layout/layout'
import { NextPageWithLayout } from './_app'
import { useEffect, useState } from 'react'
import { GetServerSideProps } from 'next'
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { createFootballTeam, IFootball, getAll } from '../services/football.service'
import { getRickAndMorty, GET_RICK_AND_MORTY_QUERY_KEY } from '../services/rickandmorty.service'
import Image from 'next/image'

const Home: NextPageWithLayout = (props) => {

  const [page, setPage] = useState(1)
  const queryClient = useQueryClient()
  const { data, isLoading, error } = useQuery([GET_RICK_AND_MORTY_QUERY_KEY, page], ({ queryKey }) => {
    const [key, currentPage] = queryKey as [string, number]
    return getRickAndMorty(currentPage)
  })

  return (
    <>
      <div className="w-[960px] mx-auto">
        <div>Home Page</div>
        <Link href="/about">
          <a className="block py-2 px-4 border bg-purple-100 rounded-md">go to about</a>
        </Link>
        <button className="py-2 px-4 border bg-red-500" onClick={() => setPage((page) => page + 1)}>Next</button>
        <button className="py-2 px-4 border bg-pink-200" onClick={() => setPage((page) => page - 1)}>Prev</button>
        {
          data?.results.map((r) => (
            <div key={r.id}>
              <span>name: {r.name}</span>
              <Image src={r.image} alt="name" width={200} height={200} />
            </div>
          ))
        }

      </div>
    </>
  )
}

Home.getLayout = (page) => {
  return (
    <Layout>
      {page}
    </Layout>
  )
}

export const getServerSideProps: GetServerSideProps = async (context) => {

  console.log("server env", process.env.DB_USER)
  return {
    props: {}
  }
}

export default Home
```

---


