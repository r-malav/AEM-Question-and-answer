Adobe Experience Manager (AEM) Workflows allow you to automate a series of steps to process content or assets. In AEM, workflows are essential for tasks like content approval, asset ingestion, and page publishing.

---

### Part 1: AEM Workflow - Detailed Explanation

#### Core Components:
1.  **Workflow Model:** The visual representation (blueprint) of the process. It consists of steps and transitions.
2.  **Workflow Step:** Individual units of work (e.g., a "Participant Step" for human approval or a "Process Step" for automated Java code).
3.  **Workflow Payload:** The specific resource (page or asset) the workflow is acting upon.
4.  **Workflow Instance:** A running version of a Model.
5.  **WorkItem:** The specific task assigned to a user or process within a running instance.
6.  **Launcher:** A rule that automatically triggers a workflow based on events (e.g., node created, modified).

#### Storage Locations:
*   **Models:** `/var/workflow/models`
*   **Instances (Runtime):** `/var/workflow/instances`
*   **Launcher:** `/conf/global/settings/workflow/launcher`

---

### Part 2: Implementation (Custom Process Step)

To implement custom logic, you create a Java class implementing the `WorkflowProcess` interface.

**1. Java Implementation:**
```java
package com.mysite.core.workflows;

import com.adobe.granite.workflow.WorkflowException;
import com.adobe.granite.workflow.WorkflowSession;
import com.adobe.granite.workflow.exec.WorkItem;
import com.adobe.granite.workflow.exec.WorkflowProcess;
import com.adobe.granite.workflow.metadata.MetaDataMap;
import org.osgi.service.component.annotations.Component;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.jcr.Node;
import javax.jcr.Session;

@Component(
    service = WorkflowProcess.class,
    property = {"process.label=My Custom AEM Workflow Step"}
)
public class CustomStep implements WorkflowProcess {
    private static final Logger LOG = LoggerFactory.getLogger(CustomStep.class);

    @Override
    public void execute(WorkItem workItem, WorkflowSession workflowSession, MetaDataMap processArgs) throws WorkflowException {
        try {
            // Get the payload path (e.g., /content/mysite/en/home)
            String path = workItem.getWorkflowData().getPayload().toString();
            LOG.info("Processing payload: {}", path);

            // Get JCR Session
            Session session = workflowSession.adaptTo(Session.class);
            Node node = session.getNode(path);

            // Add a property to the node
            if (node != null) {
                node.setProperty("workflowProcessed", "true");
                session.save();
            }
        } catch (Exception e) {
            LOG.error("Workflow failed: ", e);
            throw new WorkflowException(e.getMessage());
        }
    }
}
```

**2. AEM Configuration:**
1.  Go to **Tools > Workflow > Models**.
2.  Create a new Model and Edit it.
3.  Add a **Process Step**.
4.  Under the "Process" tab, select "My Custom AEM Workflow Step".
5.  Check "Handler Advance".
6.  Sync the model.

---

### Part 3: 50 Interview Questions & Answers (With Code Snippets)

#### Basics
1. **What is an AEM Workflow?**
   Automated series of steps to manage content/assets.
2. **What are the three main parts of a workflow?**
   Model, Step, Payload.
3. **Difference between Workflow and Launcher?**
   Model is the design; Launcher is the trigger based on JCR events.
4. **How do you start a workflow manually?**
   Via the Workflow console or the "Start Workflow" button in the Page/Asset editor.
5. **Where are workflows stored in AEM 6.5 vs AEM as a Cloud Service?**
   AEM 6.5: `/etc/workflow/models`. AEMaaCS/Recent: `/var/workflow/models`.

#### Development & Code
6. **Which interface is used to create a custom process step?**
   `com.adobe.granite.workflow.exec.WorkflowProcess`.
7. **What is the use of `MetaDataMap` in the `execute` method?**
   To read arguments passed to the step via the AEM UI.
   ```java
   String myArg = processArgs.get("PROCESS_ARGS", "default");
   ```
8. **How to get the JCR Session inside a workflow?**
   `Session session = workflowSession.adaptTo(Session.class);`
9. **How to access the payload path?**
   `workItem.getWorkflowData().getPayload().toString();`
10. **What is the purpose of `Handler Advance` in a Process Step?**
    If checked, the workflow automatically moves to the next step after the Java code completes.
11. **How do you handle errors in a workflow?**
    Throw a `WorkflowException`.
12. **Can you inject an OSGi Service into a Workflow Process?**
    Yes, using `@Reference`.
13. **How do you create a Dynamic Participant Step?**
    Implement `ParticipantStepChooser` and return the `Principal` ID.
14. **Explain the `WorkflowSession` object.**
    It provides methods to manage workflow instances, find items, and terminate workflows.
15. **How to send an email via workflow?**
    Use the out-of-the-box "Participant Step" or write a custom step using `MessageGatewayService`.

#### Advanced Logic
16. **What is an OR Split?**
    A conditional branch where only one path is taken.
17. **What is an AND Split?**
    A parallel branch where all paths execute simultaneously.
18. **Difference between `WorkItem` and `Workflow` object?**
    `WorkItem` is the current task; `Workflow` is the entire running instance.
19. **How to bypass workflow for specific users?**
    Configure the Launcher to exclude specific users in the "Exclude List".
20. **What are Workflow Packages?**
    A way to group multiple resources (pages/assets) and process them through a single workflow.
21. **How do you terminate a workflow programmatically?**
    `workflowSession.terminateWorkflow(workItem.getWorkflow());`
22. **What is the "Transient Workflow"?**
    Workflows that do not save history to `/var/workflow/instances`, improving performance for high-volume asset processing.
23. **How to make a workflow Transient?**
    Check the "Transient Workflow" box in the Workflow Model properties.
24. **How to retrieve workflow metadata like 'Initiator'?**
    `workItem.getWorkflow().getInitiator();`
25. **How to find all active workflow instances?**
    Use `workflowSession.getWorkflows(new String[]{"RUNNING"});`

#### Asset Processing
26. **What is the DAM Update Asset workflow?**
    The default workflow that generates renditions and extracts metadata for assets.
27. **How to disable DAM Update Asset for a specific folder?**
    Use a launcher with a condition or use the "Exclude" property in AEMaaCS.
28. **What is a "Sub-workflow"?**
    A workflow model called inside another workflow model using the "Container Step".
29. **How to pass data between steps?**
    Store it in `workItem.getWorkflow().getWorkflowData().getMetaDataMap()`.
30. **Explain Workflow Offloading.**
    Moving workflow execution to a different AEM instance (usually for heavy asset processing).

#### Implementation Scenarios (With Code snippets)
31. **How to restrict a workflow to run only on a specific path via code?**
    ```java
    if (!path.startsWith("/content/mysite")) return; 
    ```
32. **How to get the page title from a payload?**
    ```java
    ResourceResolver rr = workflowSession.adaptTo(ResourceResolver.class);
    Page page = rr.resolve(path).adaptTo(Page.class);
    String title = page.getTitle();
    ```
33. **How to trigger a workflow programmatically?**
    ```java
    WorkflowModel model = workflowSession.getModel("/var/workflow/models/request_for_activation");
    workflowSession.startWorkflow(model, workflowSession.newWorkflowData("JCR_PATH", "/content/path"));
    ```
34. **How to create a custom Participant Step chooser?**
    ```java
    @Component(service = ParticipantStepChooser.class, property = {"chooser.label=Manager Chooser"})
    public class MyChooser implements ParticipantStepChooser {
        public String getParticipant(WorkItem item, WorkflowSession session, MetaDataMap args) {
            return "manager-group";
        }
    }
    ```
35. **How to update a specific property on the metadata node of a workflow instance?**
    `workItem.getMetaDataMap().put("status", "completed");`

#### Performance & Administration
36. **How to purge old workflow instances?**
    Use the "Workflow Purge Configuration" in OSGi.
37. **How to monitor workflow health?**
    AEM Workflow Dashboard or JMX MBeans.
38. **What is the impact of not purging workflows?**
    Significant JCR bloating and slow query performance.
39. **How to restart a failed workflow step?**
    Via the Workflow Inbox or the Instances console.
40. **How many types of steps are available in AEM?**
    Participant, Process, Container, Goto, OR/AND Split, Dialog, External Process.

#### AEM as a Cloud Service (AEMaaCS)
41. **Is "Workflow Purge" needed in AEMaaCS?**
    No, it's managed automatically.
42. **Can we use "DAM Update Asset" in AEMaaCS?**
    It's replaced by "Asset Compute Service," but the workflow still exists for post-processing.
43. **Where is the Workflow UI in AEM Cloud?**
    Under Tools -> Workflow.
44. **How to define workflow models in AEMaaCS?**
    They must be defined in the `ui.content` package and synced via Git.
45. **What happens to running workflows during a Cloud deployment?**
    AEM tries to persist state, but it's best to avoid long-running workflows during deployment windows.

#### Troubleshooting
46. **A workflow is stuck in "RUNNING" state. Why?**
    Check for an "Infinite Loop" or a "Participant Step" waiting for user action.
47. **Custom process step is not showing in the dropdown. Why?**
    Check if the OSGi component is active and `process.label` is defined.
48. **Launcher is not triggering. Why?**
    Check if "Enable" is checked and if the globbing path is correct.
49. **How to log payload info for debugging?**
    `LOG.info("Workflow Payload: {}", workItem.getWorkflowData().getPayload());`
50. **How to skip a step if a condition is met?**
    Use a "Goto Step" with a script or a custom "Process Step" that evaluates logic and moves the item.
