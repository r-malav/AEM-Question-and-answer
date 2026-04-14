This guide covers the core concepts of **AEM Components**, **Dialogs**, **Design Dialogs**, and **Edit Configs**, tailored for interview preparation.

---

### **Part 1: Definitions & Annotations**

*   **`cq:Component`**: The primary node type for an AEM component.
*   **`cq:dialog`**: The Touch UI dialog (Granite UI) where authors enter content. Stored in `jcr:content`.
*   **`cq:design_dialog`**: Used in Static Templates to define styles/settings. In Editable Templates, this is replaced by **Content Policies**.
*   **`cq:editConfig`**: Defines the behavior of the component in the authoring interface (e.g., drag-and-drop, refresh on edit).
*   **`componentGroup`**: Categorizes components in the Sidekick/Asset finder.

---

### **Part 2: 50 Questions & Answers**

#### **Component Basics (1-10)**
1.  **What is an AEM Component?**
<br />Answer  :  A reusable unit of logic and style used to build web pages.
2.  **What is the node type of a component?**
<br />Answer  :  `cq:Component`.
3.  **What are the essential files in a component?**
<br />Answer  :  `.content.xml`, the rendering script (HTL/JSP), and the `cq:dialog`.
4.  **What is `sling:resourceSuperType`?**
<br />Answer  :  It allows a component to inherit properties and scripts from another component (Proxy Pattern).
5.  **How do you hide a component from the Component Browser?**
<br />Answer  :  Set the `componentGroup` property to `.hidden`.
6.  **Where are core components located?**
<br />Answer  :  `/libs/core/wcm/components`.
7.  **Why should we not modify `/libs`?**
<br />Answer  :  Any changes in `/libs` are overwritten during AEM upgrades.
8.  **What is the difference between a Foundation and a Core component?**
<br />Answer  :  Foundation components are legacy (Classic UI); Core Components are the modern, extensible standard for Touch UI.
9.  **How do you include a component within another component in HTL?**
<br />Answer  :  `<div data-sly-resource="${'path' @ resourceType='my/type'}"></div>`.
10. **What is the use of `componentGroup`?**
<br />Answer  :  It defines the tab name in the authoring "Insert" dialog.

#### **Touch UI Dialogs (cq:dialog) (11-20)**
11. **What technology is used for Touch UI dialogs?**
<br />Answer  :  Granite UI (based on Coral UI).
12. **Where is data saved when an author submits a dialog?**
<br />Answer  :  In the `jcr:content` node of the page where the component is placed.
13. **How do you create a Multifield in a dialog?**
<br />Answer  :  Use the `granite/ui/components/coral/foundation/form/multifield` resource type.
14. **What is the role of `name` property in a dialog field?**
<br />Answer  :  It defines the JCR property name where the value is stored (e.g., `./jcr:title`).
15. **How do you validate a dialog field (e.g., mandatory)?**
<br />Answer  :  Add `required="{Boolean}true"` to the field node.
16. **How do you show/hide fields based on a checkbox selection?**
<br />Answer  :  Use the `granite:hide` property or custom logic via a `clientlib` using Granite UI listeners.
17. **What is the `sling:resourceType` for a text field?**
<br />Answer  :  `granite/ui/components/coral/foundation/form/textfield`.
18. **How do you add a dropdown (Select) in a dialog?**
<br />Answer  :  Use `granite/ui/components/coral/foundation/form/select`.
19. **What is the `extraClientlibs` property in a dialog?**
<br />Answer  :  It allows you to load specific CSS/JS only when the dialog is opened.
20. **Can you use HTL inside a dialog?**
<br />Answer  :  No. Dialogs are rendered using Granite UI components (XML-based).

#### **Design Dialogs & Policies (21-30)**
21. **What is a `cq:design_dialog`?**
<br />Answer  :  A dialog used to configure global component settings (e.g., max images allowed).
22. **Where is Design Dialog data stored in Static Templates?**
<br />Answer  :  Under `/etc/designs`.
23. **What replaced Design Dialogs in Editable Templates?**
<br />Answer  :  Content Policies.
24. **Where is Content Policy data stored?**
<br />Answer  :  Under `/conf/<project>/settings/wcm/policies`.
25. **How does an author access a Content Policy?**
<br />Answer  :  By clicking the "Policy" icon on a component within the **Template Editor**.
26. **What is the benefit of Policies over Design Dialogs?**
<br />Answer  :  They are more flexible, support multi-tenancy better, and are stored in `/conf`.
27. **Can a single component have different policies?**
<br />Answer  :  Yes. Different templates can assign different policies to the same component.
28. **How do you access design properties in HTL?**
<br />Answer  :  Using the global object `currentStyle` (e.g., `${currentStyle.myProperty}`).
29. **Difference between `dialog` and `design_dialog`?**
<br />Answer  :  `dialog` is for content (instance-specific); `design_dialog` is for configuration (global for that template).
30. **Is `design_dialog` still used in AEM Cloud?**
<br />Answer  :  Rarely. Policies are the standard for AEMaaCS.

#### **cq:editConfig (31-40)**
31. **What is `cq:editConfig`?**
<br />Answer  :  A node that defines the authoring behavior of a component.
32. **What are the common properties of `cq:editConfig`?**
<br />Answer  :  `cq:actions`, `cq:layout`, `cq:dialogMode`, and `cq:listeners`.
33. **What does `cq:actions` define?**
<br />Answer  :  Buttons in the component toolbar (e.g., `edit`, `delete`, `insert`, `copymove`).
34. **How do you force a page refresh after editing a component?**
<br />Answer  :  Add a listener in `cq:editConfig`: `afteredit` = `REFRESH_PAGE`.
35. **What is a `dropTarget`?**
<br />Answer  :  It allows authors to drag assets (like images) directly from the asset finder onto the component.
36. **How do you enable In-Place Editing?**
<br />Answer  :  Define the `cq:inplaceEditing` node inside `cq:editConfig`.
37. **What is `cq:emptyText`?**
<br />Answer  :  Text displayed in the authoring placeholder when the component has no content.
38. **Difference between `afteredit` and `afterinsert` listeners?**
<br />Answer  :  `afteredit` fires when content changes; `afterinsert` fires only when the component is first dragged onto the page.
39. **What is `cq:dialogMode = "floating"`?**
<br />Answer  :  It opens the dialog as a separate window instead of an inline overlay (Legacy/Classic UI).
40. **How do you restrict where a component can be dropped?**
<br />Answer  :  Use the `allowedChildren` and `allowedParents` properties or Template Policies.

#### **Advanced & Scenario-Based (41-50)**
41. **What is a Proxy Component?**
<br />Answer  :  A component in `/apps` that points to a Core Component in `/libs` using `sling:resourceSuperType`.
42. **Why use Proxy Components?**
<br />Answer  :  To allow customization and versioning of Core Components without modifying the original code.
43. **How do you render a component as JSON?**
<br />Answer  :  Using Sling Model Exporter or by calling the `.json` extension if a default servlet is available.
44. **What is the use of `_cq_editConfig.xml`?**
<br />Answer  :  It is the file representation of the `cq:editConfig` node in your codebase.
45. **How do you handle a component that requires a JS library?**
<br />Answer  :  Create a `clientlib` and include it via `categories` in the component or via the page policy.
46. **What is the `cellName` in a `dropTarget`?**
<br />Answer  :  It specifies the property name where the dropped asset path is saved.
47. **Can a component have multiple dialogs?**
<br />Answer  :  No, but a dialog can have multiple tabs to organize fields.
48. **How do you provide a preview image for a component in the browser?**
<br />Answer  :  Add a thumbnail image named `thumbnail.png` inside the component folder.
49. **How do you create a global component available on all pages?**
<br />Answer  :  Place it in the Header/Footer of the base template.
50. **What is the purpose of `cq:template` node inside a component?**
<br />Answer  :  It defines default initial content that is automatically added when a component is dropped.

---

### **Summary Table for README.md**

| Feature | Node Name | Purpose | Data Storage |
| :--- | :--- | :--- | :--- |
| **Dialog** | `cq:dialog` | Enter content for a component instance. | `jcr:content` of the page. |
| **Design Dialog** | `cq:design_dialog` | Configure global component settings. | `/etc/designs` or `/conf`. |
| **Edit Config** | `cq:editConfig` | Define UI behavior (Drag & Drop, Listeners). | Component node itself. |
| **In-place Edit** | `cq:inplaceEditing` | Edit text directly on the page. | `jcr:content`. |
| **Policy** | N/A | Modern replacement for Design Dialog. | `/conf`. |
