# AEM (Adobe Experience Manager) Interview Reference Guide

A comprehensive guide covering core AEM concepts including JCR, Sling, OSGi, and modern AEM Cloud Service features.

---

## 📑 Table of Contents
1. [Node vs Resource](#1-node-vs-resource)
2. [Content Strategy (Immutable vs Mutable)](#2-content-strategy)
3. [Templates (Static vs Editable)](#3-templates-static-vs-editable)
4. [Role of Dispatcher](#4-role-of-dispatcher)
5. [Sling Models: @Inject vs @ValueMapValue](#5-sling-models-inject-vs-valuemapvalue)
6. [AEM OSGi Bundles](#6-aem-osgi-bundles)
7. [Servlets: Path-based vs Resource-based](#7-servlets-path-based-vs-resource-based)
8. [AEMaaCS vs On-Premise](#8-aemaacs-vs-on-premise)
9. [Content Fragments vs Experience Fragments](#9-content-fragments-vs-experience-fragments)
10. [URL Structure & HTL Attributes](#10-url-structure--htl-attributes)

---

## 1. Node vs Resource
| Feature | Node | Resource |
| :--- | :--- | :--- |
| **Definition** | A JCR entity stored in CRX. | Sling abstraction representing content in a RESTful way. |
| **API** | Uses JCR API (`javax.jcr.Node`) | Uses Sling API (`org.apache.sling.api.resource.Resource`) |
| **Properties** | Uses Property objects under the node. | Uses ValueMap (`resource.getValueMap()`). |
| **Binding** | Not directly tied to Sling. | URL resolution → Resource via ResourceResolver. |
| **Flexibility** | Only JCR-backed content. | Can represent any data source (DB, synthetic, etc.). |
| **Null Handling** | Throws error if path doesn't exist. | Returns a non-existing Resource wrapper. |
| **Used In** | Low-level JCR CRUD operations. | Rendering, Sling Models, and request processing. |

---

## 2. Content Strategy
*   **Immutable Code:** Content that cannot be changed at runtime. Located in `/apps`, `/conf`, and `/oak:index`.
*   **Mutable Content:** Content that can be modified by authors or the system. Located in `/content`, `/etc`, and user-generated data.

---

## 3. Templates: Static vs Editable
| Feature | Static Templates | Editable Templates |
| :--- | :--- | :--- |
| **Creation Location** | `/apps/<project>/templates` | `/conf/<project>/settings/wcm/templates` |
| **Control** | Developers only | Authors + Developers |
| **Structure** | Hard-coded in component hierarchy | Defined via Structure and Initial Content layers |
| **Design** | Stored in `/etc/designs` (Deprecated) | Content Policies via Style System |
| **AEM Version** | Classic UI era | Touch UI (AEM 6.2+) |

---

## 4. Role of Dispatcher
The **Dispatcher** is AEM's caching and load-balancing tool. 
*   **Caching:** Serves static content from a web server to reduce load on the AEM Publish instance.
*   **Load Balancing:** Distributes user requests across multiple Publish instances.
*   **Security:** Acts as a filter to protect the AEM environment.

---

## 5. Sling Models: @Inject vs @ValueMapValue
*   **`@Inject`**: A generic annotation that can inject adaptable objects like services, resources, requests, or other models. It is slower as it tries all injectors.
*   **`@ValueMapValue`**: Specifically for resource properties from the JCR. It ensures safe type conversion and is the preferred practice for property injection.

---

## 6. AEM OSGi Bundles
Bundles are the building blocks of AEM logic.
*   **Definition:** Modular JAR files with `MANIFEST.MF` metadata managed by **Apache Felix**.
*   **Lifecycle:** `Installed` → `Resolved` → `Starting` → `Active` → `Stopping` → `Uninstalled`.
*   **Benefit:** Supports **Hot Deployment** (updating code without restarting the server).

---

## 7. Servlets: Path-based vs Resource-based
| Feature | Path-Based Servlet | Resource-Based Servlet |
| :--- | :--- | :--- |
| **Trigger** | Fixed URL path (e.g., `/bin/myapp`) | Resource Type of the node/component |
| **Annotation** | `@SlingServletPaths` | `@SlingServletResourceTypes` |
| **Security** | Requires manual configuration | Inherits JCR Access Control (ACLs) |
| **Flexibility** | Low (tied to one path) | High (works for any node with the resourceType) |
| **Use Case** | Admin/Utility endpoints | Component rendering logic |

---

## 8. AEMaaCS vs On-Premise
| Feature | AEMaaCS (Cloud Service) | AEM On-Premise |
| :--- | :--- | :--- |
| **Deployment** | Managed by Adobe | Managed by Customer |
| **Scaling** | Auto-scaling (Author & Publish) | Manual (Hardware/Node upgrades) |
| **Updates** | Continuous, automatic updates | Manual upgrades; often involves downtime |
| **CI/CD** | Cloud Manager only | Custom (Jenkins, Bamboo, etc.) |
| **Infrastructure** | Stateless / Immutable | State-based |

---

## 9. Content Fragments vs Experience Fragments
| Feature | Content Fragment (CF) | Experience Fragment (XF) |
| :--- | :--- | :--- |
| **Focus** | Structured data / Content-only | Layout + Components (Design) |
| **Channels** | Multi-channel (Headless/GraphQL) | Web-focused (HTML/Web Page) |
| **Reuse** | Used for structured content reuse | Used for design and experience reuse |

---

## 10. URL Structure & HTL Attributes
### URL Breakdown
**Example:** `/content/page.selector1.json`
*   **Selector:** `selector1` — Used for additional processing/filtering.
*   **Extension:** `json` — Defines the renderer type.

### Common HTL Attributes
| Attribute | Purpose |
| :--- | :--- |
| `data-sly-use` | Initializes a Java class or Sling Model. |
| `data-sly-template` | Defines a reusable template block. |
| `data-sly-call` | Invokes/calls a defined template. |
| `data-sly-resource` | Includes a specific resource in the component. |
