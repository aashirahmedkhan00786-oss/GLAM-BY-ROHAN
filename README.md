# Glam By Rohan – setup

## 1. Supabase (free)
1. Create a project at supabase.com.
2. SQL Editor > paste all of `supabase/schema.sql` > Run. (Creates tables, security rules, image bucket, categories.)
3. Authentication > Users > Add user: your admin email + a strong password (tick "Auto confirm").
4. Authentication > Providers > Email: turn OFF "Allow new users to sign up" (only you can create accounts).
5. SQL Editor, run (with your email) to make yourself the admin:
   `insert into admins(user_id) select id from auth.users where email='you@example.com';`
6. Settings > API: copy Project URL and anon public key.

## 2. Run locally
```
cp .env.example .env   # fill in the 3 values (VITE_WHATSAPP = 92 + number without 0, e.g. 923001234567)
npm install
npm run dev
```
Store: http://localhost:5173 · Admin: http://localhost:5173/admin

## 3. Deploy (Vercel, free)
Push to GitHub > vercel.com > Import > add the 3 env vars > Deploy. (`vercel.json` makes /admin work.) On Netlify, `public/_redirects` does the same.

## Security notes
- Admin rights live in the `admins` table, which has no public access. Row Level Security on products, categories, orders and the image bucket checks `is_admin()` in the database, so hiding the page is not what protects it.
- Customers cannot write to `orders` directly. They call `place_order()`, which recalculates prices and stock on the server.
- Email notifications: add a Supabase Database Webhook on `orders` INSERT pointing to an Edge Function that sends mail via Resend.
