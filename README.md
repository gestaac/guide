# Responsive Landing Page — Flexbox Practice

A single-page landing layout built with **HTML and CSS only — no JavaScript**.

Covers the pieces most landing pages need: a sticky navigation bar, a two-column
hero, a row of image cards, smooth-scrolling anchor links, and a single mobile
breakpoint that reflows the whole page.

---

## Table of contents

- [What it includes](#what-it-includes)
- [File structure](#file-structure)
- [Running it](#running-it)
- [How it was built, step by step](#how-it-was-built-step-by-step)
  - [1. Reset and base styles](#1-reset-and-base-styles)
  - [2. The container — one source of alignment](#2-the-container--one-source-of-alignment)
  - [3. Sticky header and flexbox navbar](#3-sticky-header-and-flexbox-navbar)
  - [4. Smooth-scrolling anchor links](#4-smooth-scrolling-anchor-links)
  - [5. The two-column hero](#5-the-two-column-hero)
  - [6. The card row](#6-the-card-row)
  - [7. The mobile breakpoint](#7-the-mobile-breakpoint)
- [Full source](#full-source)
- [CSS concepts used](#css-concepts-used)
- [Gotchas worth remembering](#gotchas-worth-remembering)
- [Ideas to extend this](#ideas-to-extend-this)

---

## What it includes

| Feature | How it's done |
| --- | --- |
| Sticky navbar | `position: sticky` + flexbox row |
| Responsive nav | Stacks and wraps at 768px — no hamburger, no JS |
| Two-column hero | `flex: 1` on both columns |
| Three image cards | Flex row that fills the container width |
| Smooth scroll | `scroll-behavior: smooth` on `html` |
| Mobile layout | One media query at `768px` |

---

## File structure

```
web_design/
├── flexbox.html
├── css/
│   └── flex.css
└── README.md
```

---

## Running it

No build step and no dependencies — open `flexbox.html` in a browser.

> **Note:** the stylesheet is linked as `/css/flex.css` with a leading slash,
> which resolves from the **server root**. That works with a local server
> (VS Code's Live Server, `python -m http.server`), but breaks when you
> double-click the file to open it directly from disk. If you want it to work
> both ways, change the link to a relative path:
>
> ```html
> <link rel="stylesheet" href="css/flex.css">
> ```

Images are hotlinked from Unsplash's CDN, so an internet connection is needed.
For production, download them into an `images/` folder and reference locally.

---

## How it was built, step by step

### 1. Reset and base styles

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
```

`box-sizing: border-box` is the important one. By default, if you give an element
`width: 300px` and `padding: 20px`, it actually occupies **340px** — padding is
added on top of the width. With `border-box`, padding is included *inside* the
width, so 300px means 300px. This makes layout math predictable, and it's why
almost every stylesheet starts here.

### 2. The container — one source of alignment

```css
.container {
    max-width: 1400px;
    margin: 0 auto;
    padding: 10px 20px;
}
```

Everything on the page that should line up vertically uses this one class — the
navbar, the hero, and each section. `margin: 0 auto` centers the box once it hits
its max width; the padding keeps content off the screen edge on phones.

Sections and their content wrapper are kept as **separate elements**:

```html
<section id="section1">        <!-- full viewport width -->
    <div class="container">    <!-- the 1400px content well -->
```

This matters as soon as a section gets a background colour. If the `section` and
the `.container` were the same element, the background would stop at 1400px and
leave empty gutters on a wide monitor. Keeping them apart lets the background
span edge to edge while the content stays aligned with the nav.

Change `max-width` in that one rule and the entire page realigns together.

### 3. Sticky header and flexbox navbar

```css
header {
    position: sticky;
    top: 0;
    z-index: 20;
}

header .bar {
    display: flex;
    justify-content: space-between;
    min-height: 74px;
    align-items: center;
}
```

`position: sticky` with `top: 0` means the header scrolls normally until it
reaches the top of the viewport, then pins there. `z-index: 20` keeps it above
the page content as that content scrolls underneath.

Inside, flexbox does the arranging:

- `justify-content: space-between` → logo pushed left, nav pushed right
- `align-items: center` → both vertically centered in the 74px bar

**`min-height`, not `height`.** A fixed `height: 74px` looks fine on desktop but
breaks on mobile: once the items stack into a column they need more vertical
room, and a fixed height forces them to overflow outside the header box.
`min-height` keeps the 74px floor while letting the header grow when it has to.

### 4. Smooth-scrolling anchor links

Three separate pieces make this work.

**The link and the target must match.** A `#` in an `href` means "an id on this
page":

```html
<a href="#section1">Menu 1</a>
...
<section id="section1">
```

That's plain HTML — the jumping works with no CSS at all.

**Smooth scrolling is CSS, not an HTML attribute:**

```css
html {
    scroll-behavior: smooth;
}
```

It goes on `html` because `html` is the element that actually scrolls the page.
Browsers automatically disable this for users who have "reduce motion" enabled in
their OS settings, so it's accessible by default.

**`scroll-margin-top` — the piece people miss:**

```css
section {
    scroll-margin-top: 94px;
}
```

Because the header is sticky, it floats above the page. When the browser jumps to
a section it aligns that section to the very top of the viewport — directly
*underneath* the header, hiding the heading. `scroll-margin-top` tells the browser
to stop short by that amount. The value is the header's real height:
74px bar + 10px top and bottom padding = 94px.

### 5. The two-column hero

```css
.hero-inner {
    display: flex;
    align-items: center;
    gap: 48px;
}

.hero-text  { flex: 1; }
.hero-image { flex: 1; }
```

That's the whole layout. `flex: 1` on both children tells them to share the
available space equally, so each takes 50%. `align-items: center` vertically
centers the shorter column against the taller one.

To make the image column wider, change one number — `flex: 1.5` on `.hero-image`
gives it 60% of the row.

The heading scales without a media query:

```css
.hero h1 {
    font-size: clamp(2rem, 4vw, 3.2rem);
}
```

`clamp(min, preferred, max)` reads as: never smaller than `2rem`, never larger
than `3.2rem`, otherwise 4% of the viewport width. The heading scales smoothly
with the screen instead of snapping at a breakpoint. Headings are usually the
first thing to break on small screens, so this is a useful tool to reach for.

### 6. The card row

```css
.cards {
    display: flex;
    gap: 24px;
}

.card {
    flex: 1;
}
```

Same pattern as the hero. Because the cards are sized by `flex: 1` rather than a
fixed width, they divide the row among however many there are — two cards take
half each, three take a third each. **Adding a fourth card needs no CSS change
at all.**

That's the practical advantage of flexbox: you describe the *relationship*
between items ("share the space evenly") instead of their measurements, so the
layout survives content changes.

Images inside the cards use:

```css
.card img {
    width: 100%;
    height: 220px;
    object-fit: cover;
}
```

`object-fit: cover` is essential here. Forcing photos into a fixed box normally
**squashes** them — `cover` crops to fill instead, preserving the proportions.
Without this line, the three photos would look visibly distorted. This applies to
nearly every card layout you'll build.

### 7. The mobile breakpoint

One media query handles the entire page:

```css
@media screen and (max-width: 768px) {
    header .bar { flex-direction: column; gap: 12px; }
    nav         { flex-wrap: wrap; justify-content: center; gap: 16px; }
    .cards      { flex-direction: column; }
    .hero-inner { flex-direction: column; gap: 32px; }
    .hero-image img { height: 260px; }
}
```

Nearly every fix is the same single property: **`flex-direction: column`**. Rows
become columns and the page reflows. That's the payoff for building the layout
with flexbox in the first place.

`flex-wrap: wrap` on the nav lets five links spill onto a second line rather than
shrink into unreadable slivers.

**Why 768px?** It's the standard tablet-width breakpoint — roughly where a
horizontal nav stops fitting comfortably. A much smaller value like `400px` only
triggers on very small phones, leaving tablets and small laptops stuck with a
cramped desktop layout.

**Stacking order follows HTML order.** When a flex row becomes a column, items
stack in source order. So write your HTML in the order that makes sense on a
phone, and let the desktop layout rearrange it.

---

## Full source

<details>
<summary><strong>flexbox.html</strong></summary>

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="/css/flex.css">
    <title>Document</title>
</head>

<body>
    <header>
        <div class="container bar">
            <a href="" class="logo">Chester<span>&amp; Co.</span></a>
            <!-- menu -->
            <nav class="nav">
                <a href="#section1">Menu 1</a>
                <a href="#section2">Menu 2</a>
                <a href="">Menu 3</a>
                <a href="">Menu 4</a>
                <a href="">Menu 5</a>
            </nav>
        </div>

    </header>

    <main>
        <section class="hero">
            <div class="container hero-inner">

                <!-- left column: text + call to action -->
                <div class="hero-text">
                    <h1>Quiet places, photographed well</h1>
                    <p>A small collection of landscapes taken early in the morning, when the light is still soft and
                        nobody else is around.</p>
                    <a href="#section1" class="btn">View the collection</a>
                </div>

                <!-- right column: image -->
                <div class="hero-image">
                    <img src="https://images.unsplash.com/photo-1469474968028-56623f02e42e?w=1200&q=80"
                        alt="Sunlight breaking over a mountain ridge">
                </div>

            </div>
        </section>

        <section id="section1">
            <div class="container">
                <h2 class="section-title">Menu 1</h2>

                <div class="cards">
                    <article class="card">
                        <img src="https://images.unsplash.com/photo-1506744038136-46273834b3fb?w=800&q=80"
                            alt="A lake reflecting mountains at dusk">
                        <div class="card-body">
                            <h3>Image 1</h3>
                            <p>A quiet lake at dusk, with the mountains folding into the water. Cool light, still
                                surface, almost no movement.</p>
                        </div>
                    </article>

                    <article class="card">
                        <img src="https://images.unsplash.com/photo-1441974231531-c6227db76b6e?w=800&q=80"
                            alt="Sunlight coming through a green forest">
                        <div class="card-body">
                            <h3>Image 2</h3>
                            <p>Morning light cutting through a stand of trees. Warm greens up close, hazy and pale
                                further back.</p>
                        </div>
                    </article>

                    <article class="card">
                        <img src="https://images.unsplash.com/photo-1470071459604-3b5ec3a7fe05?w=800&q=80"
                            alt="Fog rolling over a forested hillside">
                        <div class="card-body">
                            <h3>Image 3</h3>
                            <p>Fog sitting low over the hills, thick enough that the ridgeline behind it barely reads as
                                a shape.</p>
                        </div>
                    </article>
                </div>
            </div>
        </section>

        <section id="section2">
            <div class="container">
                <h2 class="section-title">Section 2</h2>
                <p>This is the second section.</p>
            </div>
        </section>
    </main>
</body>

</html>
```

</details>

<details>
<summary><strong>css/flex.css</strong></summary>

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

html {
    scroll-behavior: smooth;
}

body {
    font-family: 'arial', sans-serif;
}

a {
    text-decoration: none;
}

/* header */

header {
    background: #fff;
    border-bottom: 1px solid #e7e2d8;
    position: sticky;
    top: 0;
    z-index: 20;
}

header .bar {
    display: flex;
    justify-content: space-between;
    min-height: 74px;
    align-items: center;
}

nav {
    display: flex;
    align-items: center;
    gap: 32px;
}

nav a {
    color: #2b2b2b;
}

.container {
    max-width: 1400px;
    margin: 0 auto;
    padding: 10px 20px;
}

/* sections */

section {
    min-height: 100vh;

    /* stops the sticky header from covering the heading on arrival */
    scroll-margin-top: 94px;
}

/* hero */

.hero {
    display: flex;
    align-items: center;
    background: linear-gradient(to bottom, #faf8f4, #f0ece4);
}

/* the two columns */
.hero-inner {
    display: flex;
    align-items: center;
    gap: 48px;
}

.hero-text {
    flex: 1;
}

.hero-image {
    flex: 1;
}

.hero-image img {
    display: block;
    width: 100%;
    height: 460px;
    object-fit: cover;
    border-radius: 12px;
}

.hero h1 {
    /* shrinks on phones, grows on desktop, no media query needed */
    font-size: clamp(2rem, 4vw, 3.2rem);
    line-height: 1.15;
    margin-bottom: 16px;
}

.hero p {
    font-size: 1.05rem;
    line-height: 1.6;
    color: #5a5a5a;
}

.btn {
    display: inline-block;
    margin-top: 28px;
    padding: 14px 34px;
    background: #2b2b2b;
    color: #fff;
    border-radius: 6px;
    font-weight: bold;
    transition: background .2s ease;
}

.btn:hover {
    background: #4a4a4a;
}

.section-title {
    text-align: center;
    margin: 48px 0 28px;
}

/* cards */

.cards {
    display: flex;
    gap: 24px;
}

.card {
    /* no max-width, so the row fills the container and lines up with the nav */
    flex: 1;
    border: 1px solid #e7e2d8;
    border-radius: 10px;
    overflow: hidden;
}

.card img {
    display: block;
    width: 100%;
    height: 220px;

    /* crops instead of squashing when the ratio doesn't match */
    object-fit: cover;
}

.card-body {
    padding: 16px 20px;
}

.card-body h3 {
    margin-bottom: 8px;
}

.card-body p {
    color: #5a5a5a;
    line-height: 1.6;
}

@media screen and (max-width: 768px) {

    /* stack the logo above the menu instead of squeezing them side by side */
    header .bar {
        flex-direction: column;
        gap: 12px;
    }

    /* let the links flow onto a second line if they run out of room */
    nav {
        flex-wrap: wrap;
        justify-content: center;
        gap: 16px;
    }

    /* one card per row instead of three narrow ones */
    .cards {
        flex-direction: column;
    }

    /* hero columns stack: text first, image below */
    .hero-inner {
        flex-direction: column;
        gap: 32px;
    }

    .hero-image img {
        height: 260px;
    }
}
```

</details>

---

## CSS concepts used

| Property | What it does here |
| --- | --- |
| `box-sizing: border-box` | Padding counts inside the width, so layout math is predictable |
| `display: flex` | Turns a container into a row (or column) of arrangeable items |
| `justify-content` | Spacing **along** the flex direction — horizontally in a row |
| `align-items` | Alignment **across** the flex direction — vertically in a row |
| `gap` | Space between flex items, without margins on each child |
| `flex: 1` | "Share the leftover space equally" — the basis of both layouts here |
| `flex-direction: column` | Turns a row into a stack; the core mobile fix |
| `flex-wrap: wrap` | Lets items spill onto a new line instead of shrinking |
| `position: sticky` | Scrolls normally, then pins at the `top` offset |
| `object-fit: cover` | Crops an image to fill a box instead of squashing it |
| `scroll-behavior: smooth` | Animates anchor jumps instead of teleporting |
| `scroll-margin-top` | Offsets the scroll landing point so a fixed header doesn't cover it |
| `clamp(min, val, max)` | Fluid sizing with hard limits — no media query needed |
| `min-height: 100vh` | Makes a section at least one full screen tall |

---

## Gotchas worth remembering

**`height` vs `min-height` on a flex container.** A fixed height crushes content
the moment items stack into a column. Use `min-height` for anything that changes
shape across breakpoints.

**`object-fit: cover` is not optional for images in fixed boxes.** Without it,
every photo gets stretched. It's the single most reused line in card layouts.

**A sticky header hides anchor targets.** `scroll-behavior: smooth` alone lands
the heading *behind* the header. Pair it with `scroll-margin-top` set to the
header's height.

**Keep sections and containers as separate elements.** Merging them caps the
section's background at the container width, which shows up as white gutters on
wide screens as soon as you add a background.

**`max-width` on flex children can silently break alignment.** The cards
originally had `max-width: 420px`, which made the row narrower than the container
and left them visibly inset from the nav. Removing it let `flex: 1` fill the
width and the edges lined up.

**Mobile stacking follows HTML source order.** Write the HTML in the order that
reads well on a phone; use flexbox to rearrange it on desktop.

---

## Ideas to extend this

- **Hamburger menu.** The stacked nav takes ~130px of a sticky header on a phone,
  which is a lot of permanent screen space. That pressure — not aesthetics — is
  the real reason hamburger menus exist. It can be done without JavaScript using
  a hidden checkbox and the `:checked ~ sibling` selector.
- **Wrap the cards instead of stacking them.** `flex: 1 1 300px` with
  `flex-wrap: wrap` on `.cards` turns them into a self-arranging grid that needs
  no media query and handles any number of cards.
- **Fill in Menu 3–5.** They still point at `href=""`, which reloads the page.
  Give them `#section3` and so on as you add sections.
- **Set a real page title.** `<title>Document</title>` is the default placeholder
  and shows up in the browser tab and search results.
- **Host the images locally.** Hotlinked Unsplash URLs can change or go away.

---

## License

Free to use for learning. Photographs are from
[Unsplash](https://unsplash.com) under the
[Unsplash License](https://unsplash.com/license).
