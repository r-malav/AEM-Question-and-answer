# 🔹 SECTION 1: Basics (1–10)

### 1. What is a Sling Servlet?

A Java class used to handle HTTP requests in AEM using Sling framework.

---

### 2. How to create a Sling Servlet?

```java
@Component(service = Servlet.class,
property = {
    "sling.servlet.paths=/bin/example",
    "sling.servlet.methods=GET"
})
public class ExampleServlet extends SlingSafeMethodsServlet {
}
```

```java

@Component(service = Servlet.class, property = {
        ServletResolverConstants.SLING_SERVLET_METHODS + "=" + HttpConstants.METHOD_GET,
        ServletResolverConstants.SLING_SERVLET_PATHS + "=" + "/bin/example"
})
public class ExampleServlet extends SlingSafeMethodsServlet {
}
```

```java

@Component(service = Servlet.class, property = {
        ServletResolverConstants.SLING_SERVLET_METHODS + "=" + HttpConstants.METHOD_GET,

        ServletResolverConstants.SLING_SERVLET_RESOURCE_TYPES + "=" + "wknd/content/us/en/magazine",
        ServletResolverConstants.SLING_SERVLET_RESOURCE_TYPES + "=" + "wknd/content/us/en/adventures",
        ServletResolverConstants.SLING_SERVLET_RESOURCE_TYPES + "=" + "wknd/content/us/en/faqs",
        ServletResolverConstants.SLING_SERVLET_RESOURCE_TYPES + "=" + "wknd/content/us/en/about",

        ServletResolverConstants.SLING_SERVLET_EXTENSIONS + "=" + "json"
})
public class ExampleServlet extends SlingSafeMethodsServlet {
}
```

### 3. Difference: SlingSafeMethodsServlet vs SlingAllMethodsServlet

* Safe → GET only
* All → GET, POST, PUT, DELETE

---

### 4. Register servlet by path

```java
"sling.servlet.paths=/bin/test"
```

---

### 5. Register servlet by resourceType

```java
"sling.servlet.resourceTypes=my/components/page"
```

---

### 6. Which is better?

✅ resourceType (recommended)
❌ path (less secure)

---

### 7. Handle GET request

```java
protected void doGet(SlingHttpServletRequest request,
                    SlingHttpServletResponse response) {
}
```

---

### 8. Handle POST request

```java
protected void doPost(SlingHttpServletRequest request,
                     SlingHttpServletResponse response) {
}
```

---

### 9. Read query parameter

```java
String name = request.getParameter("name");
```

---

### 10. Set response type

```java
response.setContentType("application/json");
```

---

# 🔹 SECTION 2: Request Handling (11–20)

### 11. Get current resource

```java
Resource resource = request.getResource();
```

---

### 12. Get ResourceResolver

```java
ResourceResolver resolver = request.getResourceResolver();
```

---

### 13. Get selectors

```java
request.getRequestPathInfo().getSelectors();
```

---

### 14. Get extension

```java
request.getRequestPathInfo().getExtension();
```

---

### 15. Get suffix

```java
request.getRequestPathInfo().getSuffix();
```

---

### 16. Get resource path

```java
request.getRequestPathInfo().getResourcePath();
```

---

### 17. Read multiple params

```java
request.getParameterValues("tags");
```

---

### 18. Read JSON body

```java
BufferedReader reader = request.getReader();
```

---

### 19. Get headers

```java
request.getHeader("User-Agent");
```

---

### 20. Get cookies

```java
request.getCookies();
```

---

# 🔹 SECTION 3: Response Handling (21–30)

### 21. Write JSON response

```java
response.getWriter().write("{\"status\":\"ok\"}");
```

---

### 22. Set status code

```java
response.setStatus(200);
```

---

### 23. Return 422 error

```java
response.setStatus(422);
response.getWriter().write("Invalid input");
```

---

### 24. Convert object to JSON

```java
new Gson().toJson(obj);
```

---

### 25. Redirect

```java
response.sendRedirect("/content/home.html");
```

---

### 26. Set headers

```java
response.setHeader("Cache-Control", "no-cache");
```

---

### 27. File download

```java
response.setHeader("Content-Disposition", "attachment; filename=file.txt");
```

---

### 28. HTML response

```java
response.setContentType("text/html");
```

---

### 29. Binary stream

```java
response.getOutputStream();
```

---

### 30. Close writer

```java
response.getWriter().close();
```

---

# 🔹 SECTION 4: Real Scenarios (31–40)

### 31. Validate input

```java
if(query == null || query.trim().isEmpty()){
    response.setStatus(422);
}
```

---

### 32. Reject special chars

```java
query.matches(".*[^a-zA-Z0-9 ].*");
```

---

### 33. Custom JSON response

```json
{"status":422,"message":"Invalid query"}
```

---

### 34. Get page in servlet

```java
PageManager pm = resolver.adaptTo(PageManager.class);
```

---

### 35. Get child pages

```java
page.listChildren();
```

---

### 36. Create page list

```java
List<String> pages = new ArrayList<>();
```

---

### 37. Use QueryBuilder

```java
queryBuilder.createQuery(...);
```

---

### 38. Pagination

```java
request.getParameter("offset");
```

---

### 39. Filter by tag

```java
map.put("tagid", "news");
```

---

### 40. Remove duplicates

```java
list.stream().distinct().collect(Collectors.toList());
```

---

# 🔹 SECTION 5: Security & Best Practices (41–50)

### 41. Use service user

```java
getServiceResourceResolver(params);
```

---

### 42. Avoid admin session ❌

---

### 43. Close resolver

```java
resolver.close();
```

---

### 44. XSS protection

```java
xssAPI.encodeForHTML(input);
```

---

### 45. Sanitize input

```java
query.replaceAll("[^a-zA-Z0-9 ]", "");
```

---

### 46. Dispatcher security

* Restrict `/bin` endpoints

---

### 47. Limit API results

```java
if(limit > 50) limit = 50;
```

---

### 48. Logging

```java
log.info("Request received");
```

---

### 49. Exception handling

```java
try {} catch(Exception e){}
```

---

### 50. Use resourceType binding

```java
"sling.servlet.resourceTypes=my/components/search"
```
