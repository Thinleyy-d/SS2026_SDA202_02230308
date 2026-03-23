1.PROBLEM STATEMENT ANALYSIS

The problem is about designing a system like a URL shortener (example: TinyURL).
What the system needs to do is take a long URL and turn it into a short one, and when someone clicks the short URL, it
should redirect them to the original. It also has to handle a huge amount of traffic without breaking down.
The key constraints are: 100 million URLs per day, more reads than writes (roughly 10 to 1), data needs to be kept for 10
years, and every short URL must be unique.
My understanding of this is that it is not just a coding problem. It is about building something that works in the real
world, handles billions of records, responds instantly, and does not crash under heavy load.


2.Analysis of Solutions

A. High-Level Design

The design uses two simple APIs: one POST request to create a short URL, and one GET request to redirect to the original.
This is clean, straightforward, and follows standard REST design.

B. URL Redirection

There are two ways to redirect:
301 Redirect is permanent. The browser saves it, so next time the user clicks the link, it does not even hit the server.
This is faster and uses less server resources.
302 Redirect is temporary. The request always goes through the server, which means you can track it. This is useful for
analytics.
So if the goal is speed, use 301. If the goal is tracking user behavior, use 302. Both have valid use cases depending on
what the product needs.

C. Data Storage

At first, a hash table might seem like an option, but that only lives in memory, which is expensive and not practical at
scale. A proper database with columns for ID, short URL, and long URL is the right approach for storing this kind of data
long-term.

D. Hashing (Most Important Part)

There are two approaches here.
The first is hash-based generation using something like MD5 or SHA-1. You take the long URL, hash it, and cut the result
down to 7 characters. The problem is that two different URLs can sometimes produce the same hash, which is called a
collision. Handling collisions means extra checks against the database, which slows things down.
The second approach is Base62 conversion, and this is the better one. Instead of hashing the URL itself, you assign a unique
ID to each record and then convert that ID into a short string using 62 characters (a to z, A to Z, and 0 to 9). Since every
ID is unique, there are no collisions, no extra checks needed, and it is much faster.

E. Performance Optimization

Using a cache (like Redis) makes a big difference. URLs that get clicked a lot are stored in memory, so the database does
not need to be hit every single time. This speeds things up and reduces load on the database significantly.

F. Scalability

To handle massive traffic, the system needs a load balancer to spread requests across multiple servers, database sharding to
split data into manageable chunks, replication so there are backup copies of data, and rate limiting to prevent abuse.


3.Things I Was Confused About

Some parts were not entirely clear to me:
How does the unique ID generator work when there are multiple servers running at the same time? If two servers generate the
same ID at the same moment, that is a problem.
How does sharding actually decide which piece of data goes to which database?
When do real companies actually choose 301 over 302, and what are the business reasons behind that choice?
If a URL changes or gets deleted, how does the cache know to update or remove it?


4.Topics I Want to Study More

After going through this, there are a few things I want to understand better: distributed systems and how they coordinate,
database sharding techniques, how caching systems like Redis work internally, load balancing strategies, and bloom filters
which were briefly mentioned.


5.Summary

This chapter walks through how to design a URL shortener that can realistically handle billions of requests. The author
starts by laying out the requirements clearly, then proposes a practical design using APIs, a database, and caching.
The most interesting part for me was the comparison between hashing and Base62 conversion. Base62 is clearly the smarter
choice because it ties the short URL to a unique ID rather than trying to hash the content, which avoids the whole collision
problem.
The overall design is solid and reflects how these systems are actually built in the industry. Some of the more advanced
topics like distributed ID generation and database scaling still need more digging into, but the chapter gives a good
foundation to build on.

