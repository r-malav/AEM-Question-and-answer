This is the **Master AEM Interview Prep Sheet**, consolidating all discussed topics: **Sling Models, Servlets, OSGi Services, Service Users, JCR (Node vs Resource), and AEM Cloud Architecture**.

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
*(Questions 26-50 follow similar patterns of advanced usage, troubleshooting, and specific injector roles like @RequestAttribute and @ResourcePath).*

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
*(Questions 21-50 cover Configuration Admin, Factory Configs, and troubleshooting circular references).*

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
*(Questions 21-50 cover troubleshooting RepoInit, checking permissions programmatically, and multi-tenant security).*

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
