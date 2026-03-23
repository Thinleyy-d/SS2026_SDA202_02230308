# Designing a URL Shortener – My Personal Analysis

## 1. Problem Statement

We need to design a system that can take a URL and turn it into a short one, like TinyURL. At first it seems easy, but when you think about all the details it gets complicated.

The system has to do two things:

- Take a long URL and give back a short one
- When someone visits the short URL, send them back to the original URL

### What the System Has to Handle

- It has to deal with 100 million new URLs every day
- For every one URL that is created, it is visited 10 times
- It has to run for 10 years, which means it will have to handle about 365 billion URLs
- It will need around 365 TB of storage space over that time
- The short URLs are made up of numbers and letters from 0–9, a–z, and A–Z
- Once a URL is stored it cannot be changed or deleted

This is not just about writing code. The real challenge is making something that can handle billions of records and still work fast and reliably.

---

## 2. Breaking Down the Solution

### API Design

We only need two endpoints:
```
POST /api/v1/data/shorten
Input:  the long URL
Output: the short URL

GET /api/v1/{shortUrl}
Output: the original long URL
```

This design is simple and does exactly what it needs to do.

---

### URL Redirection

When a user clicks on a link, the system has to decide how to redirect them. There are two options:

**301 – Permanent Redirect**
The browser remembers this the first time and does not need to ask the server again. This is good for performance, but we lose visibility into how often the link is being used.

**302 – Temporary Redirect**
Every time someone clicks on the link, it goes through the server. This is a bit slower, but we can track who clicked, when, and from where. This is better for understanding what is happening.

The choice depends on what is more important: speed or tracking.

---

### Data Storage

We cannot store everything in memory because it is expensive and does not survive if the system restarts. We need a database with a simple structure:

| Column   | Description                 |
|----------|-----------------------------|
| id       | A unique identifier         |
| shortURL | The short link we generated |
| longURL  | The original long URL       |

---

### Generating Short URLs

This is the most interesting part. There are two ways to do it:

#### Option 1: Hash + Collision Handling
Take the URL, run it through a hash function like MD5 or SHA-1, and then trim the result to 7 characters. The problem is that sometimes different URLs can produce the same hash, which causes problems and needs extra work to fix.

#### Option 2: Base62 Conversion
Instead of hashing the URL, give each entry a unique ID and convert that number into a short string using 62 characters. Since every ID is unique, there are no collision problems. A 7-character Base62 string can represent 3.5 trillion combinations, which is more than enough for 365 billion URLs.

This is clearly the better choice.

---

### How URL Shortening Works (Step by Step)

1. The user submits a URL
2. The system checks if the URL already exists in the database
3. If it does, the system returns the existing short URL
4. If it does not, the system generates a unique ID, converts it to Base62, stores it, and returns it

---

### How Redirection Works (Step by Step)

1. The user clicks on a short URL
2. The request goes to the load balancer
3. The system checks the cache first
   - If it is there, the system returns the long URL right away
   - If it is not there, the system queries the database, caches the result, and then returns it
4. The user is redirected to the original URL

---

### Making It Faster and More Reliable

A few things can make the system work better:

- **Caching** (like Redis) stores frequently used URLs in memory so the database is not hit every time
- **Load balancers** spread the traffic across multiple servers
- **Rate limiting** stops one user from using too much of the system
- **Database replication and sharding** keep reads fast and make the system work even if something fails

---

## 3. What I Found Unclear

There are a few things I want to understand better:

- **Distributed ID generation** — how do we make sure multiple servers do not generate the same ID at the same time?
- **Bloom filters** — I do not fully understand how they work for detecting duplicate URLs
- **Cache invalidation** — how does the cache know when to update if something changes?
- **301 vs 302 in practice** — how do real companies decide which one to use?

---

## 4. What I Want to Explore Next

- Distributed ID generation (like Snowflake ID)
- How Redis handles caching at a large scale
- Load balancing strategies (like round robin or least connections)
- Database sharding in practice
- CAP theorem and how it applies to systems like this

---

## 5. Summary

This analysis covers a practical way to build a URL shortener that can handle large-scale traffic. The design starts with the numbers and works backwards to figure out what the system needs, then builds up from APIs and storage to caching and traffic management.

The biggest decision is to use Base62 encoding with unique IDs instead of hashing URLs directly. It is a clean solution to what would otherwise be a complicated collision problem, and it works well for hundreds of billions of URLs.

Some of the more advanced parts — distributed ID generation, Bloom filters, and sharding strategy — still need more research. But the foundation here is solid and matches how these systems are actually built in the industry.