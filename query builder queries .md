This `README.md` file is structured to be a comprehensive reference for **AEM QueryBuilder**, covering date-range logic and the top 20 queries frequently asked in interviews.

```markdown
# AEM QueryBuilder Cheat Sheet

A comprehensive guide to AEM QueryBuilder queries, including date range filters, common search patterns, and interview-specific scenarios.

---

## 📅 Date Range Queries (daterange)

AEM QueryBuilder uses the `daterange` predicate to filter content based on JCR date properties.

### 1. Find pages modified AFTER a specific date
**JSON Map:**
```json
{
  "path": "/content/my-site",
  "type": "cq:Page",
  "daterange.property": "jcr:content/cq:lastModified",
  "daterange.lowerBound": "2024-01-01T00:00:00.000Z",
  "daterange.lowerOperation": ">=",
  "p.limit": -1
}
```

### 2. Find pages modified BEFORE a specific date
**JSON Map:**
```json
{
  "path": "/content/my-site",
  "type": "cq:Page",
  "daterange.property": "jcr:content/cq:lastModified",
  "daterange.upperBound": "2024-06-01T00:00:00.000Z",
  "daterange.upperOperation": "<=",
  "p.limit": -1
}
```

### 3. Find pages BETWEEN two dates
**JSON Map:**
```json
{
  "path": "/content/my-site",
  "type": "cq:Page",
  "daterange.property": "jcr:content/cq:lastModified",
  "daterange.lowerBound": "2024-01-01T00:00:00.000Z",
  "daterange.lowerOperation": ">=",
  "daterange.upperBound": "2024-03-31T23:59:59.999Z",
  "daterange.upperOperation": "<=",
  "p.limit": -1
}
```

### 4. Query by Replication Date
**JSON Map:**
```json
{
  "path": "/content/my-site",
  "type": "cq:Page",
  "daterange.property": "jcr:content/cq:lastReplicated",
  "daterange.lowerBound": "2024-10-01T00:00:00.000Z",
  "daterange.lowerOperation": ">=",
  "p.limit": -1
}
```

### 💡 Supported Date Formats
AEM QueryBuilder accepts **ISO-8601** format: `yyyy-MM-dd'T'HH:mm:ss.SSS'Z'`
*   **UTC:** `2024-01-01T00:00:00.000Z`
*   **Timezone Offset:** `2024-12-01T05:30:00.000+05:30`

---

## 🚀 Top 20 QueryBuilder Interview Queries

### 1. All pages under a path
`path=/content/my-site` | `type=cq:Page` | `p.limit=-1`

### 2. Pages created by a specific author
`path=/content` | `type=cq:PageContent` | `property=jcr:createdBy` | `property.value=admin`

### 3. Published in the last 7 days
`path=/content` | `type=cq:PageContent` | `daterange.property=jcr:content/cq:lastReplicated` | `daterange.lowerBound=-7d`

### 4. Specific resourceType components
`path=/content/my-site` | `type=nt:unstructured` | `property=sling:resourceType` | `property.value=my/components/hero`

### 5. Assets by Mime Type (e.g., JPEG)
`path=/content/dam/my-site` | `type=dam:Asset` | `property=jcr:content/metadata/dc:format` | `property.value=image/jpeg`

### 6. Pages using a specific Template
`path=/content/my-site` | `type=cq:Page` | `property=jcr:content/cq:template` | `property.value=/conf/my-site/settings/wcm/templates/home`

### 7. Unpublished pages
`path=/content/my-site` | `type=cq:PageContent` | `property=jcr:content/cq:lastReplicationAction` | `property.operation=unequals` | `property.value=Activate`

### 8. Full-text search
`path=/content/my-site` | `fulltext=bank` | `p.limit=20`

### 9. Multifield items containing a value
`path=/content/my-site` | `type=nt:unstructured` | `property=myMultiField/*` | `property.value=demo`

### 10. Components used anywhere (LIKE operation)
`path=/content/my-site` | `1_property=sling:resourceType` | `1_property.value=my/components/%` | `1_property.operation=like`

### 11. Modified in the last 24 hours
`path=/content` | `type=cq:PageContent` | `daterange.property=jcr:lastModified` | `daterange.lowerBound=-1d`

### 12. Pages missing a specific property
`path=/content/my-site` | `type=cq:PageContent` | `property=jcr:title` | `property.operation=not`

### 13. Pages with specific Tag ID
`path=/content` | `type=cq:PageContent` | `tagid=marketing:offers`

### 14. Untagged pages
`path=/content` | `type=cq:PageContent` | `property=cq:tags` | `property.operation=not`

### 15. Locked pages
`path=/content` | `type=cq:PageContent` | `property=jcr:lockOwner` | `property.operation=exists`

### 16. Find all Live Copies (MSM)
`type=cq:LiveSyncConfig` | `p.limit=-1`

### 17. Search by Title using Wildcards
`path=/content` | `type=cq:Page` | `property=jcr:content/jcr:title` | `property.operation=like` | `property.value=%Home%`

### 18. Find all Redirects
`path=/content/my-site` | `type=nt:unstructured` | `property=sling:redirect` | `property.operation=exists`

### 19. Large Assets (Above 5MB)
`path=/content/dam` | `type=dam:AssetContent` | `range.property=jcr:content/metadata/dam:size` | `range.lowerBound=5242880`

### 20. Pagination (Offset + Limit)
`path=/content/my-site` | `type=cq:Page` | `p.offset=20` | `p.limit=10`

---

## 🔗 URL Execution
You can test these queries directly in the browser via the QueryBuilder Servlet:
`/bin/querybuilder.json?path=/content/my-site&type=cq:Page&p.limit=-1`
```
