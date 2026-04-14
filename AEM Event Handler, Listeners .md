In AEM, eventing allows you to trigger logic when something changes in the repository (e.g., a page is created, an asset is deleted, or a property is modified). 

There are three primary ways to handle events in AEM:
1.  **OSGi Event Handlers:** The most common and recommended way (asynchronous).
2.  **Sling Resource Change Listeners:** High-level Sling abstraction for CRUD operations.
3.  **JCR Observation Listeners:** Low-level JCR events (synchronous or asynchronous).

---

### Part 1: Detailed Explanation

#### 1. OSGi Event Handlers
OSGi events are fired by various services in AEM. When you move a page, AEM fires an OSGi event with a specific "Topic."
*   **Best For:** Page moves, replication (activation), asset processing, and custom application events.
*   **Execution:** Asynchronous (non-blocking).

#### 2. Sling Resource Change Listeners
Introduced to replace the older `ResourceEventHandler`, this is the modern Sling way to listen for changes to resources.
*   **Best For:** Listening to specific resource paths for Added, Changed, or Removed events.
*   **Execution:** Asynchronous.

#### 3. JCR Observation Listeners
This is the lowest level, communicating directly with the Oak repository.
*   **Best For:** When you need to know exactly which JCR property changed at a very granular level.
*   **Warning:** Can impact performance if not filtered correctly, as they can be synchronous.

---

### Part 2: Implementation

#### 1. OSGi Event Handler (Example: Listen for Page Moves)
```java
package com.mysite.core.listeners;

import org.osgi.service.component.annotations.Component;
import org.osgi.service.event.Event;
import org.osgi.service.event.EventConstants;
import org.osgi.service.event.EventHandler;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import com.day.cq.wcm.api.PageEvent;

@Component(service = EventHandler.class,
           immediate = true,
           property = {
               EventConstants.EVENT_TOPIC + "=" + PageEvent.EVENT_TOPIC
           })
public class PageEventHandler implements EventHandler {
    private static final Logger LOG = LoggerFactory.getLogger(PageEventHandler.class);

    @Override
    public void handleEvent(Event event) {
        // Get the path of the page from the event
        String path = (String) event.getProperty("path");
        LOG.info("Event Topic: {}, Page Path: {}", event.getTopic(), path);
    }
}
```

#### 2. Sling Resource Change Listener (Example: Listen for Content Updates)
```java
package com.mysite.core.listeners;

import org.apache.sling.api.resource.observation.ResourceChange;
import org.apache.sling.api.resource.observation.ResourceChangeListener;
import org.osgi.service.component.annotations.Component;
import java.util.List;

@Component(service = ResourceChangeListener.class,
           immediate = true,
           property = {
               ResourceChangeListener.PATHS + "=/content/mysite",
               ResourceChangeListener.CHANGES + "=ADDED",
               ResourceChangeListener.CHANGES + "=CHANGED"
           })
public class MyResourceListener implements ResourceChangeListener {
    @Override
    public void onChange(List<ResourceChange> changes) {
        for (ResourceChange change : changes) {
            System.out.println("Resource changed: " + change.getPath() + " Type: " + change.getType());
        }
    }
}
```

---

### Part 3: 100 Interview Questions & Answers

#### Basics (1-20)
1.  **What is an Event Handler in AEM?**<br />Answer :  An OSGi service that listens for specific topics/actions.
2.  **Difference between an Event Listener and an Event Handler?**<br />Answer :  In AEM context, "Handler" usually refers to OSGi events, while "Listener" refers to JCR Observation or Sling Resource changes.
3.  **Are OSGi Event Handlers synchronous or asynchronous?**<br />Answer :  Asynchronous.
4.  **How do you define an event topic?**<br />Answer :  Using the `EventConstants.EVENT_TOPIC` property in the `@Component` annotation.
5.  **Which interface must an OSGi Event Handler implement?**<br />Answer :  `org.osgi.service.event.EventHandler`.
6.  **How do you listen to multiple topics?**<br />Answer :  Pass an array: `property = {EventConstants.EVENT_TOPIC + "={topic1, topic2}"}`.
7.  **What is the "Topic" for Page events?**<br />Answer :  `com/day/cq/wcm/core/page`.
8.  **What is the "Topic" for Replication (Activation) events?**<br />Answer :  `com/day/cq/replication`.
9.  **What is the "Topic" for Asset events?**<br />Answer :  `com/adobe/granite/asset/core`.
10. **Where do you see the list of all fired events in AEM?**<br />Answer :  In the Web Console under **OSGi > Events** (`/system/console/events`).
11. **Why should we avoid heavy logic inside a handler?**<br />Answer :  It can bottleneck the event queue and potentially crash the system.
12. **How do you offload heavy work from a handler?**<br />Answer :  Use **Sling Jobs**.
13. **What is an "Event Filter"?**<br />Answer :  An LDAP-like filter to narrow down events (e.g., only specific paths).
14. **How to define a filter?**<br />Answer :  `EventConstants.EVENT_FILTER + "=(path=/content/mysite/*)"`.
15. **Can you create custom event topics?**<br />Answer :  Yes, using the `EventAdmin` service to post events.
16. **What is `EventAdmin`?**<br />Answer :  The OSGi service used to trigger (send) events.
17. **Difference between `sendEvent` and `postEvent`?**<br />Answer :  `send` is synchronous; `post` is asynchronous.
18. **How do you get properties from an `Event` object?**<br />Answer :  `event.getProperty("propertyName")`.
19. **What is the default runmode for listeners?**<br />Answer :  They run on both Author and Publisher unless restricted.
20. **How do you restrict a listener to run only on Author?**<br />Answer :  Use OSGi configurations targeted at the `author` runmode.

#### JCR Observation (21-40)
21. **Which interface is used for JCR Observation?**<br />Answer :  `javax.jcr.observation.EventListener`.
22. **How to register a JCR Listener?**<br />Answer :  Via `observationManager.addEventListener()`.
23. **What is `ObservationManager`?**<br />Answer :  The JCR service that manages listeners.
24. **How to get `ObservationManager`?**<br />Answer :  `session.getWorkspace().getObservationManager()`.
25. **Is JCR Observation synchronous?**<br />Answer :  It can be both, but defaults to asynchronous in most Oak implementations.
26. **What are the event types in JCR?**<br />Answer :  `NODE_ADDED`, `NODE_REMOVED`, `PROPERTY_ADDED`, `PROPERTY_CHANGED`, etc.
27. **What is the "IsDeep" parameter in JCR observation?**<br />Answer :  If true, it listens to the path and all its descendants.
28. **What is the "Uuid" parameter?**<br />Answer :  Limits events to specific node IDs.
29. **What is the "NodeTypeName" parameter?**<br />Answer :  Limits events to specific types (e.g., `nt:unstructured`).
30. **When does a JCR Event Listener stop?**<br />Answer :  When the session that registered it is closed.
31. **Why is JCR Observation discouraged for high-level tasks?**<br />Answer :  It's too granular and complex compared to Sling/OSGi events.
32. **Can JCR Observation see changes made by a specific session?**<br />Answer :  Yes, using the `noLocal` flag.
33. **What is the `onEvent(EventIterator events)` method?**<br />Answer :  The method where JCR events are processed.
34. **Does JCR Observation work across a cluster?**<br />Answer :  Yes, Oak handles cluster-wide events.
35. **What is an External Event in JCR?**<br />Answer :  An event triggered on a different node in a cluster.
36. **How to identify an external event?**<br />Answer :  Check `event instanceof JackrabbitEvent` and call `isExternal()`.
37. **What happens if you don't call `session.save()`?**<br />Answer :  No events will be fired.
38. **How to filter events by User ID?**<br />Answer :  JCR observation allows filtering by specific user IDs during registration.
39. **Is JCR Observation thread-safe?**<br />Answer :  The implementation must handle thread safety.
40. **How many listeners can you have?**<br />Answer :  No hard limit, but performance degrades with many deep listeners.

#### Sling Resource Change Listener (41-60)
41. **What is the package for `ResourceChangeListener`?**<br />Answer :  `org.apache.sling.api.resource.observation`.
42. **What property defines the paths?**<br />Answer :  `ResourceChangeListener.PATHS`.
43. **What property defines the change types?**<br />Answer :  `ResourceChangeListener.CHANGES`.
44. **Name the types of `ResourceChange`.** `ADDED`, `CHANGED`, `REMOVED`, `PROVIDER_ADDED`, `PROVIDER_REMOVED`.
45. **How is it better than OSGi `EventHandler`?**<br />Answer :  It’s more resource-centric and easier to filter by path.
46. **What is the `onChange` method?**<br />Answer :  It receives a `List<ResourceChange>`.
47. **How to get the user who made the change?**<br />Answer :  Use `change.getUserId()`.
48. **Is `ResourceChangeListener` cluster-aware?**<br />Answer :  Yes.
49. **How to handle events only from the local node?**<br />Answer :  Use the `PROPERTY_EXTERNAL` check.
50. **Can it listen to property changes?**<br />Answer :  Yes, via the `CHANGED` type.
51. **How to use Globbing patterns?**<br />Answer :  Paths support standard Sling globbing (e.g., `**`).
52. **Is it faster than JCR Observation?**<br />Answer :  Usually, as it’s better optimized for the Sling Resource tree.
53. **What happens if the service is unregistered?**<br />Answer :  Sling stops sending events to it immediately.
54. **Can you listen to multiple paths?**<br />Answer :  Yes, by providing an array of strings.
55. **Does it require a JCR Session?**<br />Answer :  No, it works on the Resource level.
56. **What is `ExternalResourceChangeListener`?**<br />Answer :  An extension for specifically handling cluster events.
57. **How to ignore events from a specific path?**<br />Answer :  Use a negative filter or logic inside `onChange`.
58. **Is the order of events guaranteed?**<br />Answer :  No.
59. **Can you modify the resource that triggered the event?**<br />Answer :  Yes, but be careful of infinite loops!
60. **How to prevent infinite loops?**<br />Answer :  Check the user ID or a "marker property" on the node.

#### Implementation & Best Practices (61-80)
61. **Write the annotation for a Page Move listener.**
    `@Component(property = {EventConstants.EVENT_TOPIC + "=com/day/cq/wcm/core/page"})`.
62. **How to get the `ResourceResolver` inside an Event Handler?**<br />Answer : 
    Use `ResourceResolverFactory.getServiceResourceResolver()`.
63. **Should you use `administrative` ResourceResolver?**<br />Answer :  No, use a **Service User**.
64. **What is a "Service User"?**<br />Answer :  A system user with specific permissions mapped to a bundle.
65. **How to map a service user?**<br />Answer :  In the **Apache Sling Service User Mapper Service** configuration.
66. **What is the risk of an asynchronous listener?**<br />Answer :  The data might change again before the listener finishes.
67. **How to handle high-frequency events?**<br />Answer :  Use a **Throttling** mechanism or a Job queue.
68. **What is a Sling Job?**<br />Answer :  A task that is guaranteed to run at least once, even after a restart.
69. **How to trigger a Sling Job from a Listener?**<br />Answer : 
    ```java
    jobManager.addJob("my/job/topic", payloadMap);
    ```
70. **Why are Sling Jobs better than Thread pools?**<br />Answer :  They are persistent and cluster-aware.
71. **How to debug a listener that isn't firing?**<br />Answer :  Check logs, ensure the component is "Active", and verify topics.
72. **What runmode is used for "Author only" logic?**<br />Answer :  `author`.
73. **Can listeners trigger Workflows?**<br />Answer :  Yes, using the `WorkflowService`.
74. **How to find the event topic for a custom AEM action?**<br />Answer :  Check the **Events** tab in the Web Console while performing the action.
75. **What is the `immediate = true` attribute for?**<br />Answer :  Ensures the component starts as soon as the bundle is active.
76. **How to listen for Replication events?**<br />Answer :  Topic: `ReplicationAction.EVENT_TOPIC`.
77. **How to distinguish between Activation and Deactivation?**<br />Answer : 
    `ReplicationAction action = ReplicationAction.fromEvent(event);` then `action.getType()`.
78. **What is the `ResourceChangeListener.CHANGES` value for all types?**<br />Answer :  `{"ADDED", "CHANGED", "REMOVED"}`.
79. **How to log event data safely?**<br />Answer :  Use placeholders: `LOG.info("Event: {}", event.getTopic())`.
80. **How to ignore events triggered by the listener itself?**<br />Answer :  Check `event.getProperty("userId")` against your Service User name.

#### Scenarios & Advanced (81-100)
81. **Scenario: Update a property on a page when it is activated.**
    Use a Replication Event Handler; get path; use Service User to update property.
82. **Scenario: Send an email when an asset is deleted.**
    Use `ResourceChangeListener` on `/content/dam`; filter for `REMOVED`.
83. **Scenario: Sync data to an external API when a node is modified.**
    Event Handler triggers a **Sling Job**; the Job performs the API call.
84. **Scenario: Validate content before it is saved.**
    **Events cannot do this!** Use a **JCR Validator** or a **Sling Request Filter** (pre-processing).
85. **Why can't Event Handlers cancel an action?**<br />Answer :  Because they are asynchronous and run *after* the action has happened.
86. **What is the difference between `Event` and `PageEvent`?**<br />Answer :  `PageEvent` is a wrapper providing helper methods for WCM actions.
87. **How to handle events in AEM as a Cloud Service?**<br />Answer :  Same way, but ensure high performance as Cloud is more sensitive to resource usage.
88. **Does `ResourceChangeListener` work with `Sling Resource Aliases`?**<br />Answer :  It works on the underlying path, not the alias.
89. **How to handle bulk page moves?**<br />Answer :  The event will contain multiple paths; ensure your code iterates through them.
90. **Can you listen to changes in `/etc` or `/conf`?**<br />Answer :  Yes, if permissions allow.
91. **What is the limit of the OSGi event queue?**<br />Answer :  Configurable in the **Apache Sling Event Support** OSGi config.
92. **How to check if an event was missed?**<br />Answer :  Check logs for "queue full" or "dropped events" warnings.
93. **Can we use events for search indexing?**<br />Answer :  AEM already does this via Oak indexing, but you can listen to track indexing status.
94. **How to listen to Bundle changes?**<br />Answer :  Topic: `org/osgi/framework/BundleEvent/STARTED`.
95. **How to capture user login events?**<br />Answer :  Topic: `org/apache/sling/auth/core/Authenticator/LOGIN`.
96. **How to pass data between two listeners?**<br />Answer :  Use a shared JCR node or a shared OSGi Cache service.
97. **Can you prioritize certain events?**<br />Answer :  Not directly in OSGi, but you can prioritize **Sling Job Queues**.
98. **How to unit test an Event Handler?**<br />Answer :  Use **AemContext** (Sling Mocks) to manually trigger the `handleEvent()` method.
99. **What is the maximum path length for JCR Observation?**<br />Answer :  No limit, but deep paths are slower to process.
100. **Final Best Practice:** Always close `ResourceResolver` in a `finally` block when used inside a listener.
