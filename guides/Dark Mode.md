---
index: 3
title: "Add a dark mode toggle"
description: A simple way to switch themes on your website which takes into account users' system preferences.
date: "2022-08-04"
authors:
  - name: "Michael Fester"
    twitter: "michaelfester"
    avatarUrl: "https://res.cloudinary.com/djp21wtxm/image/upload/v1651630007/Me_uyndrz.jpg"
---

import { useEffect, useState } from "react"
import cn from "classnames"
import { TwoOfThreeCols } from "@components/ui/gridcolspans"
import { Moon, Sun } from "react-feather"
import { Bleed } from "@components/ui/bleed"
import { Image } from "@components/ui/image"

export const Gallery = () => {
  const [dark, setDark] = useState(true)

  useEffect(() => {
    setInterval(() => {
      setDark(d => !d)
    }, 4000)
  }, [])

  return <Bleed>
    <a href="/community/sites/402226441792160481" className="block relative rounded-md overflow-hidden not-prose p-0 hover:opacity-70 unstyled-link border-b-0">
        <div className={cn("relative z-0 w-full transition duration-500", {
          "opacity-0": dark
        })}>
          <Image
            bordered
            alt="Light mode"
            className="object-contain w-full"
            src="https://res.cloudinary.com/djp21wtxm/image/upload/v1662401680/i1600x1001-_kH-9Eigr1eq_wej35v.png"
          />
        </div>
        <div className={cn("absolute top-0 w-full transition duration-500", {
            "opacity-0": !dark
        })}>
          <Image
            alt="Dark mode"
            bordered
            className="object-contain w-full"
            src="https://res.cloudinary.com/djp21wtxm/image/upload/v1662401657/i1600x1001-ThcbPDV4gwgo_uokpja.png"
          />
      </div>
    </a>
  </Bleed>
}

<TwoOfThreeCols>

In this guide, we are using [`next-themes`](https://github.com/pacocoursey/next-themes), which is a library that makes it easy to manage website themes, including taking into account users' system preferences.

</TwoOfThreeCols>

<Gallery />

<TwoOfThreeCols>

## Set up `next-theme`

We first need to wrap our content in the theme provider. The most convenient place to do it is in templates. So let's create a template, say `main`, in the `templates` folder, and add the following:

```jsx
import { ThemeProvider } from "next-themes"

export const Template = ({ children }) => {
  return (
    <ThemeProvider attribute="class">
      <div className="prose dark:prose-invert p-8">
        {children}
      </div>
    </ThemeProvider>)
}
```

Some notes:
- We will be using [Tailwind CSS](https://tailwindcss.com/) for our theme definitions, and since it is based on class names, we are telling `ThemeProvider` to use `class` for theming.
- We wrap our content inside a `div` with the `prose` and `dark:prose-invert` class, which are [utility classes from Tailwind](https://tailwindcss.com/docs/typography-plugin) that sets up nice typography and color defaults.
- To use the template on all pages, we can add the following line to our `motif.json` configuration:

```json
{
  "templates": {
    "**/*": "main",
  },
  // ...
}
```

## Create the theme switcher

Next, let's create a toggle to switch between themes.

export const SwitcherDemo = () => {
  const [dark, setDark] = useState(false)

  return <Bleed>
    <div className={cn("px-8 py-20 rounded-md flex items-center justify-center transition", {
        "bg-neutral-50": !dark,
        "bg-neutral-900": dark
      })}>
      <div
        onClick={() => setDark(d => !d)}
        className={cn("p-2 cursor-pointer transition rounded", {
          "text-neutral-900 hover:bg-neutral-100": !dark,
          "text-white hover:bg-white/10": dark,
        })}>
        { dark ? <Sun /> : <Moon />}
      </div>
    </div>
  </Bleed>
}

<SwitcherDemo />

```jsx
import { useTheme } from "next-themes"
import { Moon, Sun } from "react-feather"

export const ThemeSwitcher = () => {
  const { resolvedTheme, setTheme } = useTheme()
  const [isMounted, setMounted] = useState(false)
  const toggleColorMode = () => setTheme(resolvedTheme === 'dark' ? 'light' : 'dark')

  useEffect(() => {
    setMounted(true)
  }, [])

  return (
    <>
      {isMounted && (
        <button
          className="w-4 h-4 cursor-pointer focus:outline-none text-neutral-900 dark:text-white"
          onClick={toggleColorMode}
          aria-label={`Switch to ${resolvedTheme === 'dark' ? "light" : "dark"} mode`}
        >
          {resolvedTheme === 'dark' ? <Sun /> : <Moon />}
        </button>
      )}
    </>
  )
}
```

Note the use of an `isMounted` flag, which is there to prevent [React hydration mismatches](https://github.com/pacocoursey/next-themes#avoid-hydration-mismatch).

Let's put the theme switcher in our template, so that it is included on all pages:

```jsx
<ThemeProvider attribute="class">
  <div className="prose dark:prose-invert p-8">
    <ThemeSwitcher />
    {children}
  </div>
</ThemeProvider>
```

## Configure Tailwind

We need to instruct Tailwind to use `class` for determining dark mode. In `tailwind.config.js` (located at the root of your project), add the following flag:

```js
module.exports = {
  darkMode: 'class',
  // ...
}
```

## Configure the theme

We are all set up. Now we just need to tell what colors/styles to use in dark mode. This is done by adding a `dark:` prefix to our Tailwind classes. So for instance if we want a button to have a blue background in light mode and a white background in dark mode, we can specify it as follows:

```jsx
<button className="bg-blue dark:bg-white">Click me</button>
```

Any class can be prefixed with the `dark:` prefix, so we can customize more than just colors:

```jsx
<button className="bg-blue dark:bg-white text-white dark:text-black rounded-sm dark:rounded-lg">Click me</button>
```

To change the website background, we'll want to set the style of our page body, globally in `main.css`:

```css
body {
  @apply bg-white dark:bg-black;
}
```

---

That's it! We now have a dark mode toggle and a way to customize the design when it's on, to our users' delight.

If you want to try out a fully working setup, check out our [Highway Docs template](https://motif.land/community/sites/402226441792160481) →

</TwoOfThreeCols>