UNDERSTANDING THE PROBLEM

- The problem is about creating a system like a URL shortener for example TinyURL. This system takes an URL and turns it into a short one. 
When someone clicks the URL it should take them to the original URL. It also has to handle an amount of traffic without breaking down. 
The system needs to deal with 100 million URLs per day. It has reads than writes, about 10 to 1. 
The data needs to be kept for 10 years. Every short URL must be unique.

- This is about building something that works in the world. It has to handle billions of records respond instantly and not crash under load. The
URL shortener system is like a machine that helps people use shorter URLs.

LOOKING AT SOLUTIONS

1. High-Level Design

- The design uses two APIs, one is a POST request to create an URL while the other one is a GET request to redirect to the URL. This is clean and
straightforward. It follows REST design. The APIs are like two doors. One door lets you in to create an URL. The other door takes you to the URL.

2. URL Redirection

- There are two ways to redirect. One is 301 Redirect. This is permanent. The browser saves it. This is faster. Uses less server resources. The
other is 302 Redirect. This is temporary. The request always goes through the server. This means you can track it. This is useful for analytics.
If the goal is speed use 301. If the goal is tracking user behavior use 302. Both have use cases depending on what the product needs.

- For example if you want people to go to your website quickly you can use 301.. If you want to know how many people are going to your website you
can use 302.

3. Data Storage

- At first a hash table might seem like an option.. That only lives in memory, which is expensive and not practical at scale. A proper database
with columns for ID, short URL and long URL is the approach for storing this kind of data long-term. The database is like a box that stores all the
URLs.

4. Hashing

- There are two approaches. The first is hash-based generation. You take the URL hash it and cut the result down to 7 characters. The problem is
that two different URLs can sometimes produce the hash, which is called a collision. Handling collisions means checks against the database, which
slows things down.

- The second approach is Base62 conversion. This is the one. Of hashing the URL itself you assign a unique ID to each record and then convert that
ID into a short string using 62 characters. Since every ID is unique there are no collisions no checks needed and it is much faster. Base62 is
like a code that makes sure every URL is unique.

5. Performance Optimization

- Using a cache makes a difference. URLs that get clicked a lot are stored in memory. The database does not need to be hit every single time. This
speeds things up. Reduces load on the database significantly. The cache is like a box that stores the most used URLs.

6. Scalability

- To handle traffic the system needs a load balancer to spread requests across multiple servers database sharding to split data into manageable
chunks replication so there are backup copies of data and rate limiting to prevent abuse. The load balancer is like a manager that makes sure all
the servers are working together.

THINGS I WAS CONFUSED ABOUT

- Some parts were not entirely clear to me. How does the unique ID generator work when there are servers running at the same time? If two servers
generate the ID at the same moment that is a problem. I also want to know how sharding actually decides which piece of data goes to which database.

- When do real companies actually choose 301 over 302. What are the business reasons behind that choice? If a URL changes or gets deleted how does
the cache know to update or remove it? These are all questions that I need to find answers to.

TOPICS I WANT TO STUDY MORE

- After going through this there are a things I want to understand better. I want to learn more about distributed systems and how they coordinate.
I also want to know more about database sharding techniques. How caching systems like Redis work internally is also something I want to study.

- I want to learn more about load balancing strategies and bloom filters. These are all topics that can help me build a better URL shortener
system.

SUMMARY

- This chapter walks through how to design a URL shortener that can realistically handle billions of requests. The author starts by laying out the
requirements then proposes a practical design using APIs, a database and caching. The interesting part for me was the comparison between hashing
and Base62 conversion. Base62 is clearly the choice because it ties the short URL to a unique ID rather than trying to hash the content, which
avoids the whole collision problem.

- The overall design is solid. Reflects how these systems are actually built in the industry. Some of the advanced topics like distributed ID
generation and database scaling still need more digging, into but the chapter gives a good foundation to build on. I can use this knowledge to
build my URL shortener system that can handle a huge amount of traffic.


