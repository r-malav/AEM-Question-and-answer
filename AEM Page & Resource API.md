## 1. Page & Resource API (Traversal & Properties)

**Q1: How to read a property "jcr:title" from a resource?**<br />Answer :
```java
String title = resource.getValueMap().get("jcr:title", String.class);
```

**Q2: How to get the Page object from a Resource?**<br />Answer :
```java
Page page = resource.adaptTo(Page.class);
```

**Q3: How to list all immediate child pages of a parent page?**<br />Answer :
```java
Iterator<Page> children = parentPage.listChildren();
```

**Q4: Write a recursive method to read all nested child pages.**
```java
public void getAllPages(Page page) {
    Iterator<Page> children = page.listChildren();
    while(children.hasNext()){
        Page child = children.next();
        // Logic here
        getAllPages(child); // Recursion
    }
}
```

**Q5: How to check if a page is "Hidden in Navigation"?**<br />Answer :
`page.isHideInNav();`

**Q6: How to get the template path of a page?**<br />Answer :
`page.getTemplate().getPath();`

**Q7: How to get the ResourceResolver from a request?**<br />Answer :
`request.getResourceResolver();`

**Q8: How to get a Page object if you have a path string?**<br />Answer :
```java
PageManager pm = resolver.adaptTo(PageManager.class);
Page page = pm.getPage("/content/site/en");
```

**Q9: Difference between `resource.adaptTo(ValueMap.class)` and `resource.getValueMap()`?**<br />Answer :
`getValueMap()` is a convenience method; both return the same properties.

**Q10: How to get the content resource of a page?**<br />Answer :
`page.getContentResource();` (returns the `jcr:content` node).

---

## 2. Sling Servlets (Request Handling)

**Q11: Register a Servlet by ResourceType (Best Practice).**
```java
@Component(service = Servlet.class)
@SlingServletResourceTypes(
    resourceTypes = "mysite/components/page",
    selectors = "info",
    extensions = "json",
    methods = "GET"
)
public class MyServlet extends SlingSafeMethodsServlet { ... }
```

**Q12: Register a Servlet by Path.**
`@SlingServletPaths(paths = "/bin/myapi")`

**Q13: Why avoid Path-based servlets?**<br />Answer :
Security risks (bypass JCR ACLs), difficult to overlay, and hard to manage across environments.

**Q14: Difference between `SlingSafeMethodsServlet` and `SlingAllMethodsServlet`?**<br />Answer :
Safe = GET (Read-only). All = POST, PUT, DELETE (Write).

**Q15: How to get selectors from a servlet request?**<br />Answer :
`request.getRequestPathInfo().getSelectors();`

**Q16: How to write a JSON response?**<br />Answer :
```java
response.setContentType("application/json");
response.getWriter().write("{\"key\":\"value\"}");
```

**Q17: How to get a parameter from the URL?**<br />Answer :
`request.getParameter("id");`

**Q18: What is the default servlet in Sling?**<br />Answer :
The `DefaultServlet` which renders the resource based on script resolution.

**Q19: How to get the suffix from a URL?**<br />Answer :
`request.getRequestPathInfo().getSuffix();`

**Q20: How to redirect from a servlet?**<br />Answer :
`response.sendRedirect("/content/home.html");`

---

## 3. OSGi Services & Components

**Q21: How to define an OSGi Service?**<br />Answer :
`@Component(service = MyService.class)`

**Q22: How to inject (Reference) a service?**<br />Answer :
`@Reference private MyService myService;`

**Q23: What is `@Activate`?**<br />Answer :
A method that executes when the component is started.

**Q24: What is `@Modified`?**<br />Answer :
A method that executes when the OSGi configuration is updated.

**Q25: What is "Service Ranking"?**<br />Answer :
A property that decides which service to inject if multiple implementations exist.

**Q26: How to use a "Target Filter" in `@Reference`?**<br />Answer :
`@Reference(target = "(myProp=value)")`

**Q27: Difference between Component and Service?**<br />Answer :
All Services are Components, but not all Components (like some logic-only ones) are exported as Services.

**Q28: How to get a Service Resource Resolver?**<br />Answer :
Use `ResourceResolverFactory.getServiceResourceResolver(map)`.

**Q29: What is the "Immediate" property in `@Component`?**<br />Answer :
`immediate = true` means the component is created on startup, not when first requested.

**Q30: How to disable a component in the OSGi Console?**<br />Answer :
Locate the bundle in `/system/console/components` and click "Disable".

---

## 4. OSGi Configurations (OCD)

**Q31: How to create an OSGi Config?**<br />Answer :
```java
@ObjectClassDefinition(name = "My Config")
public @interface MyConfig {
    @AttributeDefinition(name = "API Key")
    String api_key() default "default";
}
```

**Q32: Where are OSGi configs stored in JCR?**<br />Answer :
Under `/apps/myapp/config` or `/apps/myapp/config.author`.

**Q33: How to create environment-specific configs?**<br />Answer :
Using runmode folders (e.g., `config.prod`, `config.dev`).

**Q34: Can you read OSGi config in HTL?**<br />Answer :
No, you must read it in a Sling Model or Service first.

**Q35: What is the file extension for modern OSGi configs?**<br />Answer :
`.cfg.json`.

---

## 5. Sling Filters

**Q36: What is a Sling Filter?**<br />Answer :
A middleware that intercepts requests before they reach the servlet/script.

**Q37: How to register a filter?**<br />Answer :
```java
@Component(service = Filter.class, property = {
    "sling.filter.scope=REQUEST",
    "service.ranking=-100"
})
```

**Q38: What are the scopes for filters?**<br />Answer :
`REQUEST`, `COMPONENT`, `ERROR`, `FORWARD`, `INCLUDE`.

**Q39: How to pass the request to the next filter?**<br />Answer :
`chain.doFilter(request, response);`

**Q40: Why use `service.ranking` in filters?**<br />Answer :
To determine the execution order of multiple filters.

---

## 6. Listeners & Observation

**Q41: Difference between `EventListener` and `ResourceChangeListener`?**<br />Answer :
`EventListener` is JCR-level (low level). `ResourceChangeListener` is Sling-level (recommended).

**Q42: Write a ResourceChangeListener for a path.**
```java
@Component(service = ResourceChangeListener.class, property = {
    ResourceChangeListener.PATHS + "=/content/site",
    ResourceChangeListener.CHANGES + "=ADDED"
})
public class MyListener implements ResourceChangeListener {
    public void onChange(List<ResourceChange> list) { ... }
}
```

**Q43: What is the `EventAdmin`?**<br />Answer :
An OSGi service used to send and receive asynchronous events.

**Q44: How to trigger an event manually?**<br />Answer :
`eventAdmin.postEvent(new Event("my/topic", map));`

**Q45: How to avoid infinite loops in listeners?**<br />Answer :
Check if the modification was made by the "Service User" associated with the listener itself.

---

## 7. Jobs & Schedulers

**Q46: How to create a Scheduler using Runnable?**<br />Answer :
```java
@Component(service = Runnable.class, property = {
    "scheduler.expression=0 0/1 * 1/1 * ? *" // Every minute
})
public class MyTask implements Runnable { ... }
```

**Q47: Why use Sling Jobs over Schedulers?**<br />Answer :
Jobs are guaranteed to run (persisted in JCR). Schedulers are "fire and forget".

**Q48: How to add a Job?**<br />Answer :
`jobManager.addJob("my/topic", payloadMap);`

**Q49: How to consume a Job?**<br />Answer :
```java
@Component(service = JobConsumer.class, property = {
    JobConsumer.PROPERTY_TOPICS + "=my/topic"
})
```

**Q50: How to stop a running scheduler?**<br />Answer :
Stop the OSGi component or remove the configuration.

---

## 8. Tagging API

**Q51: How to get the TagManager?**<br />Answer :
`resolver.adaptTo(TagManager.class);`

**Q52: How to get tags from a Page?**<br />Answer :
`page.getTags();`

**Q53: How to read a Tag title?**<br />Answer :
`tag.getTitle();`

**Q54: How to find pages with a specific tag?**<br />Answer :
`tagManager.find("/content/tags/my-tag", new String[]{"/content/site"});`

**Q55: How to get localized tag titles?**<br />Answer :
`tag.getLocalizedTitle(Locale.US);`

---

## 9. Sling Models (General)

**Q56: `@ValueMapValue` vs `@Inject`?**<br />Answer :
`@ValueMapValue` is specific to resource properties; `@Inject` is generic.

**Q57: What is `@Self`?**<br />Answer :
Injects the adaptable itself (e.g., the Request or Resource).

**Q58: What is `@Via`?**<br />Answer :
Used to change the source of injection (e.g., inject from a child resource).

**Q59: How to handle default values in Sling Models?**<br />Answer :
`@Default(values = "N/A")`.

**Q60: How to export a Sling Model as JSON?**<br />Answer :
Use `@Exporter(name = "jackson", extensions = "json")`.



---

### **Section 2: Servlets & OSGi Services**

**11. What is the difference between Path-based and Resource-type-based servlets?**
*   **Ans:** Path-based servlets use a fixed URL; Resource-type servlets bind to a component and respect AEM security (ACLs).

**12. Why is Resource-type-based servlet preferred?**
*   **Ans:** It allows for better security, respects the JCR structure, and can be easily managed via Dispatcher.

**13. What is the role of `@Component` in OSGi?**
*   **Ans:** It marks a class as an OSGi service/component managed by the framework.

**14. What does `immediate = true` do in a Component?**
*   **Ans:** It activates the component as soon as the bundle starts, without waiting for a reference.

**15. How do you read OSGi configurations in R7?**
*   **Ans:** Using `@ObjectClassDefinition` (OCD) and passing the configuration interface to the `@Activate` method.

**16. What is the difference between `SlingSafeMethodsServlet` and `SlingAllMethodsServlet`?**
*   **Ans:** `Safe` handles read-only (GET); `All` handles all methods including data-modifying ones (POST, PUT, DELETE).

**17. What is `@Reference`?**
*   **Ans:** It is used to inject one OSGi service into another.

**18. How do you handle multiple service implementations for one interface?**
*   **Ans:** Use Service Ranking (higher number wins) or use a target filter: `@Reference(target = "(prop=value)")`.

**19. Where can you see if a bundle is active?**
*   **Ans:** In the AEM Web Console (Felix Console) at `/system/console/bundles`.

**20. What is the OSGi Bundle Lifecycle?**
*   **Ans:** Installed -> Resolved -> Starting -> Active -> Stopping -> Uninstalled.

---

### **Section 3: Security & Service Users**

**21. Why is `loginAdministrative()` deprecated?**
*   **Ans:** It provides full root access, which is a major security risk. It has been replaced by Service Users.

**22. How do you get a Resource Resolver in a service?**
*   **Ans:** Use `resolverFactory.getServiceResourceResolver(map)` with a subservice name.

**23. What is a Service User?**
*   **Ans:** A system-level, passwordless user used by backend code to perform JCR tasks with specific permissions.

**24. What is a "Subservice"?**
*   **Ans:** A logical name used in Java code that maps to a specific System User in the OSGi config.

**25. Why must you close a Resource Resolver?**
*   **Ans:** To prevent JCR session leaks, which lead to memory issues and server crashes.

**26. What is the best way to close a Resource Resolver?**
*   **Ans:** Using a `try-with-resources` block in Java.

**27. What is RepoInit?**
*   **Ans:** A script-based way to create folders, system users, and set ACLs automatically during deployment.

**28. What is an ACL (Access Control List)?**
*   **Ans:** A list of permissions (Read, Write, Delete) assigned to a user or group for a specific node.

**29. Does a Deny permission always override an Allow?**
*   **Ans:** Yes, in AEM/Oak, `Deny` takes precedence regardless of where it is defined.

**30. How do you check if a user has a specific permission in code?**
*   **Ans:** `session.hasPermission(path, "read")` or using the `AccessControlManager`.

---

### **Section 4: Node vs Resource & JCR**

**31. Difference between Node and Resource?**
*   **Ans:** Node is JCR-level (low level); Resource is a Sling abstraction (high level) used for rendering.

**32. What is a ValueMap?**
*   **Ans:** A map-like interface used to read properties of a Resource easily.

**33. What is the role of the ResourceResolver?**
*   **Ans:** It maps URLs to Resources and provides methods to find/resolve content.

**34. What is a Synthetic Resource?**
*   **Ans:** A resource that exists in memory for rendering but is not stored in the JCR.

**35. What is the JCR (Java Content Repository)?**
*   **Ans:** A hierarchical database (Apache Jackrabbit Oak) used by AEM to store content, metadata, and files.

---

### **Section 5: AEM Architecture & Fragments**

**36. Difference between Static and Editable Templates?**
*   **Ans:** Static templates are developer-only; Editable templates allow authors to define layout and policies without code.

**37. What is a Content Fragment (CF)?**
*   **Ans:** Content-only, structured data (no layout) used for headless delivery via GraphQL.

**38. What is an Experience Fragment (XF)?**
*   **Ans:** A group of components with a layout (design) that can be reused across multiple pages or channels.

**39. What is the Role of the Dispatcher?**
*   **Ans:** It acts as a cache and load balancer, serving static content to reduce load on the Publish instance.

**40. What is AEM as a Cloud Service (AEMaaCS)?**
*   **Ans:** A cloud-native version of AEM with auto-scaling, microservices for assets, and zero-downtime updates.

**41. What is the difference between Immutable and Mutable content?**
*   **Ans:** Immutable (`/apps`, `/libs`) cannot be changed at runtime in the cloud; Mutable (`/content`, `/conf`) can.

**42. What is a Clientlib?**
*   **Ans:** A system to manage CSS and JS files, supporting minification, merging, and dependency management.

**43. What is the Multi-Site Manager (MSM)?**
*   **Ans:** A tool to manage site variations (Live Copies) from a source (Blueprint).

**44. What is a Selector in an AEM URL?**
*   **Ans:** A string between the path and extension used to trigger different rendering logic (e.g., `page.mobile.html`).

**45. What is the Sightly (HTL) `data-sly-use`?**
*   **Ans:** An attribute used to initialize a Java Sling Model or JS file in the HTML template.

**46. How do you handle "Headless" in AEM?**
*   **Ans:** Using Content Fragments and Persisted GraphQL queries.

**47. What is a Workflow Launcher?**
*   **Ans:** A trigger that starts a workflow automatically when a node is created or modified.

**48. What is a Service User Mapping?**
*   **Ans:** An OSGi configuration that links a bundle/subservice to a specific System User.

**49. What is the purpose of `allowProxy = true` in Clientlibs?**
*   **Ans:** It allows clientlibs in `/apps` to be served securely via `/etc.clientlibs`.

**50. What is the "Style System"?**
*   **Ans:** A feature allowing authors to choose different visual CSS variations for a component via a dropdown in the UI.
