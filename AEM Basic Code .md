### Part 1: Page Properties & Resource API (Coding)

**1. How do you get a Page object from a Resource<br />Answer  :  
```java
Page page = resource.adaptTo(Page.class);
```

**2. How do you read a property "subtitle" from a page<br />Answer  :  
```java
ValueMap properties = page.getProperties();
String subtitle = properties.get("subtitle", String.class);
```

**3. How do you provide a default value if a property is missing<br />Answer  :  
```java
String title = properties.get("jcr:title", "No Title Provided");
```

**4. How do you get the Template path of a page<br />Answer  :  
```java
String templatePath = page.getTemplate().getPath();
```

**5. How do you check if a page is hidden in navigation<br />Answer  :  
```java
boolean isHidden = page.isHideInNav();
```

**6. How do you get the path of the current page<br />Answer  :  
```java
String path = page.getPath();
```

**7. How do you get the Page Manager from a Resource Resolver<br />Answer  :  
```java
PageManager pageManager = resolver.adaptTo(PageManager.class);
```

**8. How do you get a Page object if you only have a path string<br />Answer  :  
```java
Page page = pageManager.getPage("/content/we-retail/us/en");
```

**9. How do you find the "Language" of a page<br />Answer  :  
```java
Locale locale = page.getLanguage(false);
```

**10. How do you get the last modified date<br />Answer  :  
```java
Calendar lastMod = page.getLastModified();
```

---

### Part 2: Reading Child Pages & Nested Traversal (Recursion)

**11. How do you list immediate child pages<br />Answer  :  
```java
Iterator<Page> children = page.listChildren();
while(children.hasNext()) {
    Page child = children.next();
}
```

**12. Coding Question: Write a method to print all nested child paths (Deep Tree).**
```java
public void printAllPaths(Page page) {
    Iterator<Page> children = page.listChildren();
    while(children.hasNext()) {
        Page child = children.next();
        System.out.println(child.getPath());
        printAllPaths(child); // Recursive call
    }
}
```

**13. How do you list children while filtering out hidden pages<br />Answer  :  
```java
Iterator<Page> children = page.listChildren(new PageFilter(false, false));
```

**14. How do you find the "Parent" of the current page<br />Answer  :  
```java
Page parent = page.getParent();
```

**15. How do you get the page at a specific depth (e.g., Level 2)<br />Answer  :  
```java
Page level2Page = page.getAbsoluteParent(2);
```

**16. How do you check if a page has any children<br />Answer  :  
```java
boolean hasChildren = page.listChildren().hasNext();
```

**17. How do you list only children that are "activated" (replicated)<br />Answer  :  
(This requires checking the `cq:lastReplicationAction` property in the `jcr:content` node).

**18. How do you get the "depth" of a page<br />Answer  :  
```java
int depth = page.getDepth();
```

**19. How do you get the Resource of a page<br />Answer  :  
```java
Resource res = page.getContentResource();
```

**20. How do you find the next sibling page<br />Answer  :  
```java
PageManager pm = page.getPageManager();
// Usually involves getting parent, then iterating children until finding current + 1.
```

---

### Part 3: Reading Tags (Tagging API)

**21. How do you get all tags from a Page<br />Answer  :  
```java
Tag[] tags = page.getTags();
```

**22. How do you get the title of a tag<br />Answer  :  
```java
String title = tag.getTitle();
```

**23. How do you get the Tag Manager<br />Answer  :  
```java
TagManager tagManager = resourceResolver.adaptTo(TagManager.class);
```

**24. How do you resolve a Tag ID to a Tag object<br />Answer  :  
```java
Tag tag = tagManager.resolve("namespace:category/tag-name");
```

**25. How do you read tags from a specific Resource (not a Page)<br />Answer  :  
```java
TagManager tagManager = resolver.adaptTo(TagManager.class);
Tag[] tags = tagManager.getTags(resource);
```

**26. How do you get the localized title of a tag<br />Answer  :  
```java
String localTitle = tag.getLocalizedTitle(Locale.GERMANY);
```

**27. How do you find all pages that have a specific Tag<br />Answer  :  
```java
RangeIterator<Resource> list = tagManager.find("/content/cq:tags/default/my-tag", new String[]{"/content/my-site"});
```

**28. How do you get the ID of a tag<br />Answer  :  
```java
String id = tag.getTagID();
```

**29. How do you get the parent tag of a specific tag<br />Answer  :  
```java
Tag parentTag = tag.getParent();
```

**30. How do you check if two tags are the same<br />Answer  :  
```java
tag1.isSameTerm(tag2);
```

---

### Part 4: HTL (Sightly) Coding

**31. How to read a page property in HTL<br />Answer  :  
```html
<h1>${properties.jcr:title}</h1>
```

**32. How to iterate over child pages in HTL<br />Answer  :  
```html
<ul data-sly-list.child="${currentPage.listChildren}">
    <li>${child.title}</li>
</ul>
```

**33. How to check if a property exists before showing it<br />Answer  :  
```html
<p data-sly-test="${properties.description}">${properties.description}</p>
```

**34. How to call a Java Sling Model from HTL<br />Answer  :  
```html
<div data-sly-use.model="com.aem.models.MyModel">
    ${model.myData}
</div>
```

**35. How to format a date in HTL<br />Answer  :  
```html
${'yyyy-MM-dd' @ format=currentPage.lastModified}
```

**36. How to include a client library<br />Answer  :  
```html
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <sly data-sly-call="${clientlib.js @ categories='my.category'}"/>
</sly>
```

**37. How to join an array into a string in HTL<br />Answer  :  
```html
${properties.myArray @ join=', '}
```

**38. How to render raw HTML (dangerous)<br />Answer  :  
```html
${properties.richText @ context='html'}
```

**39. How to use a ternary operator in HTL<br />Answer  :  
```html
<div class="${properties.active ? 'is-active' : 'is-hidden'}"></div>
```

**40. How to include another resource/component in HTL<br />Answer  :  
```html
<sly data-sly-resource="${'path/to/resource' @ resourceType='my/type'}"/>
```

---

### Part 5: Sling Models (Logic)

**41. How to inject a Page Property<br />Answer  :  
```java
@ValueMapValue(name="jcr:title")
private String title;
```

**42. How to inject the current Page<br />Answer  :  
```java
@ScriptVariable
private Page currentPage;
```

**43. How to inject a Service into a Sling Model<br />Answer  :  
```java
@OSGiService
private MyService myService;
```

**44. How to inject the Resource Resolver<br />Answer  :  
```java
@SlingObject
private ResourceResolver resolver;
```

**45. What is `@PostConstruct`<br />Answer  :  
It's a method that runs after all injections are finished to initialize logic.

**46. How to adapt from Request vs Resource<br />Answer  :  
`@Model(adaptables = {SlingHttpServletRequest.class, Resource.class})`

**47. How to get all children of a resource in a Sling Model<br />Answer  :  
```java
@ChildResource
private List<Resource> children;
```

**48. How to use `@Self`<br />Answer  :  
Injects the adaptable itself (e.g., the current Resource).

**49. How to mark a field as Optional<br />Answer  :  
`@DefaultInjectionStrategy(DefaultInjectionStrategy.OPTIONAL)` at the class level.

**50. How to name a model for HTL<br />Answer  :  
`@Model(adaptables = Resource.class, adapters = MyInterface.class, resourceType = "my/comp")`

---

### Part 6: QueryBuilder & Search

**51. How to search for pages under a path<br />Answer  :  
`path=/content/we-retail`

**52. How to search by property value<br />Answer  :  
`property=jcr:title`, `property.value=Home`

**53. How to search for specific tags<br />Answer  :  
`tagid=namespace:tag`, `tagid.property=jcr:content/cq:tags`

**54. How to sort results by date<br />Answer  :  
`orderby=@jcr:content/jcr:lastModified`, `orderby.sort=desc`

**55. How to paginate (limit/offset)<br />Answer  :  
`p.limit=10`, `p.offset=0`

**56. How to search for specific resource types<br />Answer  :  
`type=cq:Page`

**57. How to execute a query in Java<br />Answer  :  
```java
SearchResult result = queryBuilder.createQuery(PredicateGroup.create(map), session).getResult();
```

**58. How to get the total number of matches<br />Answer  :  
`result.getTotalMatches()`

**59. How to find all images<br />Answer  :  
`type=dam:Asset`, `property=jcr:content/metadata/dc:format`, `property.value=image/jpeg`

**60. Difference between QueryBuilder and JCR-SQL2<br />Answer  :  
QueryBuilder is a Java API wrapper; JCR-SQL2 is a SQL-like string query.

---

### Part 7: Servlets & Request Handling

**61. How to register a servlet by Resource Type<br />Answer  :  
`@Component(service=Servlet.class, property={"sling.servlet.resourceTypes=my/type", "sling.servlet.methods=GET"})`

**62. Difference between `SlingSafeMethodsServlet` and `SlingAllMethodsServlet`<br />Answer  :  
Safe = Read only (GET). All = Read/Write (POST, PUT, DELETE).

**63. How to get parameters from a request<br />Answer  :  
`request.getParameter("myParam")`

**64. How to write a JSON response<br />Answer  :  
`response.setContentType("application/json"); response.getWriter().write("{\"key\":\"val\"}");`

**65. How to get the Resource from the current Request<br />Answer  :  
`request.getResource()`

**66. What is a "Selector" in AEM<br />Answer  :  
A part of the URL (e.g., `page.mobile.html` - `mobile` is the selector).

**67. How to get selectors in a Servlet<br />Answer  :  
`request.getRequestPathInfo().getSelectors()`

**68. How to get the extension<br />Answer  :  
`request.getRequestPathInfo().getExtension()`

**69. How to redirect in a servlet<br />Answer  :  
`response.sendRedirect("/content/other.html")`

**70. What is a Request Filter<br />Answer  :  
A class that intercepts requests before they hit the servlet (using `@SlingServletFilter`).

---

### Part 8: OSGi & Services

**71. How to define an OSGi Service<br />Answer  :  
`@Component(service = MyService.class)`

**72. How to create an OSGi Config field<br />Answer  :  
Use an `@ObjectClassDefinition` annotated interface.

**73. What is the lifecycle of an OSGi bundle<br />Answer  :  
Installed -> Resolved -> Starting -> Active -> Stopping -> Uninstalled.

**74. How to use `@Reference`<br />Answer  :  
To inject one OSGi service into another.

**75. What is an OSGi Alias/Target<br />Answer  :  
Used to filter which specific implementation of a service to inject.

---

### Part 9: JCR & Nodes (Low Level)

**81. How to get a Node from a Resource<br />Answer  :  
`Node node = resource.adaptTo(Node.class)`

**82. How to add a child node<br />Answer  :  
`node.addNode("myChild", "nt:unstructured")`

**83. How to set a property on a Node<br />Answer  :  
`node.setProperty("myProp", "value")`

**84. How to save changes to the JCR<br />Answer  :  
`session.save()`

**85. What is the difference between `nt:unstructured` and `cq:Page`<br />Answer  :  
`nt:unstructured` is a generic node; `cq:Page` is a specific AEM structure requiring a `jcr:content` child.

---

### Part 10: Logic & JavaScript (Nested Click / UI)

**91. How to read a data attribute from a clicked element<br />Answer  :  
```javascript
const tags = event.target.dataset.tags;
```

**92. How to implement Event Delegation for nested pages<br />Answer  :  
```javascript
document.querySelector('.parent').addEventListener('click', (e) => {
    if(e.target.matches('.child-page')) { 
        // Logic 
    }
});
```

**93. How to fetch AEM component data as JSON<br />Answer  :    
Append `.infinity.json` or `.model.json` to the URL.

**94. How to handle "Load More" for child pages<br />Answer  :         
Use a Servlet with `offset` and `limit` parameters called via AJAX.

**95. How to get the path of an image dropped into a dialog<br />Answer  :          
Read the `fileReference` property from the component's node.

