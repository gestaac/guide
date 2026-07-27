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
- [The HTML structure](#the-html-structure)
  - [The document head](#the-document-head)
  - [The page skeleton](#the-page-skeleton)
  - [Which element to use, and why](#which-element-to-use-and-why)
  - [Patterns in the markup](#patterns-in-the-markup)
- [How it was built, step by step](#how-it-was-built-step-by-step)
  - [1. Reset and base styles](#1-reset-and-base-styles)
  - [2. The container — one source of alignment](#2-the-container--one-source-of-alignment)
  - [3. Sticky header and flexbox navbar](#3-sticky-header-and-flexbox-navbar)
  - [4. Smooth-scrolling anchor links](#4-smooth-scrolling-anchor-links)
  - [5. The two-column hero](#5-the-two-column-hero)
  - [6. The card row](#6-the-card-row)
  - [7. The mobile breakpoint](#7-the-mobile-breakpoint)
- [Scaling the card row past three](#scaling-the-card-row-past-three)
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

## The HTML structure

### The document head

```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="stylesheet" href="/css/flex.css">
```

**The viewport meta tag is the single most important line for a responsive
page.** Without it, mobile browsers assume they're rendering a desktop site: they
lay the page out at roughly 980px wide and then zoom the whole thing out to fit.
The text becomes tiny, and — critically — **`max-width` media queries never
trigger**, because the browser still reports a ~980px viewport. Every mobile rule
in `flex.css` depends on this tag being present.

`width=device-width` says "use the device's actual width," and
`initial-scale=1.0` says "don't zoom."

`charset="UTF-8"` tells the browser how to decode the bytes in the file. Without
it, accented characters, curly quotes, and symbols can render as garbage.

### The page skeleton

```
body
├── header                      sticky, full width
│   └── div.container.bar       1400px well, flex row
│       ├── a.logo
│       └── nav.nav             the five menu links
│
└── main
    ├── section.hero            full width, gradient background
    │   └── div.container.hero-inner    flex row of two columns
    │       ├── div.hero-text           heading, paragraph, button
    │       └── div.hero-image          the photo
    │
    ├── section#section1        anchor target for "Menu 1"
    │   └── div.container
    │       ├── h2.section-title
    │       └── div.cards       flex row of three cards
    │           ├── article.card
    │           ├── article.card
    │           └── article.card
    │
    └── section#section2        anchor target for "Menu 2"
        └── div.container
```

The repeating shape is worth noticing: **a full-width outer element, wrapping a
`.container` that holds the content.** The outer element owns the background; the
inner one owns the alignment. Every band of the page follows it.

### Which element to use, and why

| Element | Why it's used here |
| --- | --- |
| `<header>` | The page's top banner. Not the same as `<head>` — this one is visible. |
| `<nav>` | A block of navigation links. Screen readers can jump straight to it. |
| `<main>` | The primary content, once per page. Lets assistive tech skip the header. |
| `<section>` | A distinct, thematic band of the page — usually with a heading. |
| `<article>` | A self-contained unit that would still make sense on its own — each card. |
| `<div>` | No meaning at all. Used purely as a styling hook (`.container`, `.card-body`). |

The rule of thumb: **reach for `<div>` only when nothing more descriptive fits.**
The cards are `<article>` because a card with its own image, heading, and text is
self-contained. `.card-body` is a `<div>` because it exists only to hold padding.

This costs nothing and buys real things — screen-reader navigation, better search
engine understanding, and markup that's easier to read six months later.

### Patterns in the markup

**Two classes on one element, doing different jobs:**

```html
<div class="container bar">
<div class="container hero-inner">
```

`.container` is the shared layout well — max-width, centering, padding. `.bar` and
`.hero-inner` add behaviour specific to that one spot. Splitting them this way
means the alignment rules live in exactly one place, and each component only
describes what makes it different.

**IDs are link targets, not styling hooks:**

```html
<a href="#section1">Menu 1</a>
...
<section id="section1">
```

The `id` values here exist so the anchor links have something to point at. Styling
is done entirely through classes — that keeps specificity low and predictable,
since an `id` selector is much harder to override than a class.

**Every image has `alt` text:**

```html
<img src="..." alt="A lake reflecting mountains at dusk">
```

`alt` describes the image for screen readers, and displays if the image fails to
load — which matters here, since these are hotlinked from an external CDN.
Describe what's in the photo; don't write "image of." An empty `alt=""` is correct
only for purely decorative images that add no information.

**`&amp;` is an HTML entity:**

```html
<a href="" class="logo">Chester<span>&amp; Co.</span></a>
```

A bare `&` starts an entity in HTML, so writing the character literally is
ambiguous. `&amp;` renders as `&`. The `<span>` wraps "& Co." so it can be styled
differently from "Chester" later — a hook placed in advance.

**`href=""` is a placeholder, not a no-op.** Menu 3, 4, and 5 still have empty
hrefs, which reload the current page when clicked. Point them at real targets
(`#section3`) as you add sections.

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

## Scaling the card row past three

The current setup works because there are exactly three cards. Add a fourth,
fifth, or sixth and it starts to fall apart — here's why, and what to change.

### Why `flex: 1` stops working

`flex: 1` is shorthand for three separate values:

```css
flex: 1;          /* is the same as... */
flex: 1 1 0;
/*    │ │ └── flex-basis:  the starting width, before any space is shared */
/*    │ └──── flex-shrink: how eagerly it gives up space when short */
/*    └────── flex-grow:   how eagerly it takes leftover space */
```

The part that causes trouble is **`flex-basis: 0`**. It means "ignore how wide the
content wants to be — just divide the row evenly." Three cards get a third each,
which is comfortable. But six cards get a sixth each, which on a 1400px container
is about 210px per card. The images squeeze, the paragraphs turn into narrow
columns of two-word lines, and nothing ever moves to a second row.

They never wrap because `flex-wrap` defaults to `nowrap`. Flex items shrink
indefinitely rather than break onto a new line — you have to opt in.

### The fix: give each card a preferred width, and allow wrapping

```css
.cards {
    display: flex;
    flex-wrap: wrap;      /* allow a second row */
    gap: 24px;
}

.card {
    flex: 1 1 300px;      /* grow, shrink, but aim for 300px */
}
```

Now each card asks to be **300px wide**. The browser fits as many as it can per
row, pushes the rest to the next line, and `flex-grow: 1` shares the leftover
space so each row still ends flush with the container edge.

The result adapts on its own:

| Container width | Cards per row |
| --- | --- |
| 1400px | 4 |
| 1000px | 3 |
| 700px | 2 |
| 400px | 1 |

**The media query for cards becomes unnecessary.** Because a card wants 300px and
a phone offers less than that after padding, it naturally lands one per row. You
can delete this rule entirely:

```css
/* no longer needed with flex-wrap + flex-basis */
@media screen and (max-width: 768px) {
    .cards {
        flex-direction: column;
    }
}
```

That's the appealing part of this approach — the layout responds to the *space
available*, not to a breakpoint you picked by hand.

### The catch: the lonely last card

With `flex-grow: 1`, any leftover card on the final row **stretches to fill the
whole width**. Seven cards at four-per-row leaves three on row two, each 33% wide
instead of 25% — visibly mismatched against the row above.

Two ways to handle it:

```css
.card { flex: 0 1 300px; }    /* don't grow — cards stay 300px */
```

Turning off `flex-grow` keeps every card the same size, but leaves a ragged gap at
the end of each row. Pair it with `justify-content: center` on `.cards` so the gap
is split evenly on both sides rather than dumped on the right.

Which you prefer is a judgement call: **stretch** keeps clean edges but uneven
card sizes; **no stretch** keeps uniform cards but a ragged right edge.

### When to switch to CSS Grid

If you want equal-sized cards *and* flush edges, that's the point where flexbox
stops being the right tool. One line of Grid does it:

```css
.cards {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 24px;
}
```

`auto-fit` fits as many columns as will hold at least 300px, and `1fr` splits the
remaining space equally among them — so every card in every row is the same width,
including the last one. No media query, no lonely-card problem.

The honest summary: **flexbox is for arranging things in one direction; Grid is
for laying things out in two.** A row of cards that wraps into multiple rows is
really a grid, so Grid handles it with less fighting. Flexbox is still the right
choice for the navbar and the two-column hero, which are genuinely
one-dimensional.

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

**Without the viewport meta tag, no media query works on a phone.** The browser
reports a ~980px viewport regardless of the real screen, so `max-width: 768px`
never matches. This is the first thing to check when a page "isn't responsive"
despite correct CSS.

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
- **Add more cards.** See
  [Scaling the card row past three](#scaling-the-card-row-past-three) — `flex: 1`
  breaks down past three or four, and the fix is a two-line change.
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
