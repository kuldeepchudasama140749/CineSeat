[README.md](https://github.com/user-attachments/files/34697866/README.md)
# 🎬 CineSeat — Movie Ticket Booking System

A complete movie ticket booking website (like PVR / BookMyShow) built with
**pure HTML, CSS and vanilla JavaScript**. There is no framework, no backend
and no build step — the browser's **localStorage** is used as the database.

> Web Design Project · B.Tech CSE 2nd Year · Continuous Assessment

---

## 1. Definition of the Project

**Problem.** Buying a movie ticket at the counter means standing in a queue,
and you cannot see which seats are still free until it is your turn.

**Solution.** CineSeat is an online multiplex booking website with two sides:

| Side | Who uses it | What they can do |
|---|---|---|
| **Client side** | Movie viewers | Browse movies, read details, pick a showtime, choose seats on a live seat map, add snacks, apply a coupon, pay and get an instant ticket |
| **Admin side** | Cinema staff | Add/edit/delete movies, schedule showtimes, watch revenue and occupancy, manage every booking and every registered user |

**Technology used**

| Layer | Technology |
|---|---|
| Structure | HTML5 (semantic tags, accessible labels) |
| Styling | CSS3 (variables, Flexbox, Grid, media queries) |
| Behaviour + validation | Vanilla JavaScript (ES6) |
| Database | Browser `localStorage` (JSON) |
| Fonts | Clash Display + Satoshi (Fontshare) |
| Images | Locally generated SVG posters — works fully offline |

---

## 2. How to Run

Because `localStorage` is unreliable when a page is opened with a `file://`
path, run a tiny local server:

```bash
cd ticketbook
python3 -m http.server 8000
```

Then open **http://localhost:8000** in the browser.

> In VS Code you can instead right-click `index.html` → **Open with Live Server**.

### Demo accounts

| Role | URL | Email | Password |
|---|---|---|---|
| Customer | `login.html` | `aarav@gmail.com` | `Test@123` |
| Admin | `admin/login.html` | `admin@cineseat.com` | `Admin@123` |

You can also create a brand-new customer account from `signup.html`.

The first time the site opens it automatically fills localStorage with demo
data: **12 movies, 100+ showtimes, 6 users and 18 bookings**, so no screen is
ever empty. To start fresh: **Admin → Settings → Reset demo data**.

---

## 3. Pages

### Client side (9 pages)

| File | Page | Main features |
|---|---|---|
| `index.html` | Home | Hero + search, Now Showing grid, Coming Soon row, offers, features |
| `movies.html` | All movies | Live search, genre chips, language / status filters, 3-way sorting |
| `movie-details.html` | Movie details | Poster, synopsis, cast, 5-day date strip, showtimes grouped by screen |
| `seats.html` | Seat selection | 120-seat live map, 3 price tiers, aisles, max-10 rule, live total |
| `checkout.html` | Checkout | Booking summary, snack counters, coupon codes, Card / UPI / Wallet |
| `booking-success.html` | Ticket | Printable ticket with booking ID and CSS barcode |
| `my-bookings.html` | My bookings | Upcoming / Past / Cancelled tabs, cancel a booking |
| `login.html` | Login | Split-screen layout, validation, show/hide password |
| `signup.html` | Register | Validation + live password strength meter |

### Admin side (7 pages)

| File | Page | Main features |
|---|---|---|
| `admin/login.html` | Admin login | Separate staff gate |
| `admin/dashboard.html` | Dashboard | Revenue / tickets / occupancy / movie tiles, 7-day CSS bar chart, top movies, recent bookings |
| `admin/movies.html` | Movies | Full CRUD in a modal form, search + status filter |
| `admin/shows.html` | Showtimes | Full CRUD, clash detection, occupancy bar per show |
| `admin/bookings.html` | Bookings | Search, status filter, sorting, cancel a booking |
| `admin/users.html` | Users | Bookings + money spent per user, block / unblock / delete |
| `admin/settings.html` | Settings | Cinema details, default prices, theme, storage usage, reset data |

---

## 4. Folder Structure

```
ticketbook/
├── index.html                 ← client side
├── movies.html
├── movie-details.html
├── seats.html
├── checkout.html
├── booking-success.html
├── my-bookings.html
├── login.html
├── signup.html
│
├── admin/                     ← admin side
│   ├── login.html
│   ├── dashboard.html
│   ├── movies.html
│   ├── shows.html
│   ├── bookings.html
│   ├── users.html
│   └── settings.html
│
├── assets/
│   ├── css/
│   │   ├── base.css           design system: colours, buttons, forms, navbar
│   │   ├── user.css           client pages: hero, movie cards, seat map, ticket
│   │   ├── admin.css          dashboard: sidebar, stat tiles, chart
│   │   └── animations.css     ALL animations of the website
│   ├── js/
│   │   ├── store.js           the localStorage "database" + helpers
│   │   ├── seed.js            demo movies, shows, users, bookings
│   │   ├── ui.js              theme switch, mobile menu, toast, modal
│   │   ├── auth.js            signup / login / logout / page guards
│   │   ├── validate.js        ALL form validation rules
│   │   ├── user.js            logic of the client pages
│   │   ├── admin.js           logic of the admin pages
│   │   └── animate.js         scroll reveal, counters, confetti
│   └── img/
│       ├── favicon.svg
│       ├── logo.svg
│       └── posters/           12 movie posters (SVG)
│
└── README.md
```

---

## 5. The Database (localStorage)

Everything is stored as JSON text under these keys:

| Key | Holds |
|---|---|
| `cs_movies` | Every movie |
| `cs_shows` | Every showtime (movie + screen + date + time + prices) |
| `cs_bookings` | Every booking |
| `cs_users` | Registered customers |
| `cs_session` | Who is logged in right now |
| `cs_settings` | Cinema name, address, default prices |
| `cs_theme` | `light` or `dark` |
| `cs_seeded` | Marks that demo data was already created |

Example of one record:

```js
booking = {
  id: 'CS-48210',
  userId: 'us-a1b2c3',
  userName: 'Aarav Sharma',
  showId: 'sh-x9y8z7',
  movieTitle: 'Jawan',
  screen: 'Audi 1',
  date: '2026-08-24',
  time: '19:00',
  seats: ['F5', 'F6'],
  amount: 670,
  status: 'confirmed'
}
```

Nothing talks to `localStorage` directly — every page uses the helper
functions in `store.js` (`getMovies()`, `addBooking()`, `getOccupiedSeats()` …),
so the storage code sits in exactly one file.

---

## 6. JavaScript Validation

All validation lives in **`assets/js/validate.js`**. A rule is just an object
with a test and a message, so the same engine works on every form:

```js
setupForm(form, {
  suEmail: [ Rule.required('Please enter your email'),
             Rule.pattern(RX.email, 'That is not a valid email') ],
  suPhone: [ Rule.pattern(RX.phone, 'Enter a 10 digit number starting with 6-9') ]
}, function () {
  /* runs only when every field is correct */
});
```

Errors appear **when you leave a field** and again **on submit**; the field
turns red and the form will not submit until it is fixed.

| Form | What is checked |
|---|---|
| **Sign up** | Name (letters only, 3+), email format, duplicate email, mobile (10 digits, starts 6-9), strong password (upper + lower + digit + symbol) with a live strength meter, confirm password match, terms ticked |
| **Login** | Email format, password present, credentials checked against `cs_users`, blocked accounts refused |
| **Checkout — Card** | Name, 16-digit number verified with the **Luhn algorithm**, auto-formatting into groups of 4, MM/YY expiry that must not be in the past, 3-digit CVV |
| **Checkout — UPI** | `name@bank` pattern |
| **Checkout — Wallet** | 10-digit mobile + 6-digit OTP |
| **Coupon** | Only `CINE20` and `FIRST100` accepted; `FIRST100` refused if you already have a booking |
| **Admin — Movie** | Title length, genre, language, duration 30–300 min, rating 0–10, poster chosen, at least 2 cast names, synopsis 25+ characters |
| **Admin — Show** | Movie, screen, date not in the past, time slot, prices ₹50–₹2000, Gold ≥ Silver, Recliner ≥ Gold, and **no two shows on the same screen at the same time** |
| **Admin — Settings** | Cinema name, city, email, phone, address, price order, fee ₹0–₹200 |

Page guards in `auth.js` also stop a logged-out visitor from opening
`seats.html`, `checkout.html` or `my-bookings.html`, and stop a non-admin from
opening anything inside `/admin/`.

---

## 7. Design

- **Theme** — "Soft Plum": a muted plum-violet accent on a warm off-white
  background, and an ink-navy background in dark mode. Nothing neon.
- **Light + Dark mode** — the ☀ / ☾ button in the navbar. The choice is saved in
  `localStorage`, and a tiny script in `<head>` applies it *before* the page is
  painted so there is no white flash on reload.
- **Contrast** — body text is 14.9:1 on light and 13.8:1 on dark (WCAG AAA).
- **Fonts** — Clash Display for headings, Satoshi for body text.
- **Animations** — every animation is kept in one file, `assets/css/animations.css`
  (plus `assets/js/animate.js`). Deleting those two lines from the pages turns the
  whole site static without breaking anything. See Section 8.
- **Colour system** — every colour is a CSS variable in `:root`, so the whole
  site can be re-themed by editing about 20 lines at the top of `base.css`.

### Responsive

Tested from **320px to 1440px**. Breakpoints at 480 / 640 / 768 / 1024 / 1280.

| Screen | What changes |
|---|---|
| Phone | Navbar becomes a slide-in drawer; admin sidebar becomes a drawer; every data table turns into stacked cards; the seat summary becomes a bar fixed to the bottom; the seat map scrolls inside its own box so the page never scrolls sideways |
| Tablet | Two-column grids; the movie details page stacks the poster above the text |
| Desktop | Full layout, content centred with a 1200px maximum width |

---

## 8. Animations

All motion is written in **`assets/css/animations.css`** (keyframes + hover
effects) and **`assets/js/animate.js`** (the parts that need JavaScript).

| Where | Effect |
|---|---|
| Whole page | Content fades in; the theme change fades instead of snapping |
| Scroll | A thin progress bar at the top, a navbar shadow once you scroll, a "back to top" button, and **every component reveals as it enters the screen** — each one with its own entrance (see the table below) |
| Home hero | Heading, text, search box and stats rise in one after another; the three posters float slowly up and down |
| Movie cards | Card lifts, poster zooms inside its frame, dark overlay fades in, the Book button pops, the title turns plum |
| Movie lists | Cards are **invisible until you scroll to them**, then take turns between three different entrances. A `MutationObserver` watches the page, so cards drawn later — after a search, a filter or a date change — **animate in again** instead of just appearing |
| Movie details | Poster panel rises, cast circles pop in, date pills and showtime blocks stagger |
| Seat map | Seats appear row by row, lift on hover, pop when selected; the screen glows gently; chosen seat tags pop into the summary |
| Buttons | Lift on hover, press down on click, and a light sweeps across the primary button |
| Forms | Error messages drop down; the password strength bar slides |
| Checkout | Payment panels fade in; the Pay button turns into a spinner while processing |
| **Ticket page** | The green tick pops with an expanding ring, the ticket rises — and **confetti falls across the screen** 🎉 |
| Admin | Stat tiles stagger in and the numbers **count up** from zero, chart bars grow from the bottom, progress bars grow from the left, table rows slide in on every search |
| Toast / Modal | Toasts slide in from the right, modals scale up from behind the blur |

### A different entrance for every component

So the page never feels like one repeated effect, each component arrives in its
own way. All of it is in section 16 of `animations.css`.

| Component | How it enters |
|---|---|
| **Movie cards** | Take turns between **three** entrances — the 1st, 4th, 7th… swing in from the **left** with a 3D tilt; the 2nd, 5th, 8th… **rise up** from below; the 3rd, 6th, 9th… swing in from the **right**. A row of posters therefore arrives from three directions at once |
| Feature boxes | Zoom out from small |
| Offer coupons | Slide in from the right |
| Stat tiles | Drop down from above with a 3D tilt |
| Top-movies list | Slides in from the left |
| Cast circles | Pop from small while spinning slightly |
| Date pills | Drop in from the top |
| Showtime blocks | Slide in from the left |
| Snack rows | Slide in from the right |
| Tickets | Tilt forward, like a card being placed on a table |
| Call-to-action band | Zooms in |
| Admin panels | Rise up |
| Booking cards | Fade and grow |
| Seat map panel | Zooms in from the centre |
| Filter bars / tabs | Fade down from above |
| Table rows | Fade only — moving a `<tr>` looks glitchy in some browsers |

Items sitting next to each other are staggered by **70 ms** each, so a row
cascades instead of appearing together.

**Movie card hover:** the card lifts, the poster zooms inside its frame, a band
of light **sweeps across** the poster, the rating badge grows, a dark overlay
fades in with a Book button, and the title turns plum.

**Accessibility:** if the computer is set to "reduce motion"
(`prefers-reduced-motion`), every animation is switched off automatically —
handled in both the CSS and `animate.js`.

---

## 9. Seat Map

The hall has **120 seats — rows A to J × 12 seats**, with walking aisles after
seat 3 and seat 9.

| Rows | Class | Default price |
|---|---|---|
| A – C | Silver | ₹180 |
| D – G | Gold | ₹250 |
| H – J | Recliner (drawn wider) | ₹450 |

Evening shows automatically cost 20% more. A seat is shown as **sold** if it is
in the show's pre-booked list *or* in any confirmed booking, so a seat booked by
one account immediately turns grey for everyone else. A maximum of 10 seats can
be booked at once, and the seats are checked again just before payment in case
somebody else booked them in another tab.

---

## 10. Testing Done

The whole site was tested end-to-end with an automated script (106 checks, all
passing), covering: seeding, signup and login validation, filters and search,
showtime rendering, seat selection and pricing, coupon rules, Luhn card
validation, booking creation, seats becoming unavailable afterwards, the ticket
page, admin login, dashboard figures, movie and showtime CRUD (including the
same-screen clash rule), user blocking, settings validation, the page guards,
and the animation layer (scroll reveal, staggered delays, counters and confetti).

---

## 11. Marks Mapping

| Rubric item | Where it is in this project |
|---|---|
| Definition of Project | Section 1 of this README |
| Wireframe | Layout followed by every page (nav → content → footer) |
| Client Side Pages (responsive) | 9 pages, `index.html` … `signup.html` |
| Admin Side Pages (responsive) | 7 pages inside `admin/` |
| Validation Using JavaScript | `assets/js/validate.js` + Section 6 |
| Final Submission | This folder, zipped, plus the GitHub link |

---

## 12. Possible Improvements

- A real backend (PHP / Node + MySQL) instead of localStorage
- Real payment gateway integration
- Email or SMS ticket delivery with a real QR code
- Multiple cinemas and city selection
- More than one admin account (see `ADMIN_ACCOUNT` in `assets/js/auth.js`)

---

Made with ❤️ using HTML, CSS and JavaScript.
