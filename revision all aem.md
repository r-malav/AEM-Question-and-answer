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
Here are questions **21 to 50** for the **Service Users & Permissions** category, focusing on **programmatic checks, RepoInit troubleshooting, and multi-tenant security strategies**.

---

### **Section 3: Service Users & Permissions (Continued: 21-50)**

#### **Programmatic Permission Checks (21-30)**
21. **How do you check if a Service User can write to a specific path in Java?**
      <br />Answer  :    `session.hasPermission(path, Session.ACTION_ADD_NODE)` or `ACTION_SET_PROPERTY`.
22. **What is the `AccessControlManager`?**
      <br />Answer  :    A JCR API used to programmatically retrieve, add, or modify ACLs/Policies on nodes.
23. **How do you get a list of all privileges a user has on a node?**
      <br />Answer  :    Use `accessControlManager.getPrivileges(path)`.
24. **What is the difference between `session.checkPermission()` and `session.hasPermission()`?**
      <br />Answer  :    `hasPermission` returns a boolean; `checkPermission` throws an `AccessControlException` if the permission is missing.
25. **Can you check permissions using the `ResourceResolver` directly?**
      <br />Answer  :    Not directly. You must adapt it: `resolver.adaptTo(Session.class)` or `resolver.adaptTo(AccessControlManager.class)`.
26. **What are "Privileges" in JCR?**
      <br />Answer  :    Aggregated rights like `jcr:read`, `jcr:modifyProperties`, `jcr:addChildNodes`, or the all-encompassing `jcr:all`.
27. **How do you verify if a Service User has access to a specific OSGi service?**
      <br />Answer  :    OSGi services aren't governed by JCR ACLs; access is controlled via code logic or Service Rankings.
28. **How do you get the UserID of the current ResourceResolver?**
      <br />Answer  :    `resolver.getUserID()`. For a service user, it will return the System User name.
29. **What is the `UserManager` API?**
      <br />Answer  :    An AEM/Sling API used to manage users and groups (creating users, adding users to groups).
30. **How do you adapt a ResourceResolver to a `UserManager`?**
      <br />Answer  :    `resolver.adaptTo(UserManager.class)`. This requires the service user to have read/write access to `/home/users`.

#### **RepoInit & Deployment (31-40)**
31. **What happens if a RepoInit script tries to create a path that already exists?**
      <br />Answer  :    RepoInit is generally "idempotent." If you use `create path`, it ignores it if the path exists. However, `create service user` will fail if a *normal* user with that name already exists.
32. **Where do you see RepoInit execution logs?**
      <br />Answer  :    In the `error.log` during bundle activation or in the "Sling Repository Initializer" section of the Web Console.
33. **Can you delete content using RepoInit?**
      <br />Answer  :    No. RepoInit is designed for "initialization" (creating users, paths, and ACLs). It does not support `delete` for safety reasons.
34. **How do you handle RepoInit errors in AEM Cloud?**
      <br />Answer  :    If RepoInit fails, the **Cloud Manager pipeline will fail** during the "Deploy" phase. You must fix the script and push the code again.
35. **What is the difference between `create path` and `create node` in RepoInit?**
      <br />Answer  :    `create path` is used for simple folder structures; `create node` allows you to specify a primary type and properties.
36. **How do you set a property on a node via RepoInit?**
      <br />Answer  :    `set properties on /path { set myProp to "value" }`.
37. **Can RepoInit be used to create regular (human) users?**
      <br />Answer  :    No. It only supports `create service user`.
38. **What is the best way to group multiple ACLs in RepoInit?**
      <br />Answer  :    
    ```text
    set ACL on /content/siteA, /content/siteB
        allow jcr:read for my-service-user
    end
    ```
39. **Does RepoInit run every time AEM starts?**
      <br />Answer  :    It runs during the bundle start phase. If the instructions have already been processed and the state matches, it does nothing.
40. **Can you define RepoInit in a `.config` file?**
      <br />Answer  :    Yes, it is defined in an OSGi configuration for `org.apache.sling.jcr.repoinit.RepositoryInitializer`.

#### **Multi-tenant Security & Troubleshooting (41-50)**
41. **How do you achieve "Tenant Isolation" in AEM?**
      <br />Answer  :    By creating separate root paths for each tenant (e.g., `/content/tenant-a`, `/content/tenant-b`) and assigning unique Service Users to each.
42. **What is a "Closed User Group" (CUG)?**
      <br />Answer  :    A security feature that restricts access to a page (and its children) to a specific set of users/groups on the **Publish** instance.
43. **Service User mapping is correct, but I still get `LoginException`. Why?**
      <br />Answer  :    The System User might not have been created yet (check RepoInit), or the bundle symbolic name in the mapping is misspelled.
44. **How do you check which Service User a specific bundle is using?**
      <br />Answer  :    Check the **Service User Mapper** console in the Web Console (`/system/console/serviceusers`).
45. **How to prevent one tenant's Servlet from accessing another tenant's data?**
      <br />Answer  :    The Servlet should use a Service User with ACLs restricted **only** to that tenant's path.
46. **What is the `rep:policy` node?**
      <br />Answer  :    A hidden system node that stores the Access Control Entries (ACEs) for its parent node.
47. **How do you move a Service User mapping from one environment to another?**
      <br />Answer  :    Deploy it as an OSGi configuration (`.cfg.json`) via your Maven project.
48. **Can a Service User be a member of a User Group?**
      <br />Answer  :    Yes. This is a best practice for managing permissions across multiple service users.
49. **Why should you avoid `jcr:all` even for Service Users?**
      <br />Answer  :    It includes the ability to delete nodes and modify ACLs, which can lead to accidental data loss or security holes.
50. **What is the "Everyone" group?**
      <br />Answer  :    A built-in group that includes all users. If you `deny` something to the "everyone" group, it might block your service user too.

---

### **Top 3 Interview Troubleshooting Scenarios**

1.  **"My code works on Author but fails on Publish."**
    *   *Reason:* The Service User ACLs were not replicated or the RepoInit script wasn't deployed to the Publish environment.
2.  **"I added permissions to /content, but I can't read /content/site/en."**
    *   *Reason:* Check if there is a `deny` on the child node or if the user lacks `rep:read` on the parent `/content` node (required to traverse the tree).
3.  **"RepoInit script is correct but users aren't created."**
    *   *Reason:* The OSGi bundle containing the RepoInit config might be in the "Installed" state (not "Active") due to a missing dependency.
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
