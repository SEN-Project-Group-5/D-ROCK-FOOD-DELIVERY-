# CampusEats — WordPress Build Guide

## What this covers
The interactive prototype (`campuseats-website.html`) shows every page, flow, and piece of copy for CampusEats end to end — homepage, menu, delivery zones with a node map, login/register, and working Customer, Vendor, and Courier dashboards with a simulated order lifecycle (Placed → Preparing → Ready → Dispatched → Delivered, with OTP delivery confirmation and the 70/15/15 payout split).

I can't provision a live WordPress install, install plugins, or connect a payment gateway from here — that needs real hosting, licensed plugins, and admin access. What's below is the concrete build plan your developer (or you, with a bit of patience) can follow to turn this design into the real, database-backed WordPress site, using the plugin stack you specified.

## Plugin-to-feature map

| Plugin | What it powers in CampusEats |
|---|---|
| **WooCommerce** | Product catalog = menu items; each vendor's dishes become WooCommerce products with a "Vendor" taxonomy. Cart, checkout, and order objects map directly to the cart/checkout flow in the prototype. |
| **WooCommerce Multivendor add-on** (e.g. Dokan or WC Vendors — not in your original list but needed alongside WooCommerce) | Turns single-store WooCommerce into a multi-vendor marketplace so each of the 15+ vendors manages only their own menu and orders. |
| **MemberPress or UserPro** | Custom user roles (Customer, Vendor, Courier), NBU email-domain gating at registration, and the profile fields shown in each dashboard. |
| **WP Job Manager** | Powers the Courier "Available Orders" board — each ready order posts as a claimable "job" (delivery task) filtered by zone, and a courier "applying" = claiming the order. |
| **WP Google Maps** | Replaces the illustrative SVG node map with a real, geolocated map plugin using the coordinates already listed on the Delivery Zones page. |
| **OTP Verification plugin** | Generates and validates the delivery-confirmation code shown in the customer dashboard and entered by the courier — same flow as the prototype's OTP box. |
| **WPForms** | Contact page form and the FAQ/support requests. |

## Site structure (matches the prototype 1:1)

- Homepage — hero, stats, search, featured vendors, "How It Works" summary, testimonials
- Menu — WooCommerce shop page filtered by category (breakfast/lunch/dinner/snacks)
- Delivery Zones — WP Google Maps map + the 6 zone list with coordinates
- How It Works — static page
- About Us, Contact, Blog/News, FAQ — standard WordPress pages/posts
- Login / Register — MemberPress/UserPro forms, role selection (Student/Vendor/Courier), email-domain validation
- Customer Dashboard — MemberPress account page + WooCommerce "My Orders" + custom order-tracking template
- Vendor Dashboard — Dokan/WC Vendors dashboard (menu management, incoming orders, sales reports)
- Courier Dashboard — WP Job Manager "my listings/applications" view, customized for claim + OTP confirmation
- Terms, Privacy, Refund Policy — static legal pages

## Build order I'd recommend

1. Install WordPress + a lightweight, mobile-first theme (or a custom theme matching the palette/type below).
2. Install WooCommerce, then a multivendor add-on (Dokan or WC Vendors) so vendors get their own dashboards.
3. Install MemberPress or UserPro; create three roles (Customer, Vendor, Courier); add a registration-time email-domain check for `@nbu.edu.ng` / `@nigerianbritishuniversity.edu.ng`.
4. Install WP Job Manager; configure a custom "Delivery Job" post type fed automatically from new WooCommerce orders once a vendor marks them Ready (small custom function or Zapier/webhook).
5. Install and configure an OTP plugin on the order-completion step; store the OTP on the order meta; require it in the courier's "Confirm Delivery" action.
6. Install WP Google Maps; add the 6 zone markers using the coordinates already gathered in the prototype.
7. Install WPForms for Contact/Support.
8. Rebuild the static pages (Home, About, How It Works, Blog, FAQ, legal pages) using the copy from the prototype as a starting draft.
9. Apply the visual identity: deep green `#163A2B`, bright green `#2F8F5B`, gold `#F0A93E`, spice `#C1440C`, ivory `#FFF8EC` background; Clash Display for headings, General Sans for body text, IBM Plex Mono for order IDs/coordinates/OTP codes.

## Honest limitations to flag to your team
- Multivendor + job-board + membership + OTP is four separate plugin ecosystems stitched together — expect real custom-code glue (a few PHP snippets/hooks) to move an order automatically from "WooCommerce order placed" → "Job Manager listing" → "OTP-gated completion."
- None of the plugins listed natively do the automatic order-status handoff between vendor, courier, and customer — that's the part most worth budgeting developer time for.
- Payment gateway (Paystack/Flutterwave are the common choices in Nigeria) isn't in your plugin list but is required for WooCommerce checkout to actually process money.
