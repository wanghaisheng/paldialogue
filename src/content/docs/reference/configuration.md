---
title: Configuration Reference
description: An overview of all the configuration options Starlight supports.
---

## Configure the `starlight` integration

Starlight is an integration built on top the [Astro](https://astro.build) web framework. You can configure your project inside the `astro.config.mjs` configuration file:

```js
// astro.config.mjs
import { defineConfig } from 'astro/config';
import starlight from '@astrojs/starlight';

export default defineConfig({
  integrations: [
    starlight({
      title: 'My delightful docs site',
    }),
  ],
});
```

You can pass the following options to the `starlight` integration.

### `title` (required)

**type:** `string`

Set the title for your website. Will be used in metadata and in the browser tab title.

### `description`

**type:** `string`

Set the description for your website. Used in metadata shared with search engines in the `<meta name="description">` tag if `description` is not set in a page’s frontmatter.

### `logo`

**type:** [`LogoConfig`](#logoconfig)

Set a logo image to show in the navigation bar alongside or instead of the site title. You can either set a single `src` property or set separate image sources for `light` and `dark`.

```js
starlight({
  logo: {
    src: '/src/assets/my-logo.svg',
  },
});
```

#### `LogoConfig`

```ts
type LogoConfig = { alt?: string; replacesTitle?: boolean } & (
  | { src: string }
  | { light: string; dark: string }
);
```

### `tableOfContents`

**type:** `false | { minHeadingLevel?: number; maxHeadingLevel?: number; }`  
**default:** `{ minHeadingLevel: 2; maxHeadingLevel: 3; }`

Configure the table of contents shown on the right of each page. By default, `<h2>` and `<h3>` headings will be included in this table of contents.

### `editLink`

**type:** `{ baseUrl: string }`

Enable “Edit this page” links by setting the base URL these should use. The final link will be `editLink.baseUrl` + the current page path. For example, to enable editing pages in the `withastro/starlight` repo on GitHub:

```js
starlight({
  editLink: {
    baseUrl: 'https://github.com/withastro/starlight/edit/main/',
  },
});
```

With this config, a `/introduction` page would have an edit link pointing to `https://github.com/withastro/starlight/edit/main/src/docs/introduction.md`.

### `sidebar`

**type:** [`SidebarGroup[]`](#sidebargroup)

Configure your site’s sidebar navigation items.

A sidebar is an array of groups, each with a `label` for the group and either an `items` array or an `autogenerate` configuration object.

You can manually set the contents of a group using `items`, which is an array that can include links and subgroups. You can also automatically generate the contents of a group from a specific directory of your docs, using `autogenerate`.

```js
starlight({
  sidebar: [
    // A group labelled “Start Here” containing two links.
    {
      label: 'Start Here',
      items: [
        { label: 'Introduction', link: '/intro' },
        { label: 'Next Steps', link: '/next-steps' },
      ],
    },
    // A group linking to all pages in the reference directory.
    {
      label: 'Reference',
      autogenerate: { directory: 'reference' },
    },
  ],
});
```

#### Sorting

Autogenerated sidebar groups are sorted by filename alphabetically.
For example, a page generated from `astro.md` would appear above the page for `starlight.md`.

#### Translating labels

If your site is multilingual, each item’s `label` is considered to be in the default locale. You can set a `translations` property to provide labels for your other supported languages:

```js
sidebar: [
  // An example sidebar with labels translated to French.
  {
    label: 'Start Here',
    translations: { fr: 'Commencez ici' },
    items: [
      {
        label: 'Getting Started',
        translations: { fr: 'Bien démarrer' },
        link: '/getting-started',
      },
      {
        label: 'Project Structure',
        translations: { fr: 'Structure du projet' },
        link: '/structure',
      },
    ],
  },
];
```

#### `SidebarGroup`

```ts
type SidebarGroup =
  | {
      label: string;
      translations?: Record<string, string>;
      items: Array<LinkItem | SidebarGroup>;
    }
  | {
      label: string;
      translations?: Record<string, string>;
      autogenerate: {
        directory: string;
      };
    };
```

#### `LinkItem`

```ts
interface LinkItem {
  label: string;
  link: string;
}
```

### `locales`

**type:** `{ [dir: string]: LocaleConfig }`

Configure internationalization (i18n) for your site by setting which `locales` are supported.

Each entry should use the directory where that language’s files are saved as the key.

```js
// astro.config.mjs
import { defineConfig } from 'astro/config';
import starlight from '@astrojs/starlight';

export default defineConfig({
  integrations: [
    starlight({
      title: 'My Site',
      // Set English as the default language for this site.
      defaultLocale: 'en',
      locales: {
        // English docs in `src/content/docs/en/`
        en: {
          label: 'English',
        },
        // Simplified Chinese docs in `src/content/docs/zh/`
        zh: {
          label: '简体中文',
          lang: 'zh-CN',
        },
        // Arabic docs in `src/content/docs/ar/`
        ar: {
          label: 'العربية',
          dir: 'rtl',
        },
      },
    }),
  ],
});
```

#### Locale options

You can set the following options for each locale:

##### `label` (required)

**type:** `string`

The label for this language to show to users, for example in the language switcher. Most often you will want this to be the language’s name as a user of that language would expect to read it, e.g. `"English"`, `"العربية"`, or `"简体中文"`.

##### `lang`

**type:** `string`

The BCP-47 tag for this language, e.g. `"en"`, `"ar"`, or `"zh-CN"`. If not set, the language’s directory name will be used by default.

##### `dir`

**type:** `'ltr' | 'rtl'`

The writing direction of this language; `"ltr"` for left-to-right (the default) or `"rtl"` for right-to-left.

#### Root locale

You can serve the default language without a `/lang/` directory by setting a `root` locale:

```js
starlight({
  locales: {
    root: {
      label: 'English',
      lang: 'en',
    },
    fr: {
      label: 'Français',
    },
  },
});
```

For example, this allows you to serve `/getting-started/` as an English route and use `/fr/getting-started/` as the equivalent French page.

### `defaultLocale`

**type:** `string`

Set the language which is the default for this site.
The value should match one of the keys of your [`locales`](#locales) object.
(If your default language is your [root locale](#root-locale), you can skip this.)

The default locale will be used to provide fallback content where translations are missing.

### `social`

**type:** `{ discord?: string; github?: string; mastodon?: string; twitter?: string }`

Optional details about the social media accounts for this site. Adding any of these will display them as icon links in the site header.

```js
starlight({
  social: {
    discord: 'https://astro.build/chat',
    github: 'https://github.com/withastro/starlight',
    mastodon: 'https://m.webtoo.ls/@astro',
    twitter: 'https://twitter.com/astrodotbuild',
  },
});
```

### `customCss`

**type:** `string[]`

Provide CSS files to customize the look and feel of your Starlight site.

Supports local CSS files relative to the root of your project, e.g. `'/src/custom.css'`, and CSS you installed as an npm module, e.g. `'@fontsource/roboto'`.

```js
starlight({
  customCss: ['/src/custom-styles.css', '@fontsource/roboto'],
});
```

### `head`

**type:** [`HeadConfig[]`](#headconfig)

Add custom tags to the `<head>` of your Starlight site.
Can be useful for adding analytics and other third-party scripts and resources.

```js
starlight({
  head: [
    // Example: add Fathom analytics script tag.
    {
      tag: 'script',
      attrs: {
        src: 'https://cdn.usefathom.com/script.js',
        'data-site': 'MY-FATHOM-ID',
        defer: true,
      },
    },
  ],
});
```

#### `HeadConfig`

```ts
interface HeadConfig {
  tag: string;
  attrs?: Record<string, string | boolean | undefined>;
  content?: string;
}
```