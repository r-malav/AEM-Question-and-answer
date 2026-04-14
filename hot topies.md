Node vs Resource — Interview Points
A Node is the actual content stored in JCR, while a Resource is Sling’s view/representation of that content (or any other data) used for rendering and models.
Node is a JCR-level object stored in the content repository (CRX).
Resource is a Sling abstraction representing content for rendering.
Node uses JCR API (javax.jcr.Node), Resource uses Sling API (org.apache.sling.api.resource.Resource).
Node manages properties using JCR Property, Resource exposes properties using ValueMap.
ResourceResolver resolves URL → Resource; Node is not URL-driven.
Node exists only if stored in JCR; Resource can represent non-JCR data (synthetic, external).
Resource can be adapted to Node when low-level JCR operations are needed.
A Node is a JCR repository entity stored in CRX.
 A Resource is Sling’s representation of content for rendering.


Node interacts with JCR API (javax.jcr.Node).

 Resource interacts with Sling API (org.apache.sling.api.resource.Resource).


Node stores content in a hierarchical structure in JCR.
 Resource represents content in a REST-style structure used by Sling.


Node properties are accessed as JCR Property objects.
 Resource properties are accessed through a ValueMap.


Node is strictly JCR-backed.
 Resource can represent external/synthetic sources (not only JCR).


Node may throw exceptions if a path doesn’t exist.
 Resource can return a non-existing Resource object instead of failing.


Node is used for low-level CRUD operations on repository content.
 Resource is used for component rendering and Sling Models.






Feature
Node
Resource
Definition
A JCR (Java Content Repository) entity stored in CRX.
Sling abstraction representing content in a RESTful way.
API
Uses JCR API (javax.jcr.Node)
Uses Sling API (org.apache.sling.api.resource.Resource)
Data Access
Structured hierarchical content storage in JCR.
Accesses content + type info via ResourceResolver.
Properties
Uses Property objects under the node.
Uses ValueMap (resource.getValueMap()).
Binding to Sling
Not directly tied to Sling; it depends on JCR implementation.
URL resolution → Resource via Sling ResourceResolver.
Flexibility
Only JCR-backed content.
Can represent any data source (filesystem, DB, synthetic).
Null Handling
If the path doesn’t exist, → throws an error.
Can return a non-existing Resource wrapper.
Used In
Low-level repository CRUD operations in JCR.
Rendering, Sling Models, components, and request processing.


Immutable vs Mutable Content Strategy
Immutable code → /apps, /conf, /oak:index
Mutable content → /content, /etc, user-generated data

Feature
Static Templates
Editable Templates
Creation Location
/apps/<project>/templates
Managed via Template Editor (/conf/<project>/settings/wcm/templates)
Who Controls Structure?
Developers only
Authors + Developers
Page Structure
Hard-coded in component hierarchy
Defined using Structure and Initial Content layers
Editable Areas
Limited / fixed parsys
Responsive Layout + Dynamic policies for components
Design Configurations
Stored in /etc/designs (deprecated)
Content Policies via Style System
Component Permissions
Code-driven
Configurable without code deployment
Flexibility
Low
Very High
Recommended for
Legacy projects / strict fixed layouts
Modern AEM development (Adobe best practice)
AEM Version
Classic UI era
Touch UI (AEM 6.2+)




7. Role of Dispatcher in CQ5?
In CQ5, a Dispatcher is responsible for caching and serving the static content of a website. It helps reduce the load on the authoring environment and improves website performance by serving cached content to end-users.




@Inject vs @ValueMapValue


@Inject is a generic Sling Models injection annotation that can inject any adaptable object like services, resources, requests, or other models.
@ValueMapValue is specifically used to inject resource properties from the JCR ValueMap. It ensures safe type conversion and clearer intention for property injection.


@ValueMapValue
Safe conversion & avoids ClassCastException.


@Inject
Resource
ResourceResolver
OSGi services
SlingHttpServletRequest
Other Sling Models (child/adaptable)


What are AEM Bundles?
In AEM, Bundles are the modular building blocks of the OSGi framework.
 A bundle is just a JAR file with special metadata inside MANIFEST.MF that allows OSGi to manage it.
AEM Bundles are OSGi-based modular JAR files that contain services and business logic. They are dynamically installable and manageable by the OSGi framework (Apache Felix), supporting hot deployment and versioning.
OSGi allows bundles to be:
1. Installed 2.Started 3. Resolved 4. Active 5.Stopped 6.Uninstalled
 without restarting the server → Hot deployment
 One-Liner for Interview
Path-based servlets are tied to a fixed URL path, mainly useful for utilities/APIs, while resource-based servlets are tied to resourceTypes and are preferred in AEM for component-driven, reusable logic.
Feature
Path-Based Servlet
Resource-Based Servlet
How it is triggered
Based on exact JCR or URL path
Based on Resource Type of node/component
Annotation Used
@SlingServletPaths or /system/console/configMgr -> Sling Servlet Paths
@SlingServletResourceTypes
Binding
URL → Servlet
URL → Resource → Servlet
Flexibility
Low — tied to one path
High — reusable across many components/pages
Used For
Utility endpoints, custom APIs, specific paths
Page/component rendering, logic tied to content structure
Dependency on Content Structure
No — just path
Yes — must match a resource type in JCR
Common Example
/bin/myapp/data
ResourceType: myapp/components/content/hero
Mapping Type
Mapped using SlingServletPaths
Mapped using SlingServletResourceTypes
URL Dependency
Works only for that specific path
Works for any resource using that resourceType
Reusability
❌ Low (tightly coupled to URL path)
✔ High (used across components/pages with same resourceType)
Use Case
Admin/utility endpoints like /bin/...
Component rendering, content-driven servlets
Impact of Content Move
URL change → servlet breaks
Still works when content path changes
Security & Flexibility
Exposed endpoint → must secure
Less exposure → more secure by default
Best Suitable For
APIs, backend jobs, fixed service URLs
Extending component behavior dynamically




AEMaaCS and onprims


AEMaaCS is Adobe-managed cloud hosting with auto-scaling, continuous upgrades, stateless architecture, and built-in Cloud Manager CI/CD. On-Prem is fully customer-managed with manual deployments, scaling, hardware management, and version upgrades.



Feature
AEMaaCS (Cloud Service)
AEM On-Premise
1️⃣ Deployment Model
Fully managed by Adobe on cloud
Installed & managed on customer servers
2️⃣ Scaling
Auto-scaling (author & publish scale automatically based on load)
Manual scaling — upgrade hardware, add nodes
3️⃣ Release & Updates
Continuous updates by Adobe (zero downtime releases)
Customer controls updates; often requires downtime
4️⃣ DevOps & Pipeline
Adobe Cloud Manager for CI/CD, automated quality gates
Custom DevOps pipelines (Jenkins/Bamboo etc.), manual monitoring
5️⃣ Architecture
Stateless publish, immutable infrastructure, CDN + Edge Delivery
State-based architecture, needs manual dispatcher/CDN setup


When to Choose AEM On-Prem?
Choose On-Prem if:
Highly secured environments where data cannot leave customer network (Defense, BFSI)
Need complete infrastructure control (low-level configs, database tuning)
Heavy custom integrations not supported in Cloud
Cost-optimization for small-scale deployments
Offline network environments required

 When to Choose AEMaaCS (Cloud)?
Choose Cloud when:
You want auto-scaling + zero downtime upgrades
Minimum DevOps — Adobe manages infrastructure
Need rapid deployments and fast provisioning of environments
Large multi-site multilingual platform
Performance monitoring and quality gates built-in


Content Fragment vs Experience Fragment

Feature
Content Fragment
Experience Fragment
Content or Layout?
Content-only
Layout + Components
Channels
Multi-channel/headless
Web-focused
Use case
Structured data reuse
Page experience reuse
API delivery
GraphQL supported
Not for headless




What is Selector and Extension in AEM URL?
URL: /page.selector1.selector2.html

Part
Use
Selector
Additional processing (filtering, config)
Extension
Renderer type (html, json, xml)



Attribute
Purpose
Usage
data-sly-use
Access Java/Sling Models in HTL
${model.property}
data-sly-template
Define reusable HTL template
<div data-sly-template.name="${param}">...</div>
data-sly-call
Invoke template defined above
<div data-sly-call.name="${param: value}"></div>



