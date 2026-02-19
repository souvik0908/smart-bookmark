# Smart Bookmark App

A modern, real-time bookmark manager built to explore the "T3/SaaS Stack" using Next.js and Supabase. 

## 🚀 Live Demo
**URL:** [Insert your Vercel URL here once deployed]

## 🛠️ Tech Stack
* **Frontend/Framework:** Next.js 14 (App Router), React, Tailwind CSS
* **Backend/Database:** Supabase (PostgreSQL)
* **Authentication:** Supabase Auth (Google OAuth)
* **Real-time:** Supabase Realtime (WebSockets)

## ✨ Features
1. **Google OAuth Integration:** Secure, passwordless login using `@supabase/ssr` to manage session cookies.
2. **Private Bookmarks:** Enforced at the database level using PostgreSQL Row Level Security (RLS). Users can only view, create, and delete their own data.
3. **Real-Time Updates:** The UI syncs across multiple tabs instantly without page refreshes when a bookmark is added or deleted.

---

## 🧠 What Problems I Ran Into & How I Solved Them

Building a hybrid Server/Client application introduced some interesting state management challenges, specifically around real-time data syncing.

### Problem 1: The "Two Brains" Syncing Issue (Server State vs. Client State)
* **The Issue:** I used Next.js Server Actions to add bookmarks securely and revalidate the page path. However, my React Client Component (which held the bookmarks in a `useState` array for real-time manipulation) was ignoring these fresh server updates after the initial load.
* **The Solution:** I implemented a `useEffect` hook in the Client Component that actively listens for changes to the `initialBookmarks` prop sent from the server. If the server sends a newly fetched list after a form submission, React seamlessly updates its client-side state to match:
  ```javascript
  useEffect(() => {
    setBookmarks(initialBookmarks)
  }, [initialBookmarks])
  ```

Problem 2: Duplicate Real-Time Renders
The Issue: Because I was fetching the updated list from the server and listening for INSERT events via the Supabase WebSocket, adding a bookmark locally would sometimes cause the UI to briefly render the new bookmark twice.

The Solution: I added a duplication check inside the real-time payload listener. Before adding an incoming WebSocket payload to the state array, the code checks if the bookmark ID already exists in the current state:

JavaScript
if (prev.some((b) => b.id === payload.new.id)) return prev;
Problem 3: Enabling Database Broadcasts
The Issue: Initially, my delete and insert actions were updating the database, but the WebSocket wasn't picking them up in my second browser tab.

The Solution: I discovered that Supabase disables real-time broadcasts by default to save server resources. I had to manually go into the Supabase Database Replication settings and enable Insert, Update, and Delete broadcasts specifically for the bookmarks table.

💻 Local Setup
If you want to run this project locally:

Clone the repository.

Run npm install.

Create a .env.local file in the root directory and add your Supabase keys:

Code snippet
```
NEXT_PUBLIC_SUPABASE_URL=your_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
```
Run the SQL script provided in the Supabase SQL editor to create the bookmarks table and apply RLS policies.

Run npm run dev to start the local development server at http://localhost:3000.


---

Once you push this, your GitHub repository will look highly professional and exactly address the assignment's grading criteria. 

Would you like me to guide you through the final step of deploying this project to Ve
