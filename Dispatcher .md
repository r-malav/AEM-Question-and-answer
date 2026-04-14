The **AEM Dispatcher** is Adobe Experience Manager's caching and security layer. It is an Apache (or IIS) module that sits in front of the AEM Publish instance.

---

### Part 1: Detailed Explanation

#### Core Functions:
1.  **Caching:** Stores static and dynamic content as HTML files on the file system. Subsequent requests for the same page are served at lightning speed without hitting the AEM Publisher.
2.  **Load Balancing:** Distributes user requests across multiple AEM Publish instances.
3.  **Security (Filtering):** Acts as a firewall, blocking access to sensitive AEM areas (like `/system/console` or `/crx/de`).
4.  **Sticky Sessions:** Ensures a user stays on the same Publish instance during a session (important for personalized content).

#### How it Works:
1.  **Request Arrival:** A user requests `/content/mysite/en.html`.
2.  **Cache Check:** Dispatcher checks if the file exists in its docroot.
    *   **If Yes (Cache Hit):** It checks if the file is "statistically" valid. If valid, it serves the file.
    *   **If No (Cache Miss):** It forwards the request to the AEM Publisher.
3.  **AEM Response:** Publisher sends the HTML back.
4.  **Caching Logic:** If the request is cacheable, Dispatcher saves the file and returns it to the user.

---

### Part 2: Implementation (Configuration Structure)

In modern AEM (and AEM Cloud Service), the Dispatcher is configured using a specific folder structure:

*   **`conf.d/`**: Apache global settings.
*   **`conf.dispatcher.d/`**: The core Dispatcher logic.
    *   **`filters/`**: Security rules.
    *   **`cache/`**: What to cache and what to ignore.
    *   **`virtualhosts/`**: Maps domains to AEM paths.
    *   **`renders/`**: Defines the backend AEM Publish instances.

#### Example Filter Implementation (`filters.any`):
```apache
/filter {
    # 1. Block everything first (Deny by default)
    /0001 { /type "deny" /glob "*" }
    
    # 2. Allow content paths
    /0023 { /type "allow" /url "/content/*" }
    
    # 3. Allow Clientlibs
    /0041 { /type "allow" /url "/etc.clientlibs/*" }
    
    # 4. Deny access to sensitive admin paths
    /0061 { /type "deny" /url "/system/*" }
}
```

#### Example Cache Rule (`cache.any`):
```apache
/cache {
    /docroot "/var/www/html"
    /rules {
        /0000 { /glob "*" /type "allow" }
        /0001 { /glob "/content/forms/*" /type "deny" } # Don't cache forms
    }
    /invalidate {
        /0000 { /glob "*" /type "allow" }
    }
}
```

---

### Part 3: 100 Interview Questions & Answers

#### Basics (1-20)
1. **What is the Dispatcher<br />Answer  :   An Apache/IIS module for caching and security.
2. **Where does it sit<br />Answer  :   Between the User/CDN and the AEM Publisher.
3. **Can it be used with Author<br />Answer  :   No, it is strictly for Publish (except for rare debugging cases).
4. **Primary benefits<br />Answer  :   Performance and Security.
5. **What is a "Cache Hit"<br />Answer  :   When the requested file is served directly from the Dispatcher's disk.
6. **What is a "Cache Miss"<br />Answer  :   When Dispatcher must fetch the page from AEM.
7. **What is the "Docroot"<br />Answer  :   The physical directory on the webserver where cached files are stored.
8. **What is `dispatcher.any`<br />Answer  :   The main configuration file.
9. **What is a "Farm"<br />Answer  :   A set of configurations for a specific website or group of sites.
10. **Explain Load Balancing in Dispatcher.** It uses the `/renders` section to distribute traffic.
11. **What are "Sticky Sessions"<br />Answer  :   Ensuring one user stays on one AEM node to maintain session data.
12. **What happens if the Publisher is down<br />Answer  :   Dispatcher serves cached content if available; otherwise, a 502/503 error.
13. **Can Dispatcher cache JSON<br />Answer  :   Yes, if the headers and extensions are configured correctly.
14. **What is the default port for Dispatcher-to-AEM communication<br />Answer  :   Usually 4503.
15. **What is a "Virtual Host"<br />Answer  :   A configuration that allows one Apache instance to handle multiple domains.
16. **How does Dispatcher identify a unique request<br />Answer  :   By URL, Extension, and sometimes Query Params.
17. **What is the `.stat` file<br />Answer  :   A file used to determine the age of cached content for invalidation.
18. **What is the "Statslevel"<br />Answer  :   Determines how many levels of folders are invalidated when a `.stat` file is updated.
19. **What is `/clientheaders`<br />Answer  :   Defines which HTTP headers are passed from the user to AEM.
20. **Can you have multiple Farms<br />Answer  :   Yes.

#### Caching & Invalidation (21-40)
21. **How to invalidate the cache<br />Answer  :   Via Flush Agents on AEM Publisher or manual deletion.
22. **What is a Flush Agent<br />Answer  :   A service on AEM that sends an HTTP request to Dispatcher to clear cache.
23. **What is the "Allow-Authorized" property<br />Answer  :   If 1, it caches content even if an authentication header is present.
24. **How to ignore query parameters in cache<br />Answer  :   Use `/ignoreUrlParams`.
    ```apache
    /ignoreUrlParams {
        /0001 { /glob "*" /type "deny" }
        /0002 { /glob "q" /type "allow" } # 'q' param will trigger new cache
    }
    ```
25. **Why shouldn't you cache pages with sensitive data<br />Answer  :   Because it would be served to all users regardless of permissions.
26. **What is "TTL" (Time To Live)<br />Answer  :   A feature to expire cache after a specific time (requires `mod_expires`).
27. **How to cache only specific extensions<br />Answer  :   In `/rules`, allow only `.html` or `.jpg`.
28. **What is "Statfile-level"<br />Answer  :   Defines how deep the `.stat` file affects subdirectories.
29. **Difference between `invalidate` and `rules`<br />Answer  :   `rules` define what is cached; `invalidate` defines what is deleted on a flush.
30. **What is "Serve Stale Content"<br />Answer  :   Serving an old cached file while the Publisher is busy updating it.
31. **What is `/gracePeriod`<br />Answer  :   The time Dispatcher continues to serve stale content after invalidation.
32. **Does Dispatcher cache POST requests<br />Answer  :   No, only GET (and sometimes HEAD).
33. **How does the `.stat` file work<br />Answer  :   If the cached file is older than the `.stat` file, it is considered invalid.
34. **How to cache 404 pages<br />Answer  :   Use Apache `ErrorDocument` or configure specific cache rules.
35. **What is the impact of a high Statfile-level<br />Answer  :   More precision, but more `.stat` files created.
36. **How to clear the entire cache<br />Answer  :   Delete all files in the docroot and touch the `.stat` file.
37. **Can Dispatcher handle SSL<br />Answer  :   Yes, but it's usually handled by the Apache module `mod_ssl`.
38. **What is "Vanity URL" caching<br />Answer  :   A specific configuration to allow AEM vanity URLs to be cached.
39. **How to prevent caching of a specific page<br />Answer  :   Send a `Cache-Control: no-cache` header from AEM.
40. **How to debug cache misses<br />Answer  :   Check `dispatcher.log` and look for "request not cacheable" messages.

#### Security & Filters (41-60)
41. **What is the `/filter` section<br />Answer  :   It defines which URLs Apache is allowed to forward to AEM.
42. **Why is `/0001 { /type "deny" /glob "*" }` important<br />Answer  :   It follows the security best practice of "Deny All" first.
43. **How to block access to CRXDE<br />Answer  :   `/filter { /0010 { /type "deny" /url "/crx/*" } }`
44. **What is the difference between `/glob` and `/url` in filters<br />Answer  :   `/url` is for path matching; `/glob` is for general pattern matching.
45. **How to allow only specific query strings<br />Answer  :   Use the `/query` property in a filter rule.
46. **What is `/method` in filters<br />Answer  :   Restricts requests based on GET, POST, etc.
47. **How to protect against DoS<br />Answer  :   Use Apache modules like `mod_evasive` alongside Dispatcher filters.
48. **Should you allow `/bin/*`<br />Answer  :   Only for specific servlets; otherwise, it's a security risk.
49. **How to handle Dispatcher Security on AEM Cloud<br />Answer  :   Use the provided `filter.any` in the SDK.
50. **What is the `/selectors` filter<br />Answer  :   Allows you to block dangerous selectors like `.infinity.json`.
51. **How to allow AJAX requests<br />Answer  :   Ensure the `.json` extension and the specific path are allowed in filters.
52. **How to block "query-heavy" URLs<br />Answer  :   Use filters to deny paths containing specific characters like `?` or `&`.
53. **What is the risk of caching `/content/userinfo.html`<br />Answer  :   Data leakage; user A sees user B's information.
54. **How to allow images<br />Answer  :   `/filter { /0030 { /type "allow" /extension '(jpg|jpeg|png|gif)' } }`
55. **Is Dispatcher a Web Application Firewall (WAF)<br />Answer  :   No, but it provides basic WAF-like filtering.
56. **What is the `/protocol` filter<br />Answer  :   Restricts requests to HTTP or HTTPS.
57. **How to test a filter rule<br />Answer  :   Use `curl` to try and access the blocked path.
58. **Does Dispatcher support Regex in filters<br />Answer  :   Yes, in modern versions using the `/url` or `/path` properties.
59. **How to allow the "Dispatcher Flush" request<br />Answer  :   Allow the specific IP of the Publisher in Apache and Dispatcher filters.
60. **What is the `/sessionmanagement` section<br />Answer  :   Used for managing authenticated sessions at the Dispatcher level.

#### Configuration & Directives (61-80)
61. **What is `/renders`<br />Answer  :   Defines the IP/Hostname and Port of the Publish instances.
62. **What is `/timeout` in renders<br />Answer  :   How long Dispatcher waits for AEM to respond.
63. **What is `/retryDelay`<br />Answer  :   The time to wait before retrying a failed connection to a render.
64. **What is `/numberOfRetries`<br />Answer  :   Maximum attempts to connect to a render.
65. **What is the `/health_check`<br />Answer  :   A URL used to see if an AEM instance is "alive."
66. **What is the `/failover` property<br />Answer  :   Controls how requests are re-routed if a render fails.
67. **How to handle multiple domains<br />Answer  :   Use multiple Farms with different `/virtualhosts`.
68. **What is `/propagateSyndPost`<br />Answer  :   Deprecated, but used for old-school replication.
69. **What is `/headers` in the cache section<br />Answer  :   Defines which response headers from AEM should be cached.
70. **What is `/ignoreUrlParams`<br />Answer  :   Lists parameters that should not create a unique cache file.
71. **What is the `/auth_checker`<br />Answer  :   A powerful feature that asks AEM if a user is allowed to see a cached file.
72. **How does Auth Checker work<br />Answer  :   Dispatcher intercepts request -> asks AEM (Head request) -> if 200, serves cache.
73. **What is `/secure_session`<br />Answer  :   Related to encrytped session handling.
74. **How to set the log level<br />Answer  :   In `httpd.conf` or `dispatcher.any` using `DispatcherLogLevel`.
75. **What are the Dispatcher log levels<br />Answer  :   0 (Errors), 1 (Warnings), 2 (Infos), 3 (Debug).
76. **What is the `/info` log level<br />Answer  :   Shows which files are being served and cached.
77. **What is the `/debug` log level<br />Answer  :   Shows exactly why a file was not cached.
78. **How to handle Large File Downloads<br />Answer  :   Use the `X-Sendfile` header to let Apache handle the file transfer.
79. **What is the `/allowAuthorized` 0 vs 1<br />Answer  :   0 = Don't cache if Auth header is present; 1 = Cache anyway.
80. **What is the `/dispatcher.passError`<br />Answer  :   Determines if Apache or AEM handles 4xx/5xx errors.

#### AEM as a Cloud Service (81-100)
81. **How is Dispatcher different in AEM Cloud<br />Answer  :   It's containerized; you use an SDK to test locally.
82. **What is the "Dispatcher Optimizer"<br />Answer  :   A tool that validates your config before deployment.
83. **Can you modify `httpd.conf` in AEM Cloud<br />Answer  :   Only via specific include files.
84. **What is the `clientheaders.any` in Cloud<br />Answer  :   A strictly managed list of headers allowed to pass through.
85. **What is the "Immutable" section of Cloud Dispatcher<br />Answer  :   Certain global settings cannot be changed by developers.
86. **How to test Cloud Dispatcher locally<br />Answer  :   Using the Docker-based Dispatcher Tools.
87. **What is the `docker_run.sh` script<br />Answer  :   Used to start the local Dispatcher container.
88. **How to handle Redirects in AEM Cloud<br />Answer  :   Preferred way is using `RewriteRules` in Apache.
89. **Where do logs go in AEM Cloud<br />Answer  :   They are streamed to Cloud Manager / Splunk.
90. **What is the `variables.any` file<br />Answer  :   Used to define environment-specific variables.
91. **What is the "Flexible" vs "Standard" mode in Cloud<br />Answer  :   Refers to the folder structure of the configuration.
92. **How to use `mod_rewrite` for vanity URLs<br />Answer  :   Use a `Map` file or direct `RewriteRule` in the vhost.
93. **What is the `conf.d/rewrites/rewrite.rules`<br />Answer  :   Standard file for redirect logic.
94. **How to block an IP address<br />Answer  :   Use Apache `Require exclude` or `Deny from` directives.
95. **What is the impact of CDN on Dispatcher<br />Answer  :   CDN caches content first; Dispatcher is the second layer.
96. **Does AEM Cloud use a "Stat file"<br />Answer  :   Yes, but the invalidation logic is handled by the Cloud orchestrator.
97. **How to bypass Dispatcher for debugging<br />Answer  :   Access the Publish instance directly via a VPN or specific header.
98. **What is the `/filter` for GraphQL<br />Answer  :   You must specifically allow the `/endpoint/graphql` path.
99. **How to handle CORS in Dispatcher<br />Answer  :   Using `mod_headers` in Apache to set `Access-Control-Allow-Origin`.
100. **What is the command to validate Dispatcher config<br />Answer  :   `./bin/validator full -d out conf` (in the SDK).

---

### Code Snippet: Useful Rewrite Rule (Permanent Redirect)
To be placed in your `vhost` file:
```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    # Redirect old-page.html to new-page.html
    RewriteRule ^/content/mysite/old-page.html$ /content/mysite/new-page.html [R=301,L]
</IfModule>
```

### Code Snippet: Blocking a Selector
In `filters.any`:
```apache
# Block any request that tries to use the 'infinity' selector
/0099 { /type "deny" /selectors 'infinity' /extension 'json' }
```
