
### **Part 1: All AEM Sling Model Annotations Explained**

Annotations are categorized into **Core Model Annotations** (define the model) and **Injectors** (get the data).

#### **1. Core Annotations**
*   **`@Model`**: Marks a class as a Sling Model.
    *   *Attributes*: `adaptables` (e.g., `Resource.class`, `SlingHttpServletRequest.class`), `adapters` (interfaces the model implements), `resourceType` (binds model to a component), `defaultInjectionStrategy` (REQUIRED or OPTIONAL).
*   **`@PostConstruct`**: Marks a method to be executed after all injections are complete. Used for initialization logic.
*   **`@Exporter`**: Used for the Sling Model Exporter framework to serialize the model into JSON (usually using Jackson).

#### **2. Injector-Specific Annotations (Best Practice over `@Inject`)**
*   **`@ValueMapValue`**: Injects a property from the JCR (e.g., `jcr:title`).
*   **`@OSGiService`**: Injects an OSGi service (replaces `@Reference` in Models).
*   **`@ScriptVariable`**: Injects variables available in HTL/Sightly scripts (e.g., `currentPage`, `resourceResolver`, `currentDesign`).
*   **`@SlingObject`**: Injects common Sling objects like `ResourceResolver`, `SlingHttpServletRequest`, or the current `Resource`.
*   **`@Self`**: Injects the adaptable object itself (e.g., the current Resource if the model is adapted from it).
*   **`@ChildResource`**: Injects a child resource of the current resource.
*   **`@RequestAttribute`**: Injects an attribute from the HTTP request.
*   **`@ResourcePath`**: Injects a resource from a specific path.

#### **3. Injection Modifiers**
*   **`@Named`**: Used if the JCR property name differs from the Java field name.
*   **`@Optional`**: Prevents the model from failing if the injection cannot be satisfied.
*   **`@Required`**: (Default) The model instantiation fails if the value is missing.
*   **`@Default`**: Provides a default value if the injection fails.
*   **`@Via`**: Changes the source of the injection (e.g., `@Via("resource")` when the adaptable is a Request).
