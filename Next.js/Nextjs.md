# Cheatsheet Next.js

- [Index](/Readme.md)
- [Doc](https://nextjs.org/)

## Installation

```
npx create-next-app@latest
```

## Routage dynamique

pages/posts/[id].js
```jsx
import { useRouter } from 'next/router';

export default function Post() {
  const router = useRouter();
  const { id } = router.query;

  return <p>Post: {id}</p>;
}

```
## fetch
### getStaticProps
```jsx
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return {
    props: {
      data,
    },
  };
}

export default function Page({ data }) {
  return <div>{data.someField}</div>;
}

```

### getServerSideProps
```jsx
export async function getServerSideProps(context) {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return {
    props: {
      data,
    },
  };
}

export default function Page({ data }) {
  return <div>{data.someField}</div>;
}

```

### getStaticPaths
```jsx
export async function getStaticPaths() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  const paths = posts.map((post) => ({
    params: { id: post.id.toString() },
  }));

  return { paths, fallback: false };
}

```

### Combiner getStaticPaths et getStaticProps

```jsx
export async function getStaticProps({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);
  const post = await res.json();

  return {
    props: {
      post,
    },
  };
}

export async function getStaticPaths() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  const paths = posts.map((post) => ({
    params: { id: post.id.toString() },
  }));

  return { paths, fallback: false };
}

export default function Post({ post }) {
  return <div>{post.title}</div>;
}

```

## Internationalisation
Configuration dans next.config.js
```jsx
module.exports = {
  i18n: {
    locales: ['en', 'fr', 'es'],
    defaultLocale: 'en',
  },
};

```

Utilisation dans les pages
```jsx
import { useRouter } from 'next/router';

export default function Home() {
  const { locale } = useRouter();

  return <div>Current locale: {locale}</div>;
}
