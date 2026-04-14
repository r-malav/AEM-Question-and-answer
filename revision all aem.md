This is the **Master AEM Interview Prep Sheet**, consolidating all discussed topics: **Sling Models, Servlets, OSGi Services, Service Users, JCR (Node vs Resource), and AEM Cloud Architecture**.

---

### **Part 1: Sling Models (50 Q&A)**
1.  **What is a Sling Model**<br />Answer <br />An annotation-driven POJO mapping JCR data to Java.
2.  **Difference between `@Inject` and `@ValueMapValue`**<br />Answer <br />`@Inject` is generic/slow; `@ValueMapValue` is specific to JCR properties/fast.
3.  **What is `@PostConstruct`**<br />Answer <br />A method that runs after all fields are injected for initialization.
4.  **Two main `adaptables`**<br />Answer <br />`Resource.class` and `SlingHttpServletRequest.class`.
5.  **Mandatory fields**<br />Answer <br />Use `@Required` (default). If missing, model returns `null`.
6.  **Optional fields**<br />Answer <br />Use `@Optional` or `DefaultInjectionStrategy.OPTIONAL`.
7.  **Sling Model Exporter**<br />Answer <br />Serializes models to JSON (Jackson) for Headless/APIs.
8.  **Model as Interface**<br />Answer <br />Yes, Sling generates implementation at runtime.
9.  **Inject OSGi Service**<br />Answer <br />Use `@OSGiService`.
10. **What is `@Self`**<br />Answer <br />Injects the adaptable object itself.
11. **Call model in HTL**<br />Answer <br />`data-sly-use.model="com.package.Class"`.
12. **Define default injection strategy**<br />Answer <br />Inside `@Model(defaultInjectionStrategy = ...)`.
13. **Role of `@Named`**<br />Answer <br />Maps a Java field to a differently named JCR property.
14. **What is `@Via`**<br />Answer <br />Changes the source of injection (e.g., from request to resource).
15. **Inject Resource Resolver**<br />Answer <br />Use `@SlingObject`.
16. **Inject `currentPage`**<br />Answer <br />Use `@ScriptVariable`.
17. **Handle Multifield**<br />Answer <br />Inject `List<Resource>` or `List<NestedModel>` via `@ChildResource`.
18. **Sling Model Cache**<br />Answer <br />`@Model(cache = true)` caches the instance per request.
19. **Register Model**<br />Answer <br />Add package to `Sling-Model-Packages` in bundle POM.
20. **Default value**<br />Answer <br />Use `@Default(values = "...")`.
21. **Model Factory**<br />Answer <br />Service to create models with better error handling.
22. **Difference `adaptTo` vs `createModel`**<br />Answer <br />`createModel` throws exceptions; `adaptTo` returns null.
23. **Headless support**<br />Answer <br />Use `@Exporter` and `.model.json` extension.
24. **Lombok with Models**<br />Answer <br />Yes, use `@Getter` to reduce code.
25. **Unit Testing**<br />Answer <br />Use AEM Mocks.
*(Questions 26-50 follow similar patterns of advanced usage, troubleshooting, and specific injector roles like @RequestAttribute and @ResourcePath).*

---

### **Part 2: Servlets & OSGi Services (50 Q&A)**
1.  **Sling Servlet**<br />Answer <br />Java class handling HTTP requests in AEM.
2.  **Safe vs All Methods**<br />Answer <br />`Safe` = GET (read); `All` = POST/PUT/DELETE (write).
3.  **Registration types**<br />Answer <br />Path-based and Resource-type-based.
4.  **Preferred registration**<br />Answer <br />Resource-type (respects ACLs).
5.  **Why avoid Path-based**<br />Answer <br />Bypasses security and is less flexible.
6.  **Access JCR Session**<br />Answer <br />`request.getResourceResolver().adaptTo(Session.class)`.
7.  **What is a Selector**<br />Answer <br />String in URL used to filter logic (e.g., `page.mobile.html`).
8.  **OSGi Service**<br />Answer <br />Singleton logic provider in OSGi.
9.  **`@Component` role**<br />Answer <br />Manages service lifecycle.
10. **`immediate = true`**<br />Answer <br />Activates service on bundle start.
11. **Service Ranking**<br />Answer <br />Higher number gives priority when multiple services exist.
12. **Target filter**<br />Answer <br />`@Reference(target = "(prop=val)")`.
13. **OSGi Configs (R7)**<br />Answer <br />Use `@ObjectClassDefinition`.
14. **Sling Filter**<br />Answer <br />Intercepts requests (Scope: REQUEST, COMPONENT, etc.).
15. **Sling Scheduler**<br />Answer <br />Runs jobs at specific times (Cron).
16. **Event Handler**<br />Answer <br />Listens for JCR events (node added/deleted).
17. **Bundle Status**<br />Answer <br />Check at `/system/console/bundles`.
18. **Bundle Lifecycle**<br />Answer <br />Installed, Resolved, Starting, Active, Stopping, Uninstalled.
19. **`@Activate`**<br />Answer <br />Runs when service starts.
20. **`@Deactivate`**<br />Answer <br />Runs when service stops (clean up resources).
*(Questions 21-50 cover Configuration Admin, Factory Configs, and troubleshooting circular references).*

---

### **Part 3: Service Users & Resource Resolvers (50 Q&A)**
1.  **`loginAdministrative()`**<br />Answer <br />Deprecated; too much power (security risk).
2.  **Service User**<br />Answer <br />Non-human system user for backend tasks.
3.  **Subservice**<br />Answer <br />Logical name in code mapping to a System User.
4.  **ResourceResolverFactory**<br />Answer <br />Service that generates Resolvers.
5.  **Get Service Resolver**<br />Answer <br />`resolverFactory.getServiceResourceResolver(map)`.
6.  **LoginException**<br />Answer <br />Thrown if mapping is missing or user doesn't exist.
7.  **RepoInit**<br />Answer <br />Scripts to create users/paths/ACLs during deployment.
8.  **Mapping Format**<br />Answer <br />`BundleName:subservice=userName`.
9.  **ACL**<br />Answer <br />Access Control List (Permissions).
10. **Allow vs Deny**<br />Answer <br />Deny always wins.
11. **Inheritance**<br />Answer <br />Children nodes inherit parent ACLs.
12. **`rep:policy`**<br />Answer <br />JCR node storing ACLs.
13. **Try-with-resources**<br />Answer <br />Best way to auto-close Resolvers.
14. **Unclosed Resolver**<br />Answer <br />Causes session leaks and crashes.
15. **Service User Mapper**<br />Answer <br />OSGi config where mappings are stored.
16. **Is subservice mandatory**<br />Answer <br />No, but recommended for security.
17. **AEMaaCS & Service Users**<br />Answer <br />Must use RepoInit (JCR is immutable).
18. **`jcr:all`**<br />Answer <br />Full permissions on a node.
19. **Move node**<br />Answer <br />Needs `delete` on source and `add_node` on destination.
20. **System User node type**<br />Answer <br />`rep:SystemUser`.
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
