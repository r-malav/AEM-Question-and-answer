

### **Part 1: All Sling Model Annotations Explained**

#### **1. Core Annotations**
*   **`@Model`**: Identifies a class as a Sling Model.
    *   `adaptables`: Defines which object can be adapted (e.g., `Resource.class` or `SlingHttpServletRequest.class`).
    *   `adapters`: Allows the model to be adapted to an interface.
    *   `defaultInjectionStrategy`: Can be set to `REQUIRED` (default) or `OPTIONAL`.
*   **`@Exporter`**: Used to export the model as JSON (standard for AEM Headless). Usually paired with the Jackson exporter.

#### **2. Dependency Injectors (Specific)**
*   **`@ValueMapValue`**: Injects properties from the JCR (the `node` properties).
*   **`@OSGiService`**: Injects an OSGi service (replaces `@Reference`).
*   **`@ScriptVariable`**: Injects HTL objects like `currentPage`, `resourceResolver`, or `currentDesign`.
*   **`@SlingObject`**: Injects common Sling objects (e.g., `ResourceResolver`, `SlingHttpServletRequest`).
*   **`@Self`**: Injects the adaptable object itself (the resource or request).
*   **`@ChildResource`**: Injects a child node/resource of the current resource.
*   **`@RequestAttribute`**: Injects an attribute from the Servlet request.
*   **`@ResourcePath`**: Injects a resource from a specific path.
*   **`@Inject`**: The generic injector. It tries all available injectors until one works.

#### **3. Modifiers**
*   **`@Named`**: Used if the Java variable name is different from the JCR property name.
*   **`@Optional`**: Prevents the model from failing if a value is missing.
*   **`@Required`**: Forces the model to fail (return null) if the value is missing.
*   **`@Default`**: Provides a fallback value (e.g., `@Default(values = "N/A")`).
*   **`@Via`**: Changes the source of the injection (e.g., look in the `resource` even if the adaptable is a `request`).

#### **4. Lifecycle**
*   **`@PostConstruct`**: A method that runs automatically after all fields are injected. Used for logic/calculations.

---

### **Part 2: 50 Questions & Answers**

#### **Category 1: Basics (1-10)**
1.  **What is a Sling Model?**
    *   *Ans:* An annotation-driven POJO that allows mapping JCR data to Java objects.
2.  **How do you adapt a resource to a Sling Model in HTL?**
    *   *Ans:* `data-sly-use.model="com.example.MyModel"`.
3.  **What are the two most common `adaptables`?**
    *   *Ans:* `Resource.class` and `SlingHttpServletRequest.class`.
4.  **How do you register a Sling Model package?**
    *   *Ans:* Add the package to the `Sling-Model-Packages` header in the bundle's `pom.xml`.
5.  **What is the default injection strategy?**
    *   *Ans:* `REQUIRED`.
6.  **Can Sling Models be interfaces?**
    *   *Ans:* Yes. Sling generates the implementation at runtime.
7.  **What happens if a `@Required` field is not found?**
    *   *Ans:* The model adaptation fails and returns `null`.
8.  **Why use Sling Models over `WCMUsePojo`?**
    *   *Ans:* Sling Models are purely POJO, easier to unit test, and support OSGi injection better.
9.  **Can you use `@Reference` in a Sling Model?**
    *   *Ans:* No. Use `@OSGiService`.
10. **How do you define an interface as the adapter?**
    *   *Ans:* `@Model(adaptables = Resource.class, adapters = MyInterface.class)`.

#### **Category 2: Annotations in Depth (11-20)**
11. **Difference between `@Inject` and `@ValueMapValue`?**
    *   *Ans:* `@Inject` is generic and slow (tries all injectors); `@ValueMapValue` is specific to JCR properties and faster.
12. **What does `@PostConstruct` do?**
    *   *Ans:* It executes logic after all fields are injected but before the model is returned to the user.
13. **How do you inject a property named `jcr:title`?**
    *   *Ans:* `@ValueMapValue @Named("jcr:title") private String title;`
14. **When should you adapt from a `Request` instead of a `Resource`?**
    *   *Ans:* When you need access to request parameters, cookies, or HTL variables like `currentPage`.
15. **What is `@SlingObject` used for?**
    *   *Ans:* To inject technical Sling objects like `ResourceResolver`.
16. **How to provide a default value for a String?**
    *   *Ans:* `@ValueMapValue @Default(values = "Default Text")`.
17. **What is the use of `@Self`?**
    *   *Ans:* To get the adaptable itself or to adapt the current resource into another model.
18. **How do you inject a list of multifield items?**
    *   *Ans:* Inject a `List<Resource>` or `List<NestedModel>` using `@ChildResource`.
19. **What does `@Via` do?**
    *   *Ans:* It redirects the injector. Example: `@Via("resource")` tells a Request-based model to look for properties in the Resource.
20. **Can you use multiple adaptables?**
    *   *Ans:* Yes: `@Model(adaptables = {Resource.class, SlingHttpServletRequest.class})`.

#### **Category 3: Advanced Concepts (21-30)**
21. **What is the Sling Model Exporter?**
    *   *Ans:* A feature that serializes the model into JSON via Jackson.
22. **How do you access the JSON of a model?**
    *   *Ans:* By calling the resource with `.model.json` extension.
23. **What is `ModelFactory`?**
    *   *Ans:* An OSGi service used to create models programmatically with better error handling than `adaptTo()`.
24. **Difference between `adaptTo()` and `modelFactory.createModel()`?**
    *   *Ans:* `createModel` throws specific exceptions if it fails; `adaptTo` simply returns null.
25. **How to handle circular dependencies?**
    *   *Ans:* Avoid them by design or use the `ModelFactory` to lazily load the nested model.
26. **What is `cache = true` in `@Model`?**
    *   *Ans:* It ensures the model is instantiated only once per request, improving performance.
27. **How do you implement the Delegation Pattern?**
    *   *Ans:* Use `@Self` with `@Via(type = ResourceSuperType.class)` to extend Core Components.
28. **What is the `resourceType` attribute in `@Model`?**
    *   *Ans:* It binds the model to a specific component.
29. **How do you inject a service with a specific filter?**
    *   *Ans:* `@OSGiService(filter = "(myProp=value)")`.
30. **How do you unit test a Sling Model?**
    *   *Ans:* Use **AEM Mocks** and JUnit.

#### **Category 4: Scenarios (31-40)**
31. **How do you pass a parameter from HTL to a Sling Model?**
    *   *Ans:* You can't pass parameters directly to the constructor. Use `@RequestAttribute` and pass them via `data-sly-use`.
32. **Why is my model returning null?**
    *   *Ans:* Check if a `@Required` field is missing or if the package is not exported in the bundle.
33. **How do you get the `ResourceResolver` in a model?**
    *   *Ans:* `@SlingObject private ResourceResolver resourceResolver;`.
34. **How do you inject a property from a specific path?**
    *   *Ans:* Use `@ResourcePath(path = "/content/dam/logo.png")`.
35. **How to inject `currentPage`?**
    *   *Ans:* Adapt from `Request` and use `@ScriptVariable`.
36. **Can you use Lombok?**
    *   *Ans:* Yes, `@Getter` is widely used to avoid writing getters manually.
37. **What is a Custom Injector?**
    *   *Ans:* A class implementing the `Injector` interface to provide data not supported by default injectors.
38. **How to handle a Boolean property?**
    *   *Ans:* `@ValueMapValue private boolean isEnabled;`.
39. **How do you handle site configurations (CA Config)?**
    *   *Ans:* Use the `@ContextAwareConfiguration` injector (from ACS AEM Commons or Sling).
40. **Does Sling Model support constructor injection?**
    *   *Ans:* Yes, since Sling Models 1.3.

#### **Category 5: Troubleshooting & Best Practices (41-50)**
41. **Why is specific injection (e.g., `@ValueMapValue`) better than `@Inject`?**
    *   *Ans:* It improves performance by skipping unnecessary injector lookups.
42. **What happens if `@PostConstruct` throws an exception?**
    *   *Ans:* The model adaptation fails.
43. **Is it safe to use `SlingHttpServletRequest` as an adaptable?**
    *   *Ans:* Yes, but it makes the model slightly heavier than a `Resource` adaptable.
44. **How do you see all registered Sling Models?**
    *   *Ans:* Visit the OSGi Console at `/system/console/status-slingmodels`.
45. **How do you handle a property that might be a String or an Array?**
    *   *Ans:* Inject it as `Object` or a `String[]` (Sling handles the conversion).
46. **How to inject an Image path?**
    *   *Ans:* `@ValueMapValue private String fileReference;`.
47. **What is the `Source` modifier?**
    *   *Ans:* It forces the model to use a specific injector (e.g., `@Source("osgi-services")`).
48. **Can you inject a `Page` object?**
    *   *Ans:* Yes, by injecting the `Resource` and then adapting it to `Page.class`.
49. **How to ensure a model works for Headless?**
    *   *Ans:* Ensure it has `@Exporter` and all getter methods are public.
50. **What is the lifecycle of a Sling Model?**
    *   *Ans:* Instantiated on demand -> Fields Injected -> `@PostConstruct` called -> Model returned -> Garbage collected (it is not a singleton).
