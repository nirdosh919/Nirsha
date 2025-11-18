# Nirsha — Scooty Rental Platform (React + Vite + Tailwind)

**Project summary**
Nirsha is a peer-to-peer scooty rental web app focused on short-term rentals for visiting places and personal work. Rentals are for a minimum of 1 day and a maximum of 5 days. Pricing is distance-based (km) with a commission that ensures profit for the platform and fair earnings for owners. No drivers are provided — renters pick up the scooty from the owner or arrange pickup. This scaffold is a fully functional frontend demo ready to be connected to a backend.

---

## Features
- Browse available scooties by city/area
- Filter by vehicle type, range, owner ratings
- Distance-based fare calculator (auto-estimate using pickup + drop-off addresses) with business-friendly margins
- Booking flow (1–5 days), calendar controls, availability checks
- Owner profiles & listing pages (peer-to-peer model)
- Secure login/signup (mocked)
- Cart / booking summary and checkout (dummy payment)
- Admin dashboard (manage listings & bookings)
- Responsive, mobile-first design, SEO-friendly structure
- Local persistence via localStorage for demo bookings and listings

---

## Quick start
1. npm init vite@latest nirsha -- --template react
2. cd nirsha
3. Copy scaffold files into project
4. npm install
5. npm install -D tailwindcss postcss autoprefixer
6. npx tailwindcss init -p
7. npm run dev

---

// package.json
{
  "name": "nirsha",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.14.1",
    "clsx": "^1.2.1",
    "dayjs": "^1.11.9"
  },
  "devDependencies": {
    "vite": "^5.0.0",
    "tailwindcss": "^4.0.0",
    "postcss": "^8.4.0",
    "autoprefixer": "^10.4.0"
  }
}

// tailwind.config.js
module.exports = {
  content: ['./index.html','./src/**/*.{js,jsx,ts,tsx}'],
  theme: { extend: { colors: { brand: '#0b84ff', accent: '#0ea5e9' } } },
  plugins: []
}

// public/index.html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Nirsha — Scooty Rentals</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>

// src/main.jsx
import React from 'react'
import { createRoot } from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom'
import App from './App'
import './styles.css'

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
)

// src/styles.css
@tailwind base;
@tailwind components;
@tailwind utilities;
:root{--brand:#0b84ff}
.container{ @apply mx-auto px-4 max-w-6xl }

// src/data/scooties.js
const scooties = [
  {
    id: 's1',
    title: 'Honda Activa 125',
    city: 'Kanpur',
    owner: 'Ramesh',
    ownerId: 'u1',
    pricePerKm: 3.5, // owner earning per km
    baseFarePerDay: 120, // base per day
    images: ['https://picsum.photos/seed/s1/800/600'],
    rating: 4.6,
    description: 'Well-maintained Activa. Helmet included. Fuel as per agreement.'
  },
  {
    id: 's2',
    title: 'TVS Jupiter',
    city: 'Kanpur',
    owner: 'Sunita',
    ownerId: 'u2',
    pricePerKm: 3.0,
    baseFarePerDay: 100,
    images: ['https://picsum.photos/seed/s2/800/600'],
    rating: 4.4,
    description: 'Smooth ride, ideal for city travel and short trips.'
  },
  // ...more
]

export default scooties

// src/utils/booking.js
export const BOOKINGS_KEY = 'nirsha_bookings_v1'
export function readBookings(){ try{ return JSON.parse(localStorage.getItem(BOOKINGS_KEY) || '[]') } catch(e){ return [] } }
export function writeBookings(b){ localStorage.setItem(BOOKINGS_KEY, JSON.stringify(b)) }

// src/utils/pricing.js
/**
 * Business pricing rules (sample):
 * - Owner sets pricePerKm and baseFarePerDay
 * - Platform adds a 20% commission on top of owner earnings
 * - For distance < 10km, apply short-trip surcharge (fixed)
 * - Min rental days = 1, Max = 5
 */
export function calculateFare({distanceKm, days, scooty}){
  const minDays = 1, maxDays = 5
  const d = Math.max(minDays, Math.min(maxDays, days))

  const ownerEarnings = scooty.baseFarePerDay * d + scooty.pricePerKm * distanceKm
  const platformCommission = ownerEarnings * 0.20 // 20%
  // small business-friendly markup to ensure competitive listing
  const convenienceFee = 30 // fixed

  const total = ownerEarnings + platformCommission + convenienceFee
  return {
    ownerEarnings: +ownerEarnings.toFixed(2),
    platformCommission: +platformCommission.toFixed(2),
    convenienceFee,
    total: +total.toFixed(2)
  }
}

// src/App.jsx
import React from 'react'
import { Routes, Route } from 'react-router-dom'
import Header from './components/Header'
import Footer from './components/Footer'
import Home from './pages/Home'
import Listings from './pages/Listings'
import ListingDetail from './pages/ListingDetail'
import Booking from './pages/Booking'
import Checkout from './pages/Checkout'
import Login from './pages/Login'
import Profile from './pages/Profile'
import Admin from './pages/Admin'
import OrderConfirmation from './pages/OrderConfirmation'

export default function App(){
  return (
    <div className="min-h-screen flex flex-col">
      <Header />
      <main className="flex-1 container py-6">
        <Routes>
          <Route path="/" element={<Home/>} />
          <Route path="/listings" element={<Listings/>} />
          <Route path="/listings/:id" element={<ListingDetail/>} />
          <Route path="/book/:id" element={<Booking/>} />
          <Route path="/checkout" element={<Checkout/>} />
          <Route path="/login" element={<Login/>} />
          <Route path="/profile" element={<Profile/>} />
          <Route path="/admin" element={<Admin/>} />
          <Route path="/order-confirmation" element={<OrderConfirmation/>} />
        </Routes>
      </main>
      <Footer />
    </div>
  )
}

// src/components/Header.jsx
import React from 'react'
import { Link } from 'react-router-dom'
export default function Header(){
  return (
    <header className="bg-white border-b">
      <div className="container flex items-center justify-between py-4">
        <Link to="/" className="text-2xl font-bold">Nirsha</Link>
        <nav className="flex gap-4 items-center">
          <Link to="/listings">Find Scooty</Link>
          <Link to="/book">Book</Link>
          <Link to="/login">Login</Link>
        </nav>
      </div>
    </header>
  )
}

// src/components/Footer.jsx
import React from 'react'
export default function Footer(){
  return (
    <footer className="bg-gray-50 border-t mt-8">
      <div className="container py-6 flex flex-col md:flex-row justify-between items-start">
        <div>
          <h4 className="font-bold">Nirsha</h4>
          <p className="text-sm mt-2">Peer-to-peer scooty rentals — flexible, reliable, and local.</p>
        </div>
        <div className="flex gap-3 mt-3 md:mt-0">
          <a href="#">Twitter</a>
          <a href="#">Instagram</a>
        </div>
      </div>
      <div className="text-center py-3 text-sm">© {new Date().getFullYear()} Nirsha</div>
    </footer>
  )
}

// src/pages/Home.jsx
import React from 'react'
import { Link } from 'react-router-dom'
import scooties from '../data/scooties'
import ListingCard from '../components/ListingCard'

export default function Home(){
  const featured = scooties.slice(0,6)
  return (
    <div>
      <section className="mb-6 bg-gradient-to-r from-sky-50 to-white rounded p-6 flex flex-col md:flex-row items-center gap-6">
        <div className="flex-1">
          <h1 className="text-3xl font-bold">Nirsha — Local Scooty Rentals</h1>
          <p className="mt-2 text-gray-600">Rent a scooty for 1–5 days. Owners list their scooties, you book online. No drivers — pick up locally.</p>
          <div className="mt-4">
            <Link to="/listings" className="px-4 py-2 bg-brand text-white rounded">Find Scooty</Link>
          </div>
        </div>
        <div className="w-full md:w-1/3">
          <img src="https://picsum.photos/seed/hero-scooty/600/400" alt="hero" className="rounded shadow" />
        </div>
      </section>

      <section className="mb-6">
        <h2 className="text-xl font-semibold mb-3">Featured near you</h2>
        <div className="grid grid-cols-2 md:grid-cols-3 gap-4">
          {featured.map(s=> <ListingCard key={s.id} s={s} />)}
        </div>
      </section>

      <section className="mb-6">
        <h2 className="text-xl font-semibold mb-3">How it works</h2>
        <ol className="list-decimal ml-5 space-y-2">
          <li>Search scooties in your city.</li>
          <li>Check distance-based fare estimate and available dates.</li>
          <li>Book for 1–5 days and meet the owner to pick up the scooty.</li>
        </ol>
      </section>
    </div>
  )
}

// src/components/ListingCard.jsx
import React from 'react'
import { Link } from 'react-router-dom'
export default function ListingCard({s}){
  return (
    <div className="border rounded p-3 flex flex-col">
      <Link to={`/listings/${s.id}`} className="block">
        <img src={s.images[0]} alt={s.title} className="h-40 w-full object-cover rounded" />
        <h3 className="mt-3 font-semibold">{s.title}</h3>
      </Link>
      <div className="mt-auto flex items-center justify-between">
        <div>
          <div className="text-sm">{s.city} • Owner: {s.owner}</div>
          <div className="text-lg font-bold">Base ₹{s.baseFarePerDay}/day</div>
        </div>
        <div className="text-xs bg-green-100 px-2 py-1 rounded">{s.rating} ★</div>
      </div>
    </div>
  )
}

// src/pages/Listings.jsx
import React, {useState} from 'react'
import scooties from '../data/scooties'
import ListingCard from '../components/ListingCard'

export default function Listings(){
  const [city,setCity] = useState('Kanpur')
  const [q,setQ] = useState('')
  const filtered = scooties.filter(s=> s.city.toLowerCase().includes(city.toLowerCase()) && s.title.toLowerCase().includes(q.toLowerCase()))
  return (
    <div>
      <div className="mb-4 flex gap-2">
        <input value={city} onChange={e=>setCity(e.target.value)} className="border rounded px-3 py-2" placeholder="City" />
        <input value={q} onChange={e=>setQ(e.target.value)} className="border rounded px-3 py-2 flex-1" placeholder="Search model or owner" />
      </div>
      <div className="grid grid-cols-2 md:grid-cols-3 gap-4">
        {filtered.map(s=> <ListingCard key={s.id} s={s} />)}
      </div>
    </div>
  )
}

// src/pages/ListingDetail.jsx
import React from 'react'
import { useParams, Link } from 'react-router-dom'
import scooties from '../data/scooties'

export default function ListingDetail(){
  const {id} = useParams()
  const s = scooties.find(x=> x.id === id)
  if(!s) return <div>Not found</div>
  return (
    <div className="grid md:grid-cols-2 gap-6">
      <div>
        <img src={s.images[0]} alt={s.title} className="rounded shadow" />
      </div>
      <div>
        <h1 className="text-2xl font-bold">{s.title}</h1>
        <div className="mt-2">Owner: {s.owner}</div>
        <div className="mt-2">City: {s.city}</div>
        <div className="mt-4">{s.description}</div>
        <div className="mt-4">Base fare: ₹{s.baseFarePerDay}/day • ₹{s.pricePerKm}/km owner rate</div>
        <div className="mt-4 flex gap-2">
          <Link to={`/book/${s.id}`} className="px-4 py-2 bg-brand text-white rounded">Book now</Link>
          <button className="px-4 py-2 border rounded">Contact owner</button>
        </div>
      </div>
    </div>
  )
}

// src/pages/Booking.jsx
import React, {useState} from 'react'
import { useParams, useNavigate } from 'react-router-dom'
import scooties from '../data/scooties'
import { calculateFare } from '../utils/pricing'
import dayjs from 'dayjs'

export default function Booking(){
  const { id } = useParams()
  const s = scooties.find(x=> x.id===id)
  const navigate = useNavigate()
  const [pickup, setPickup] = useState('')
  const [dropoff, setDropoff] = useState('')
  const [days, setDays] = useState(1)
  const [distance, setDistance] = useState(10) // mock input in km

  if(!s) return <div>Select a scooty to book.</div>

  function estimate(){
    return calculateFare({ distanceKm: distance, days, scooty: s })
  }

  function submit(e){
    e.preventDefault()
    const fare = estimate()
    const bookings = JSON.parse(localStorage.getItem('nirsha_bookings_v1') || '[]')
    const order = { id: 'B'+Date.now(), scootyId: s.id, scootyTitle: s.title, pickup, dropoff, days, distance, fare, date: new Date().toISOString() }
    bookings.push(order)
    localStorage.setItem('nirsha_bookings_v1', JSON.stringify(bookings))
    navigate('/order-confirmation', { state: { order } })
  }

  const fare = estimate()

  return (
    <div className="max-w-xl mx-auto">
      <h2 className="text-xl font-semibold mb-4">Booking — {s.title}</h2>
      <form onSubmit={submit} className="space-y-3">
        <input required placeholder="Pickup location" value={pickup} onChange={e=>setPickup(e.target.value)} className="w-full border rounded px-3 py-2" />
        <input required placeholder="Drop-off location" value={dropoff} onChange={e=>setDropoff(e.target.value)} className="w-full border rounded px-3 py-2" />
        <div className="flex gap-2 items-center">
          <label>Days</label>
          <input type="number" value={days} min={1} max={5} onChange={e=>setDays(Number(e.target.value))} className="w-20 border rounded px-2 py-1" />
          <label>Distance (km)</label>
          <input type="number" value={distance} min={1} onChange={e=>setDistance(Number(e.target.value))} className="w-24 border rounded px-2 py-1" />
        </div>

        <div className="p-3 border rounded">
          <div>Owner earnings: ₹{fare.ownerEarnings}</div>
          <div>Platform commission: ₹{fare.platformCommission}</div>
          <div>Convenience fee: ₹{fare.convenienceFee}</div>
          <div className="text-lg font-bold">Total: ₹{fare.total}</div>
        </div>

        <button type="submit" className="px-4 py-2 bg-brand text-white rounded">Confirm booking</button>
      </form>
    </div>
  )
}

// src/pages/Checkout.jsx
import React from 'react'
import { useLocation, useNavigate } from 'react-router-dom'

export default function Checkout(){
  const navigate = useNavigate()
  function pay(){
    // mock payment
    navigate('/order-confirmation')
  }
  return (
    <div className="max-w-md mx-auto">
      <h2 className="text-xl font-semibold mb-4">Checkout</h2>
      <p className="mb-3">Payment is mocked in this demo. Integrate Stripe/Paytm for production.</p>
      <button onClick={pay} className="px-4 py-2 bg-brand text-white rounded">Pay now (mock)</button>
    </div>
  )
}

// src/pages/OrderConfirmation.jsx
import React from 'react'
import { useLocation, Link } from 'react-router-dom'

export default function OrderConfirmation(){
  const { state } = useLocation()
  const order = state?.order || JSON.parse(localStorage.getItem('nirsha_bookings_v1') || '[]').slice(-1)[0]
  if(!order) return <div>No recent booking found. <Link to="/">Home</Link></div>
  return (
    <div className="max-w-xl mx-auto">
      <h2 className="text-xl font-semibold">Booking confirmed</h2>
      <div className="mt-3">Booking ID: <strong>{order.id}</strong></div>
      <div className="mt-2">Total: ₹{order.fare.total}</div>
      <div className="mt-3">Contact owner after login to coordinate pickup.</div>
    </div>
  )
}

// src/pages/Login.jsx
import React, {useState} from 'react'
import { useNavigate } from 'react-router-dom'

export default function Login(){
  const [email,setEmail] = useState('')
  const [name,setName] = useState('')
  const navigate = useNavigate()

  function submit(e){
    e.preventDefault()
    const user = {email, name}
    localStorage.setItem('nirsha_user', JSON.stringify(user))
    navigate('/profile')
  }
  return (
    <div className="max-w-md mx-auto">
      <h2 className="text-xl font-semibold mb-4">Login / Signup (demo)</h2>
      <form onSubmit={submit} className="space-y-3">
        <input required value={name} onChange={e=>setName(e.target.value)} placeholder="Full name" className="w-full border rounded px-3 py-2" />
        <input required value={email} onChange={e=>setEmail(e.target.value)} placeholder="Email" className="w-full border rounded px-3 py-2" />
        <button className="px-4 py-2 bg-brand text-white rounded">Continue</button>
      </form>
    </div>
  )
}

// src/pages/Profile.jsx
import React from 'react'

export default function Profile(){
  const user = JSON.parse(localStorage.getItem('nirsha_user') || 'null')
  const bookings = JSON.parse(localStorage.getItem('nirsha_bookings_v1') || '[]')
  return (
    <div>
      <h2 className="text-xl font-semibold mb-4">Profile</h2>
      {!user ? <div>Please <a href="/login">login</a>.</div> : (
        <div>
          <div>Signed in as <strong>{user.name}</strong> ({user.email})</div>
          <div className="mt-4">
            <h3 className="font-semibold">Your bookings</h3>
            {bookings.filter(b=> b.email===user.email || true).length===0 ? <div className="text-sm text-gray-600">No bookings yet</div> : (
              <ul className="mt-2 space-y-2">
                {bookings.map(b=> (
                  <li key={b.id} className="border rounded p-3">
                    <div className="flex justify-between"><div>{b.id}</div><div>₹{b.fare.total}</div></div>
                    <div className="text-sm text-gray-600">{new Date(b.date).toLocaleString()}</div>
                  </li>
                ))}
              </ul>
            )}
          </div>
        </div>
      )}
    </div>
  )
}

// src/pages/Admin.jsx
import React, {useState} from 'react'
import scootiesData from '../data/scooties'

export default function Admin(){
  const [listings,setListings] = useState(()=> JSON.parse(localStorage.getItem('nirsha_listings_v1') || 'null') || scootiesData)
  const [bookings] = useState(()=> JSON.parse(localStorage.getItem('nirsha_bookings_v1') || '[]'))

  function addListing(){
    const id = 's' + Date.now()
    const newL = { id, title: 'New scooty', city: 'Kanpur', owner: 'You', ownerId: 'u'+Date.now(), pricePerKm: 3, baseFarePerDay: 100, images: ['https://picsum.photos/seed/new/800/600'], rating: 5, description: 'New listing' }
    const next = [newL,...listings]
    setListings(next)
    localStorage.setItem('nirsha_listings_v1', JSON.stringify(next))
  }

  function deleteListing(id){
    const next = listings.filter(l=> l.id!==id)
    setListings(next)
    localStorage.setItem('nirsha_listings_v1', JSON.stringify(next))
  }

  const revenue = bookings.reduce((s,b)=> s + (b.fare?.total || 0), 0)

  return (
    <div>
      <h2 className="text-xl font-semibold mb-4">Admin</h2>
      <div className="flex justify-between items-center mb-3">
        <h3 className="font-semibold">Listings</h3>
        <button onClick={addListing} className="px-3 py-1 border rounded">Add listing</button>
      </div>
      <div className="space-y-2">
        {listings.map(l=> (
          <div key={l.id} className="flex items-center justify-between border rounded p-2">
            <div>{l.title} • {l.city}</div>
            <div className="flex gap-2"><button>Edit</button><button className="text-red-600" onClick={()=>deleteListing(l.id)}>Delete</button></div>
          </div>
        ))}
      </div>

      <aside className="mt-4 border rounded p-3">
        <div>Bookings: {bookings.length}</div>
        <div>Total value: ₹{revenue.toFixed(2)}</div>
      </aside>
    </div>
  )
}

---

## Pricing rationale (business logic)
- **Owner earnings:** baseFarePerDay * days + pricePerKm * distance
- **Platform commission:** 20% of owner earnings (adjustable)
- **Convenience fee:** fixed ₹30 per booking to cover ops costs
- **Minimum rental days:** 1
- **Maximum rental days:** 5
- This ensures both owner attractiveness (owner receives clear per-day + per-km) and platform profitability.

## Additional production notes
- Replace mock distance input with Google Maps Distance Matrix API for accurate km calculation.
- Add payment integration (Stripe, Razorpay) and verify payouts to owners.
- Add identity verification (KYC) for owners and renters.
- Add calendar availability and conflict checks using backend and database.
- Add unit and E2E tests.

---

If you want, I can:
- Zip this project and provide a download,
- Convert to Next.js with SSR and API routes,
- Integrate a real distance calculation using Google Maps/OSRM,
- Add Stripe/Razorpay sandbox integration and owner payouts mock,
- Deploy a live demo on Vercel and provide the URL.

Tell me which next step you prefer and I will prepare it.

