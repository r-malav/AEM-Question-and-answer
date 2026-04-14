The **AEM Query Builder** is a powerful API and tool used to search the Java Content Repository (JCR). Built on top of the JCR search capabilities (Oak index), it allows developers to create complex search queries using simple key-value pairs (predicates) instead of writing complex XPath or JCR-SQL2 queries.

---

### Part 1: Detailed Explanation

#### How it works:
1.  **Predicates:** You define search criteria as key-value pairs (e.g., `path=/content`, `type=cq:Page`).
2.  **QueryBuilder API:** The `QueryBuilder` service takes these predicates, translates them into an underlying XPath query, and executes it against the JCR.
3.  **SearchResult:** The execution returns a `SearchResult` object containing hits (nodes), pagination data, and execution time.

#### Key Advantages:
*   **Simplicity:** Easier to write and read than JCR-SQL2.
*   **Security:** Automatically handles ACL (Access Control List) permissions (users only see what they have access to).
*   **Feature-rich:** Built-in support for pagination, sorting, facets, and excerpt generation.

#### Query Builder Debugger Tool:
AEM provides a web interface to test queries at:
`http://localhost:4502/libs/cq/search/content/querydebug.html`

---

### Part 2: Implementation (Java API)

Here is a standard implementation of a Query Builder in an AEM OSGi Service or Servlet.

```java
package com.mysite.core.services;

import com.day.cq.search.PredicateGroup;
import com.day.cq.search.Query;
import com.day.cq.search.QueryBuilder;
import com.day.cq.search.result.Hit;
import com.day.cq.search.result.SearchResult;
import org.apache.sling.api.resource.Resource;
import org.apache.sling.api.resource.ResourceResolver;
import org.osgi.service.component.annotations.Component;
import org.osgi.service.component.annotations.Reference;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.jcr.Session;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@Component(service = MySearchService.class, immediate = true)
public class MySearchServiceImpl implements MySearchService {

 private static final Logger LOG = LoggerFactory.getLogger(MySearchServiceImpl.class);

 @Reference
 private QueryBuilder queryBuilder;

 public List<String> searchPages(ResourceResolver resourceResolver, String searchText) {
  List<String> pagePaths = new ArrayList<>();
  
  // 1. Define Predicates
  Map<String, String> map = new HashMap<>();
  map.put("path", "/content/mysite/en");
  map.put("type", "cq:Page");
  map.put("fulltext", searchText);
  map.put("p.limit", "10"); // Pagination limit

  // 2. Adapt ResourceResolver to JCR Session
  Session session = resourceResolver.adaptTo(Session.class);

  try {// 3. Create and Execute QueryQuery query = queryBuilder.createQuery(PredicateGroup.create(map), session);SearchResult result = query.getResult();
// 4. Iterate over resultsfor (Hit hit : result.getHits()) { Resource resource = hit.getResource(); pagePaths.add(resource.getPath());}
  } catch (Exception e) {LOG.error("Error in query builder search", e);
  }
  return pagePaths;
 }
}
```

---

### Part 3: 50 Interview Questions & Answers (With Code Snippets)

#### Basics (1-10)
1. **What is AEM Query Builder?**<br />Answer :A framework/API to build search queries using key-value pairs without writing raw XPath or SQL2.
2. **Where can you test Query Builder queries visually?**<br />Answer :`/libs/cq/search/content/querydebug.html`.
3. **What does Query Builder internally convert queries into?**<br />Answer :It converts them into XPath queries.
4. **Name 3 major objects in the Query Builder Java API.**`QueryBuilder`, `Query`, `SearchResult`.
5. **How do you restrict search to a specific path?**<br />Answer :`path=/content/mysite`
6. **How do you restrict search to a specific node type?**<br />Answer :`type=cq:Page` or `type=dam:Asset`.
7. **What does `p.limit=-1` mean?**<br />Answer :It fetches all results, bypassing pagination. (Use with caution due to performance).
8. **What is the default limit of results returned if not specified?**<br />Answer :10 results.
9. **How to get the total number of hits?**<br />Answer :`result.getTotalMatches();`
10. **How to do pagination (skip first 10 results)?**<br />Answer :
 `p.offset=10` and `p.limit=10`.

#### Predicates & Syntax (11-20)
11. **How to search by property value?**<br />Answer :
 `property=jcr:title` and `property.value=Home`.
12. **How to do an OR condition on properties?**<br />Answer :
 ```text
 property=jcr:title
 property.1_value=Home
 property.2_value=About
 ```
13. **How to do a "Like" (contains) search on a property?**<br />Answer :
 `property.operation=like` and `property.value=%Home%`.
14. **How to search for assets modified in the last 24 hours?**<br />Answer :
 `daterange.property=jcr:content/jcr:lastModified` and `daterange.lowerBound=-24h`.
15. **How to sort results?**<br />Answer :
 `orderby=@jcr:content/jcr:lastModified` and `orderby.sort=desc`.
16. **How to search for a specific node name?**<br />Answer :
 `nodename=en`.
17. **What is the `mainsearch` predicate used for?**<br />Answer :
 It's an alias for `fulltext`.
18. **How to search for property existence (not null)?**<br />Answer :
 `property=myProperty` and `property.operation=exists`.
19. **How to combine multiple groups with OR?**<br />Answer :
 ```text
 group.p.or=true
 group.1_path=/content/siteA
 group.2_path=/content/siteB
 ```
20. **How to search by Tag?**<br />Answer :
 `tagid=properties:style/modern` and `tagid.property=jcr:content/cq:tags`.

#### Code & API (21-30)
21. **How to get the actual JCR Node from a `Hit`?**<br />Answer :
 `Node node = hit.getNode();`
22. **How to convert a map of strings into a Query?**<br />Answer :
 `queryBuilder.createQuery(PredicateGroup.create(map), session);`
23. **How to extract a specific property directly from a Hit without loading the node?**<br />Answer :
 `hit.getProperties().get("jcr:title", "");` (Very efficient).
24. **How do you get the execution time of a query?**<br />Answer :
 `result.getExecutionTimeMillis();`
25. **How to prevent loading all results into memory for performance?**<br />Answer :
 Use `result.getHits()` which lazily loads results.
26. **Write a code snippet to get the path of search results.**
 ```java
 for (Hit hit : result.getHits()) {
  String path = hit.getPath();
 }
 ```
27. **How to get a QueryBuilder instance in a Sling Model?**<br />Answer :
 `@OSGiService private QueryBuilder queryBuilder;`
28. **How do you handle QueryBuilder exceptions?**<br />Answer :
 They are runtime exceptions, but wrap the execution in a `try-catch` block for JCR RepositoryExceptions.
29. **How to execute a query using a Map instead of JSON?**<br />Answer :
 `Query query = queryBuilder.createQuery(PredicateGroup.create(map), session);`
30. **How do you pass a ResourceResolver to QueryBuilder?**<br />Answer :
 Adapt it to a Session: `Session session = resolver.adaptTo(Session.class);`

#### Optimization & Best Practices (31-40)
31. **What is "Guess Total"?**<br />Answer :
 A mechanism to speed up queries by not counting the exact total number of matches.
32. **How to implement `guessTotal` in predicates?**<br />Answer :
 `p.guessTotal=true` or `p.guessTotal=1000`.
33. **Why is `p.limit=-1` dangerous?**<br />Answer :
 It can cause `OutOfMemory` errors if the query returns thousands of nodes.
34. **What is the Oak Index and why is it important for Query Builder?**<br />Answer :
 Oak handles indexing. If a query is not backed by an index, AEM traverses the JCR, causing severe performance issues.
35. **How to check if your Query Builder query is using an index?**<br />Answer :
 Check the `error.log` or use the **Explain Query** tool in AEM Tools.
36. **What does the warning "Traversed # nodes" mean?**<br />Answer :
 It means your query is not indexed and is manually scanning nodes.
37. **How to optimize a full-text search query?**<br />Answer :
 Combine it with a `path` and a `type` limit.
38. **Should you use Query Builder for simple parent-child navigation?**<br />Answer :
 No, use `resource.getChildren()` or `page.listChildren()`. It's faster.
39. **How do you limit searches to only Pages (ignoring assets)?**<br />Answer :
 Always add `type=cq:Page`.
40. **How to find empty nodes or broken references?**<br />Answer :
 Search for properties with `property.operation=not` or `exists`.

#### Advanced (41-50)
41. **What is `p.hits=full`?**<br />Answer :
 It tells the QueryBuilder to fetch all properties and resolve resources immediately (eager loading).
42. **How to use Native XPath in QueryBuilder?**<br />Answer :
 You don't. QueryBuilder generates XPath. If you need native XPath, use the JCR `QueryManager`.
43. **How do you filter by a specific template?**<br />Answer :
 `property=jcr:content/cq:template` and `property.value=/conf/myapp/settings/wcm/templates/page-template`.
44. **How to check for a property value NOT being equal to something?**<br />Answer :
 `property=jcr:title`, `property.value=Home`, `property.operation=unequal`.
45. **How to search for text inside a DAM asset's metadata?**<br />Answer :
 `path=/content/dam`, `type=dam:Asset`, `fulltext=searchterm`.
46. **What is the Predicate Evaluator?**<br />Answer :
 An OSGi service that handles a specific predicate type (like `daterange`). You can write custom ones.
47. **How to write a case-insensitive search?**<br />Answer :
 By default, JCR `fulltext` is case-insensitive. For specific properties, use `property.operation=equals` and ensure the backend index is analyzed as lower-case.
48. **How to find pages that are NOT activated?**<br />Answer :
 `property=jcr:content/cq:lastReplicationAction`, `property.operation=not`, `property.value=Activate`.
49. **How to find pages created by a specific user?**<br />Answer :
 `property=jcr:content/jcr:createdBy` and `property.value=admin`.
50. **How to implement facets in QueryBuilder?**<br />Answer :
 Add `p.facets=true` and `property=jcr:content/cq:tags`. Use `result.getFacets()` in Java to extract the tagged counts.
