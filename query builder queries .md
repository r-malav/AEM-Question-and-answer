This version of the `README.md` file provides all **QueryBuilder** queries in **JSON Map format**, which is the standard for passing parameters to the QueryBuilder API in Java or via POST requests.

```markdown
# AEM QueryBuilder JSON Reference Guide

A comprehensive guide to AEM QueryBuilder queries in JSON format for use in Java API, Postman, or background services.

---

## 📅 Date Range Queries (JSON Format)

### 1. Find pages modified AFTER a specific date
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

---

## 🚀 Top 20 Interview Queries (JSON Format)

### 1. Find all pages under a path
```json
{
  "path": "/content/my-site",
  "type": "cq:Page",
  "p.limit": -1
}
```

### 2. Find pages created by a specific author
```json
{
  "path": "/content",
  "type": "cq:PageContent",
  "property": "jcr:createdBy",
  "property.value": "admin"
}
```

### 3. Published in the last 7 days
```json
{
  "path": "/content",
  "type": "cq:PageContent",
  "daterange.property": "jcr:content/cq:lastReplicated",
  "daterange.lowerBound": "-7d"
}
```

### 4. Components of a specific resourceType
```json
{
  "path": "/content/my-site",
  "type": "nt:unstructured",
  "property": "sling:resourceType",
  "property.value": "myproject/components/hero"
}
```

### 5. Assets by Mime Type
```json
{
  "path": "/content/dam/my-site",
  "type": "dam:Asset",
  "property": "jcr:content/metadata/dc:format",
  "property.value": "image/jpeg"
}
```

### 6. Templates used by pages
```json
{
  "path": "/content/my-site",
  "type": "cq:Page",
  "property": "jcr:content/cq:template",
  "property.value": "/conf/my-site/settings/wcm/templates/home"
}
```

### 7. Unpublished pages
```json
{
  "path": "/content/my-site",
  "type": "cq:PageContent",
  "property": "jcr:content/cq:lastReplicationAction",
  "property.operation": "unequals",
  "property.value": "Activate"
}
```

### 8. Full-text search within a site
```json
{
  "path": "/content/my-site",
  "fulltext": "bank",
  "p.limit": 20
}
```

### 9. Multifield items containing a value
```json
{
  "path": "/content/my-site",
  "type": "nt:unstructured",
  "property": "myMultiField/*",
  "property.value": "demo"
}
```

### 10. Pages using a specific component (LIKE)
```json
{
  "path": "/content/my-site",
  "1_property": "sling:resourceType",
  "1_property.value": "myproject/components/%",
  "1_property.operation": "like"
}
```

### 11. Modified pages (last 24 hours)
```json
{
  "path": "/content",
  "type": "cq:PageContent",
  "daterange.property": "jcr:lastModified",
  "daterange.lowerBound": "-1d"
}
```

### 12. Find pages missing a property
```json
{
  "path": "/content/my-site",
  "type": "cq:PageContent",
  "property": "jcr:title",
  "property.operation": "not"
}
```

### 13. Find tag-based pages
```json
{
  "path": "/content",
  "type": "cq:PageContent",
  "tagid": "marketing:offers"
}
```

### 14. Find untagged pages
```json
{
  "path": "/content",
  "type": "cq:PageContent",
  "property": "cq:tags",
  "property.operation": "not"
}
```

### 15. Find locked pages
```json
{
  "path": "/content",
  "type": "cq:PageContent",
  "property": "jcr:lockOwner",
  "property.operation": "exists"
}
```

### 16. Find all Live Copies (MSM)
```json
{
  "type": "cq:LiveSyncConfig",
  "p.limit": -1
}
```

### 17. Find pages by title using LIKE
```json
{
  "path": "/content",
  "type": "cq:Page",
  "property": "jcr:content/jcr:title",
  "property.operation": "like",
  "property.value": "%Home%"
}
```

### 18. Find all redirects
```json
{
  "path": "/content/my-site",
  "type": "nt:unstructured",
  "property": "sling:redirect",
  "property.operation": "exists"
}
```

### 19. Assets by size (above 5MB)
```json
{
  "path": "/content/dam",
  "type": "dam:AssetContent",
  "range.property": "jcr:content/metadata/dam:size",
  "range.lowerBound": "5242880"
}
```

### 20. Pagination (Offset + Limit)
```json
{
  "path": "/content/my-site",
  "type": "cq:Page",
  "p.offset": "20",
  "p.limit": "10"
}
```
```
