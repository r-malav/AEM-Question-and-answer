In AEM, **Servlets** handle HTTP requests, and **OSGi Services** provide reusable backend logic. Modern AEM development uses **OSGi R7 (Declarative Services)** annotations.

---

### **Part 1: OSGi Service & Servlet Annotations**

#### **1. OSGi Service Annotations (DS)**
*   **`@Component`**: Defines a class as an OSGi Component/Service.
   *   `service = MyService.class`: The interface this service implements.
   *   `immediate = true`: Service is activated as soon as the bundle starts.
*   **`@Reference`**: Injects another OSGi service into the current class.
   *   `cardinality`: (e.g., `ReferenceCardinality.MANDATORY`).
   *   `policy`: (e.g., `ReferencePolicy.DYNAMIC`).
*   **`@Activate`**: Method called when the service is started.
*   **`@Deactivate`**: Method called when the service is stopped.
*   **`@Modified`**: Method called when the OSGi configuration is updated.
*   **`@ObjectClassDefinition (OCD)`**: Defines the metadata for an OSGi configuration (shows up in `/system/console/configMgr`).
*   **`@AttributeDefinition`**: Defines individual fields (text, boolean, etc.) within the OCD.

#### **2. Sling Servlet Annotations**
Modern AEM uses specific annotations instead of generic `@Component` properties:
*   **`@SlingServletResourceTypes`**: Binds the servlet to a resource type (Preferred method).
   *   `resourceTypes = "myproject/components/page"`
   *   `selectors = {"s1", "s2"}`
   *   `extensions = "json"`
   *   `methods = "GET"`
*   **`@SlingServletPaths`**: Binds the servlet to a specific URL path (e.g., `/bin/myServlet`).
*   **`@SlingServletName`**: Provides a name for the servlet.

---

### **Part 2: 50 Questions & Answers (Servlets & Services)**

#### **Servlet Basics (1-10)**
1.  **What is a Sling Servlet?** 
   **<br />Answer  :A Java class that extends `SlingSafeMethodsServlet` or `SlingAllMethodsServlet` to handle HTTP requests.
2.  **Difference between `SlingSafeMethodsServlet` and `SlingAllMethodsServlet`?**
   **<br />Answer  :`Safe`: Supports read-only operations (GET, HEAD). `All`: Supports data-modifying operations (POST, PUT, DELETE).
3.  **Two ways to register a servlet?**
   **<br />Answer  :By **Path** and by **Resource Type**.
4.  **Which registration is preferred?**
   **<br />Answer  :**Resource Type** is preferred because it respects AEM security/ACLs.
5.  **Why is Path-based registration discouraged?**
   **<br />Answer  :It bypasses JCR security and requires explicit permission in the "Sling Servlet Resolver" for specific paths.
6.  **What is the default extension if none is specified?**
   **<br />Answer  :The servlet will respond to any extension unless specified.
7.  **How do you access the JCR session in a servlet?**
   **<br />Answer  :`request.getResourceResolver().adaptTo(Session.class)`.
8.  **What is a Selector?**
   **<br />Answer  :A string between the node name and the extension (e.g., `page.mobile.json` - `mobile` is the selector).
9.  **What is the return type of `doGet`?**
   **<br />Answer  :`void`, but you write to `response.getWriter()`.
10. **Where can you see all registered servlets?**
   **<br />Answer  :`/system/console/servlets`.

#### **OSGi Services (11-20)**
11. **What is an OSGi Service?**
   **<br />Answer  :A singleton Java object registered in the OSGi framework to perform specific tasks.
12. **What is the role of `@Component`?**
   **<br />Answer  :It tells the OSGi Service Component Runtime (SCR) to manage the class lifecycle.
13. **How do you inject a service into a Servlet?**
   **<br />Answer  :Using the `@Reference` annotation.
14. **What is "Immediate = true"?**
   **<br />Answer  :It forces the service to initialize as soon as its bundle is active, rather than waiting for someone to call it.
15. **What is Service Ranking?**
   **<br />Answer  :An integer used to decide which service to use when multiple services implement the same interface (Higher is better).
16. **How to get a service by a specific property?**
   **<br />Answer  :Use `@Reference(target = "(property=value)")`.
17. **What is the OSGi Service Lifecycle?**
   **<br />Answer  :`INSTALLED` -> `RESOLVED` -> `STARTING` -> `ACTIVE` -> `STOPPING` -> `UNINSTALLED`.
18. **Difference between OSGi Service and Sling Model?**
   **<br />Answer  :Service is a singleton (global); Model is per-request/resource (data-mapping).
19. **How to make a service configuration-aware?**
   **<br />Answer  :Use `@Designate` to link the `@Component` to an `@ObjectClassDefinition`.
20. **Can one class implement multiple OSGi services?**
   **<br />Answer  :Yes, by listing multiple interfaces in the `service` attribute.

#### **OSGi Configuration (21-30)**
21. **What is `@ObjectClassDefinition`?**
   **<br />Answer  :An annotation used to create a user-friendly configuration form in the AEM Web Console.
22. **What is a Factory Configuration?**
   **<br />Answer  :A configuration that allows creating multiple instances of the same service with different settings.
23. **Where are OSGi configs stored in the JCR?**
   **<br />Answer  :Under `/apps/myapp/config` as `nt:file` or `sling:OsgiConfig`.
24. **Which folder has the highest priority for configs?**
   **<br />Answer  :`config.author` or `config.publish` based on the runmode.
25. **How do you read a config value in `@Activate`?**
   **<br />Answer  :By passing the OCD interface as a parameter to the activate method.
26. **What is the `.config` vs `.xml` format for OSGi?**
   **<br />Answer  :`.config` is the modern text-based format; `.xml` was used in older versions.
27. **What is the "Configuration Admin"?**
   **<br />Answer  :The OSGi service that manages and distributes configurations to components.
28. **Can you change OSGi configs at runtime?**
   **<br />Answer  :Yes, via the Web Console or by deploying new JCR nodes.
29. **What happens to a service when its config is updated?**
   **<br />Answer  :If a `@Modified` method exists, it runs. Otherwise, the service is deactivated and reactivated.
30. **What is `@AttributeDefinition`?**
   **<br />Answer  :Defines a specific field (e.g., `name = "Service URL"`, `type = AttributeType.STRING`).

#### **Advanced Scenarios (31-40)**
31. **What is a "Sling Filter"?**
   **<br />Answer  :A service that intercepts requests before they reach the servlet (e.g., for logging or auth).
32. **How to register a filter?**
   **<br />Answer  :Implement `javax.servlet.Filter` and use `@Component` with `sling.filter.scope`.
33. **What are the common filter scopes?**
   **<br />Answer  :`REQUEST`, `COMPONENT`, `ERROR`, `FORWARD`, `INCLUDE`.
34. **What is an "Event Handler"?**
   **<br />Answer  :A service that listens for JCR events (node created, deleted).
35. **What is an "Event Listener"?**
   **<br />Answer  :A lower-level JCR API listener (Service is usually preferred).
36. **What is a "Sling Scheduler"?**
   **<br />Answer  :A service used to run jobs at specific times (using Cron expressions).
37. **How to implement a Scheduler?**
   **<br />Answer  :Implement the `Runnable` interface and configure it as a scheduler component.
38. **Difference between `ResourceResolver` and `Session`?**
   **<br />Answer  :`ResourceResolver` is Sling API (Resource-based); `Session` is JCR API (Node-based).
39. **How to resolve a service dynamically at runtime?**
   **<br />Answer  :Use the `BundleContext` or a `ServiceTracker`.
40. **What is the "Web Console"?**
   **<br />Answer  :The `/system/console/configMgr` tool used to manage bundles and services.

#### **Troubleshooting & Best Practices (41-50)**
41. **Why is my `@Reference` null?**
   **<br />Answer  :The referred service might not be active, or its bundle is stopped.
42. **How to debug a servlet?**
   **<br />Answer  :Check `logs/error.log` and use the "Recent Requests" tool in the Web Console.
43. **Best practice for service cleanup?**
   **<br />Answer  :Always nullify references and close resource resolvers in the `@Deactivate` method.
44. **How to handle multiple runmodes for a service?**
   **<br />Answer  :Create separate config folders: `config.author`, `config.prod.publish`.
45. **Can a Servlet be an OSGi Service?**
   **<br />Answer  :Yes, every Sling Servlet is internally registered as an OSGi service.
46. **What is the `OptingServlet` interface?**
   **<br />Answer  :An interface a servlet can implement to dynamically decide if it wants to handle a specific request.
47. **What is the `service.ranking` property?**
   **<br />Answer  :Used to override a default AEM service with your custom implementation.
48. **How to pass data from a Filter to a Servlet?**
   **<br />Answer  :Using `request.setAttribute()`.
49. **How to close a ResourceResolver in a service?**
   **<br />Answer  :If you opened it via `serviceResourceResolver`, close it in a `finally` block or the deactivate method.
50. **What is the "Circular Reference" error?**
   **<br />Answer  :When Service A references B, and B references A. Fix by using `ReferencePolicy.DYNAMIC` or restructuring code.
