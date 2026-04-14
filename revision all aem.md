This is the all discussed topics: **Sling Models, Servlets, OSGi Services, Service Users, JCR (Node vs Resource), and AEM Cloud Architecture**.

---

### **Part 1: Sling Models (50 Q&A)**
1.  **What is a Sling Model**<br />Answer  :   An annotation-driven POJO mapping JCR data to Java.
2.  **Difference between `@Inject` and `@ValueMapValue`**<br />Answer  :   `@Inject` is generic/slow; `@ValueMapValue` is specific to JCR properties/fast.
3.  **What is `@PostConstruct`**<br />Answer  :   A method that runs after all fields are injected for initialization.
4.  **Two main `adaptables`**<br />Answer  :   `Resource.class` and `SlingHttpServletRequest.class`.
5.  **Mandatory fields**<br />Answer  :   Use `@Required` (default). If missing, model returns `null`.
6.  **Optional fields**<br />Answer  :   Use `@Optional` or `DefaultInjectionStrategy.OPTIONAL`.
7.  **Sling Model Exporter**<br />Answer  :   Serializes models to JSON (Jackson) for Headless/APIs.
8.  **Model as Interface**<br />Answer  :   Yes, Sling generates implementation at runtime.
9.  **Inject OSGi Service**<br />Answer  :   Use `@OSGiService`.
10. **What is `@Self`**<br />Answer  :   Injects the adaptable object itself.
11. **Call model in HTL**<br />Answer  :   `data-sly-use.model="com.package.Class"`.
12. **Define default injection strategy**<br />Answer  :   Inside `@Model(defaultInjectionStrategy = ...)`.
13. **Role of `@Named`**<br />Answer  :   Maps a Java field to a differently named JCR property.
14. **What is `@Via`**<br />Answer  :   Changes the source of injection (e.g., from request to resource).
15. **Inject Resource Resolver**<br />Answer  :   Use `@SlingObject`.
16. **Inject `currentPage`**<br />Answer  :   Use `@ScriptVariable`.
17. **Handle Multifield**<br />Answer  :   Inject `List<Resource>` or `List<NestedModel>` via `@ChildResource`.
18. **Sling Model Cache**<br />Answer  :   `@Model(cache = true)` caches the instance per request.
19. **Register Model**<br />Answer  :   Add package to `Sling-Model-Packages` in bundle POM.
20. **Default value**<br />Answer  :   Use `@Default(values = "...")`.
21. **Model Factory**<br />Answer  :   Service to create models with better error handling.
22. **Difference `adaptTo` vs `createModel`**<br />Answer  :   `createModel` throws exceptions; `adaptTo` returns null.
23. **Headless support**<br />Answer  :   Use `@Exporter` and `.model.json` extension.
24. **Lombok with Models**<br />Answer  :   Yes, use `@Getter` to reduce code.
25. **Unit Testing**<br />Answer  :   Use AEM Mocks.


---

### **Part 2: Servlets & OSGi Services (50 Q&A)**
1.  **Sling Servlet**<br />Answer  :   Java class handling HTTP requests in AEM.
2.  **Safe vs All Methods**<br />Answer  :   `Safe` = GET (read); `All` = POST/PUT/DELETE (write).
3.  **Registration types**<br />Answer  :   Path-based and Resource-type-based.
4.  **Preferred registration**<br />Answer  :   Resource-type (respects ACLs).
5.  **Why avoid Path-based**<br />Answer  :   Bypasses security and is less flexible.
6.  **Access JCR Session**<br />Answer  :   `request.getResourceResolver().adaptTo(Session.class)`.
7.  **What is a Selector**<br />Answer  :   String in URL used to filter logic (e.g., `page.mobile.html`).
8.  **OSGi Service**<br />Answer  :   Singleton logic provider in OSGi.
9.  **`@Component` role**<br />Answer  :   Manages service lifecycle.
10. **`immediate = true`**<br />Answer  :   Activates service on bundle start.
11. **Service Ranking**<br />Answer  :   Higher number gives priority when multiple services exist.
12. **Target filter**<br />Answer  :   `@Reference(target = "(prop=val)")`.
13. **OSGi Configs (R7)**<br />Answer  :   Use `@ObjectClassDefinition`.
14. **Sling Filter**<br />Answer  :   Intercepts requests (Scope: REQUEST, COMPONENT, etc.).
15. **Sling Scheduler**<br />Answer  :   Runs jobs at specific times (Cron).
16. **Event Handler**<br />Answer  :   Listens for JCR events (node added/deleted).
17. **Bundle Status**<br />Answer  :   Check at `/system/console/bundles`.
18. **Bundle Lifecycle**<br />Answer  :   Installed, Resolved, Starting, Active, Stopping, Uninstalled.
19. **`@Activate`**<br />Answer  :   Runs when service starts.
20. **`@Deactivate`**<br />Answer  :   Runs when service stops (clean up resources).


### **Section 2: Servlets & OSGi Services (Continued: 21-50)**

#### **OSGi Configuration Admin (21-30)**
21. **Where are OSGi configurations stored in the JCR?**
  <br />Answer  :    Usually under `/apps/myapp/config` or `/apps/myapp/config.<runmode>`.
22. **What is the priority of OSGi configurations?**
  <br />Answer  :    Runmode-specific folders (e.g., `config.publish.prod`) have higher priority than the generic `config` folder.
23. **What is the `@Designate` annotation?**
  <br />Answer  :    It links an OSGi `@Component` to its configuration class (`@ObjectClassDefinition`).
24. **How do you define a dropdown (select) field in OSGi configurations?**
  <br />Answer  :    In the `@AttributeDefinition`, use the `options` attribute with `@Option` annotations.
25. **Can you read OSGi configurations in a Sling Model?**
  <br />Answer  :    No directly. You should read them in an OSGi service and then inject that service into the Sling Model.
26. **What is the difference between `.cfg.json`, `.config`, and `.xml` configurations?**
  <br />Answer  :    `.cfg.json` is the modern standard for AEM Cloud; `.config` is Apache Felix's internal format; `.xml` (sling:OsgiConfig) is the legacy JCR format.
27. **What is the "Configuration Admin" service?**
  <br />Answer  :    An OSGi service that manages the lifecycle and distribution of configurations to registered components.
28. **How do you handle sensitive data (passwords) in OSGi configs?**
  <br />Answer  :    In AEMaaCS, use **Secret Environment Variables**. In older versions, use **Crypto Support** (encrypted strings).
29. **What happens to a service if its configuration is deleted?**
  <br />Answer  :    The service is usually deactivated unless the code is written to handle default values.
30. **What is a "Runmode"?**
  <br />Answer  :    A keyword (e.g., `author`, `publish`, `prod`) that tells AEM which configuration or code to activate for that specific environment.

#### **Factory Configurations (31-40)**
31. **What is an OSGi Factory Configuration?**
  <br />Answer  :    A way to create multiple instances of the same service, each with different configuration settings (e.g., multiple different API connectors).
32. **How do you define a Factory Configuration in R7?**
  <br />Answer  :    Set `factory = true` in the `@Component` annotation.
33. **How does the system distinguish between factory instances?**
  <br />Answer  :    Each instance gets a unique **Persistent Identity (PID)** usually formatted as `service.pid~name`.
34. **How do you retrieve a specific instance of a factory service?**
  <br />Answer  :    Use `@Reference` with a target filter that matches a unique property of that instance.
35. **What is the role of the `name` attribute in a factory config file?**
  <br />Answer  :    The filename (e.g., `com.myapp.MyService~config1.cfg.json`) determines the unique identifier for that instance.
36. **Can you use Factory Configs for Servlets?**
  <br />Answer  :    Yes, if you need multiple servlets of the same type but mapped to different paths via configuration.
37. **What happens if two factory configs have the same PID?**
  <br />Answer  :    The OSGi framework will overwrite one with the other; they must have unique names after the `~` symbol.
38. **What is the `configurationPolicy` in `@Component`?**
  <br />Answer  :    It determines if a component requires a config to start (`REQUIRE`), ignores configs (`IGNORE`), or uses them if available (`OPTIONAL`).
39. **How do you update a factory config programmatically?**
  <br />Answer  :    Use the `ConfigurationAdmin` service to get the factory configuration by PID and call `update(Dictionary)`.
40. **When should you use a Factory Config over a standard Config?**
  <br />Answer  :    When you need a variable number of service instances (like multiple RSS feeds or multiple SMTP servers).

#### **Troubleshooting & Circular References (41-50)**
41. **What is a Circular Reference (Circular Dependency)?**
  <br />Answer  :    When Service A references Service B, and Service B references Service A.
42. **How do you detect a Circular Reference?**
  <br />Answer  :    The bundles will remain in the "Satisfied" state but will never "Activate," or you will see a `Circular Reference` error in the logs.
43. **How do you fix a Circular Reference?**
  <br />Answer  :    
        1. Refactor code to remove the dependency.
        2. Use `ReferencePolicy.DYNAMIC` and `ReferenceStrategy.LOOKUP`.
        3. Use a third service (Service C) to hold the shared logic.
44. **What is the difference between "Static" and "Dynamic" references?**
  <br />Answer  :    Static references (default) restart the component when the reference changes. Dynamic references (`policy = ReferencePolicy.DYNAMIC`) do not restart the component.
45. **What does the "Unsatisfied Reference" error mean?**
  <br />Answer  :    It means the `@Reference` you are trying to inject is not available or active in the OSGi container.
46. **What is a "Service Ranking" tie-breaker?**
  <br />Answer  :    If two services have the same ranking, the OSGi framework picks the one with the lowest **Service ID** (the one registered first).
47. **What is the "Bundle Context"?**
  <br />Answer  :    An object used to communicate with the OSGi framework, allowing you to manually register services or search for other services.
48. **How do you debug a Servlet that isn't firing?**
  <br />Answer  :    Check the **Sling Servlet Resolver** console (`/system/console/servletresolver`) to see which servlet is actually bound to a URL.
49. **What is a "Service Leak"?**
  <br />Answer  :    When a component gets a service via `getService()` but fails to release it, preventing the bundle from stopping cleanly.
50. **How do you see the properties of an active service?**
  <br />Answer  :    Go to the **Services Console** (`/system/console/services`) and search for the service PID.

---

### **Summary of Circular Reference Fixes (For Interview)**

*   **Scenario:** `Component A -> Component B -> Component A`.
*   **Solution 1 (Lazy Loading):** Don't inject Service B at the top level. Use `BundleContext` to get the service only when a specific method is called.
*   **Solution 2 (Volatile/Dynamic):** 
    ```java
    @Reference(policy = ReferencePolicy.DYNAMIC)
    private volatile MyServiceB serviceB;
    ```
*   **Solution 3 (Architectural):** Create an **Interface Bundle** or a **Shared Utility Service** to break the direct link.
---

### **Part 3: Service Users & Resource Resolvers (50 Q&A)**
1.  **`loginAdministrative()`**<br />Answer  :   Deprecated; too much power (security risk).
2.  **Service User**<br />Answer  :   Non-human system user for backend tasks.
3.  **Subservice**<br />Answer  :   Logical name in code mapping to a System User.
4.  **ResourceResolverFactory**<br />Answer  :   Service that generates Resolvers.
5.  **Get Service Resolver**<br />Answer  :   `resolverFactory.getServiceResourceResolver(map)`.
6.  **LoginException**<br />Answer  :   Thrown if mapping is missing or user doesn't exist.
7.  **RepoInit**<br />Answer  :   Scripts to create users/paths/ACLs during deployment.
8.  **Mapping Format**<br />Answer  :   `BundleName:subservice=userName`.
9.  **ACL**<br />Answer  :   Access Control List (Permissions).
10. **Allow vs Deny**<br />Answer  :   Deny always wins.
11. **Inheritance**<br />Answer  :   Children nodes inherit parent ACLs.
12. **`rep:policy`**<br />Answer  :   JCR node storing ACLs.
13. **Try-with-resources**<br />Answer  :   Best way to auto-close Resolvers.
14. **Unclosed Resolver**<br />Answer  :   Causes session leaks and crashes.
15. **Service User Mapper**<br />Answer  :   OSGi config where mappings are stored.
16. **Is subservice mandatory**<br />Answer  :   No, but recommended for security.
17. **AEMaaCS & Service Users**<br />Answer  :   Must use RepoInit (JCR is immutable).
18. **`jcr:all`**<br />Answer  :   Full permissions on a node.
19. **Move node**<br />Answer  :   Needs `delete` on source and `add_node` on destination.
20. **System User node type**<br />Answer  :   `rep:SystemUser`.

---

### **Part 4: Core JCR Concepts (Node vs Resource)**
*   **Node:** JCR-level (Apache Oak), low-level CRUD, throws exceptions if missing.
*   **Resource:** Sling-level abstraction, RESTful, returns "non-existing resource" instead of error.
*   **ValueMap:** How Resources expose properties easily.
*   **ResourceResolver:** Maps URLs to Resources.

---

### **Part 5: Architecture (AEMaaCS vs On-Prem)**
*   **AEMaaCS:** Managed by Adobe, Auto-scaling, Continuous updates, RepoInit required, Stateless.
*   **On-Prem:** Managed by customer, Manual scaling/updates, Full infrastructure control.
*   **Content Fragments (CF):** Content-only, Headless, GraphQL.
*   **Experience Fragments (XF):** Layout + Content, Web-reuse.

---

### **Part 6: Summary Table for Deployment**

| Task | Methodology |
| :--- | :--- |
| **Backend Logic** | Sling Models (POJO). |
| **HTTP Endpoints** | Resource-type Servlets. |
| **User Creation** | RepoInit scripts. |
| **Permissions** | ACLs via RepoInit or UserAdmin. |
| **Security** | Service Users (No Admin Sessions). |
| **Caching** | Dispatcher & CDN. |

### **Example RepoInit (Best Practice)**
```text
create service user my-data-service
set ACL on /content/my-site
    allow jcr:read for my-data-service
end
```
