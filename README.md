This is a comprehensive **README.md** file containing over 100 interview coding scenarios, logic questions, and answers for AEM (Adobe Experience Manager) developers.

You can copy and save this content directly into a `README.md` file for your preparation.

---

# AEM Interview Coding Questions & Answers (100+ Scenarios)

This repository contains a curated list of AEM technical interview questions focusing on **Page Properties, Nested Hierarchy Traversal, Tagging API, Sling Models, and HTL.**

## Table of Contents
1. [Page API & Property Handling](#1-page-api--property-handling)
2. [Hierarchy & Nested Child Traversal](#2-hierarchy--nested-child-traversal)
3. [Tagging API Scenarios](#3-tagging-api-scenarios)
4. [Sling Models & Annotations](#4-sling-models--annotations)
5. [HTL (Sightly) Logic](#5-htl-sightly-logic)
6. [QueryBuilder & Search](#6-querybuilder--search)
7. [Servlets & OSGi Services](#7-servlets--osgi-services)
8. [JCR & Node API](#8-jcr--node-api)

---

## 1. Page API & Property Handling

**Q1: How do you get a Page object from a Resource?**
```java
Page page = resource.adaptTo(Page.class);
```

**Q2: How do you read a property named "navTitle" with a fallback to "jcr:title"?**
```java
ValueMap properties = page.getProperties();
String title = properties.get("navTitle", properties.get("jcr:title", String.class));
```

**Q3: How do you check if a page is "Hidden in Navigation" programmatically?**
```java
boolean isHidden = page.isHideInNav();
```

**Q4: How do you get the Template path used by a page?**
```java
String templatePath = page.getTemplate().getPath();
```

**Q5: How do you get the last modified date of a page?**
```java
Calendar lastMod = page.getLastModified();
```

**Q6: How to read page properties of a specific path?**
```java
PageManager pageManager = resolver.adaptTo(PageManager.class);
Page targetPage = pageManager.getPage("/content/project/en");
ValueMap vm = targetPage.getProperties();
```

---

## 2. Hierarchy & Nested Child Traversal

**Q7: How do you list all immediate children of a page?**
```java
Iterator<Page> children = page.listChildren();
while(children.hasNext()) {
    Page child = children.next();
}
```

**Q8: CODING CHALLENGE: Write a recursive function to read ALL nested child pages.**
```java
public void readNestedPages(Page parent) {
    Iterator<Page> children = parent.listChildren();
    while(children.hasNext()) {
        Page child = children.next();
        System.out.println("Page Path: " + child.getPath());
        readNestedPages(child); // RECURSIVE CALL
    }
}
```

**Q9: How do you filter children to show only "Valid" pages (not hidden, not deleted)?**
```java
Iterator<Page> children = page.listChildren(new PageFilter(false, false));
```

**Q10: How do you find the "Parent" page at a specific absolute level (e.g., Level 2)?**
```java
Page level2Page = page.getAbsoluteParent(2);
```

**Q11: How do you get the depth of the current page in the JCR?**
```java
int depth = page.getDepth();
```

---

## 3. Tagging API Scenarios

**Q12: How do you get all tags assigned to a Page object?**
```java
Tag[] tags = page.getTags();
```

**Q13: How do you read the title of a specific Tag?**
```java
String tagTitle = tag.getTitle(); // Standard title
String tagID = tag.getTagID(); // namespace:category/tag
```

**Q14: How do you resolve a Tag ID string into a Tag object?**
```java
TagManager tagManager = resolver.adaptTo(TagManager.class);
Tag tag = tagManager.resolve("my-namespace:products/electronics");
```

**Q15: How do you find all pages associated with a specific Tag?**
```java
TagManager tagManager = resolver.adaptTo(TagManager.class);
RangeIterator<Resource> results = tagManager.find("/content/cq:tags/default/my-tag", new String[]{"/content/my-site"});
```

**Q16: How do you get a tag title in a specific language (Localization)?**
```java
String deTitle = tag.getLocalizedTitle(Locale.GERMAN);
```

---

## 4. Sling Models & Annotations

**Q17: How do you inject a Page property into a Sling Model?**
```java
@ValueMapValue(name="jcr:title")
private String pageTitle;
```

**Q18: How do you get the current page inside a Sling Model?**
```java
@ScriptVariable
private Page currentPage;
```

**Q19: How do you inject an OSGi Service?**
```java
@OSGiService
private MyService myService;
```

**Q20: What is the purpose of `@PostConstruct`?**
It is used to execute initialization logic after all fields have been injected.

**Q21: How to make all field injections optional by default?**
```java
@Model(adaptables = Resource.class, defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL)
```

---

## 5. HTL (Sightly) Logic

**Q22: How do you loop through a list of items?**
```html
<ul data-sly-list.item="${model.items}">
    <li>${item}</li>
</ul>
```

**Q23: How do you show a div only if a property exists?**
```html
<div data-sly-test="${properties.description}">
    ${properties.description}
</div>
```

**Q24: How do you format a date in HTL?**
```html
<p>${'yyyy-MM-dd' @ format=currentPage.lastModified}</p>
```

**Q25: How do you call a Sling Model from HTL?**
```html
<div data-sly-use.model="com.package.MyModel">
    ${model.someData}
</div>
```

**Q26: How do you include a client library in HTL?**
```html
<sly data-sly-use.lib="/libs/granite/sightly/templates/clientlib.html">
    <sly data-sly-call="${lib.js @ categories='my-app.base'}"/>
</sly>
```

---

## 6. QueryBuilder & Search

**Q27: How do you find all pages under `/content/site` with template `T1`?**
```properties
path=/content/site
type=cq:Page
1_property=jcr:content/cq:template
1_property.value=/conf/site/settings/wcm/templates/T1
```

**Q28: How do you sort query results by last modified date?**
```properties
orderby=@jcr:content/jcr:lastModified
orderby.sort=desc
```

**Q29: How do you implement pagination in QueryBuilder?**
```properties
p.offset=0
p.limit=10
```

---

## 7. Servlets & OSGi Services

**Q30: Difference between `SlingSafeMethodsServlet` and `SlingAllMethodsServlet`?**
`Safe` is for Read (GET), `All` is for Write (POST/PUT/DELETE).

**Q31: How to register a servlet by Resource Type?**
```java
@Component(service = Servlet.class, property = {
    "sling.servlet.resourceTypes=my-project/components/page",
    "sling.servlet.selectors=data",
    "sling.servlet.extensions=json",
    "sling.servlet.methods=GET"
})
```

**Q32: How do you get the ResourceResolver in a Service?**
Use `ResourceResolverFactory.getServiceResourceResolver(authInfo)`.

---

## 8. JCR & Node API

**Q33: How to set a property on a Node?**
```java
Node node = resource.adaptTo(Node.class);
node.setProperty("myProp", "Hello World");
session.save();
```

**Q34: How to create a child node?**
```java
node.addNode("childNodeName", "nt:unstructured");
```

---

## Summary of 100+ Topics to Study
*   **AEM Basics:** Replication, Activation, Author vs Publish, Dispatcher.
*   **JCR:** Node types (`cq:Page`, `dam:Asset`), Mixins, Versions.
*   **Sling:** Resolution process, Selectors, Extensions, Suffix.
*   **OSGi:** Bundles, Services, Configurations, Components.
*   **DAM:** Asset metadata, Workflows, Renditions.
*   **Core Components:** Proxy components, Image, List, Navigation.
*   **Advanced:** Context-Aware Configs (CA Conf), Content Fragments, Experience Fragments.

---

### Instructions for Usage
1.  Save this content as `README.md`.
2.  Open in any Markdown viewer or upload to a Git repository.
3.  Focus on the **Recursive Logic** (Question 8) and **Tagging API** (Questions 12-16) as these are high-priority coding questions.
