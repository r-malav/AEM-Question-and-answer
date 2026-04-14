Unit testing in AEM is primarily done using **JUnit 5**, **Mockito**, and **AEM Mocks** (by wcm.io). These tools allow you to test your logic (OSGi services, Sling Models, Servlets) without needing a running AEM instance.

---

### Part 1: Key Concepts

1.  **JUnit 5:** The testing framework (annotations like `@Test`, `@BeforeEach`).
2.  **Mockito:** Used to "mock" or simulate the behavior of complex objects (like a `Request` or an external API service).
3.  **AEM Mocks (`AemContext`):** The most critical part. It provides a mock JCR repository, mock Sling resources, and mock AEM objects in memory.

---

### Part 2: Implementation (Testing a Sling Model)

#### 1. The Sling Model to Test
```java
@Model(adaptables = Resource.class)
public class SimpleModel {
    @ValueMapValue
    private String title;

    public String getUpperCaseTitle() {
        return (title != null) ? title.toUpperCase() : "NO TITLE";
    }
}
```

#### 2. The JUnit 5 Test Class
```java
import io.wcm.testing.aem.mock.junit5.AemContext;
import io.wcm.testing.aem.mock.junit5.AemContextExtension;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import static org.junit.jupiter.api.Assertions.assertEquals;

@ExtendWith(AemContextExtension.class)
class SimpleModelTest {

    private final AemContext context = new AemContext();

    @BeforeEach
    void setUp() {
        // Create a mock resource in the in-memory JCR
        context.create().resource("/content/test", "title", "hello world");
    }

    @Test
    void testGetUpperCaseTitle() {
        // Adapt the resource to the model
        context.currentResource("/content/test");
        SimpleModel model = context.request().getResource().adaptTo(SimpleModel.class);

        // Assert the logic
        assertEquals("HELLO WORLD", model.getUpperCaseTitle());
    }
}
```

---

### Part 3: 50 Interview Questions & Answers

#### Basics (1-10)
1.  **What is the difference between Unit testing and Integration testing in AEM?**
    Unit tests verify a single class in isolation (using mocks). Integration tests (like ITs in AEM) run against a real AEM instance.
2.  **What is AEM Mocks?**
    A library by wcm.io that provides an in-memory JCR and AEM environment for fast testing.
3.  **Why use Mockito?**
    To simulate the behavior of dependencies (e.g., a service that fetches data from a database) so you can focus on testing your own class logic.
4.  **What is `AemContext`?**
    The central object in AEM Mocks that gives access to a mock `ResourceResolver`, `Request`, `Response`, and `PageManager`.
5.  **Which annotation is used to run tests with AEM Context in JUnit 5?**
    `@ExtendWith(AemContextExtension.class)`.
6.  **How do you mock a method call in Mockito?**
    `when(myService.getData()).thenReturn("Mock Value");`
7.  **What is `@Mock` vs `@InjectMocks`?**
    `@Mock` creates a fake object. `@InjectMocks` creates the real object and injects the mocks into it.
8.  **Where do you place your test classes?**
    In `src/test/java`.
9.  **How do you run JUnit tests?**
    Via Maven (`mvn test`) or through an IDE (right-click -> Run Test).
10. **What is the purpose of `@BeforeEach`?**
    To run setup code (like initializing mocks or creating resources) before every test method.

#### AEM Mocks & Context (11-25)
11. **How to load a JSON file into the mock JCR?**
    `context.load().json("/com/mysite/testdata.json", "/content/mysite");`
12. **How to register an OSGi service in a test?**
    `context.registerService(MyService.class, new MyServiceImpl());`
13. **How to get a `ResourceResolver` from `AemContext`?**
    `context.resourceResolver();`
14. **How to create a Page in a test?**
    `context.create().page("/content/sample", "template-path");`
15. **How to set a property on a mock resource?**
    `context.create().resource("/path", "propName", "propValue");`
16. **How to simulate a specific Request Parameter?**
    `context.request().setParameterMap(Map.of("id", "123"));`
17. **How to adapt a mock resource to a Sling Model?**
    `SimpleModel model = context.request().getResource().adaptTo(SimpleModel.class);`
18. **Can AEM Mocks handle `ResourceResolverFactory`?**
    Yes, it is pre-registered in the `AemContext`.
19. **What is the default JCR type in AEM Mocks?**
    Usually `RESOURCERESOLVER_MOCK` (in-memory).
20. **How to verify an OSGi service was called?**
    `verify(myService, times(1)).doAction();`
21. **How to test a Servlet?**
    Register the servlet, call `servlet.doGet(context.request(), context.response())`, and check the response status.
22. **How to test a Workflow Process?**
    Mock `WorkItem` and `WorkflowSession`, then call `process.execute()`.
23. **How to simulate a "Page" object?**
    `Page page = context.pageManager().getPage("/content/path");`
24. **How to mock a BundleContext?**
    `context.bundleContext();`
25. **How to test `ValueMapValue` in Sling Models?**
    Create a resource with properties in `AemContext` and adapt it to the model.

#### Mockito Deep Dive (26-40)
26. **How to mock a void method?**
    `doNothing().when(myService).voidMethod();`
27. **What is `ArgumentCaptor`?**
    A Mockito tool to capture the arguments passed to a method for later assertion.
28. **How to throw an exception in a mock?**
    `when(myService.call()).thenThrow(new RuntimeException());`
29. **What is `spy()` in Mockito?**
    A partial mock. It calls real methods unless they are explicitly mocked.
30. **How to verify a method was NEVER called?**
    `verify(myService, never()).someMethod();`
31. **What is the difference between `thenReturn()` and `thenAnswer()`?**
    `thenReturn` returns a fixed value. `thenAnswer` allows logic to decide what to return based on input.
32. **How to reset a mock?**
    `reset(myMock);`
33. **How to mock a static method?**
    Use `mockStatic(MyClass.class)` (requires `mockito-inline` dependency).
34. **How to verify the order of method calls?**
    Use `InOrder inOrder = inOrder(mock1, mock2);`.
35. **How to inject a mock into a Sling Model if it's an OSGi service?**
    `context.registerService(MyService.class, myMockService);` before adapting to the model.
36. **What is `@Captor`?**
    Annotation for `ArgumentCaptor` to avoid manual instantiation.
37. **How to mock a final class?**
    Modern Mockito (with `mockito-inline`) can mock final classes.
38. **How to test a private method?**
    You shouldn't. Test the public method that calls the private one.
39. **How to handle `null` returns in Mockito?**
    By default, mocks return `null` for objects or default values for primitives.
40. **How to verify multiple calls with different arguments?**
    `verify(mock).method("arg1"); verify(mock).method("arg2");`

#### Advanced & Scenarios (41-50)
41. **How do you test a Sling Model that uses `@PostConstruct`?**
    When you call `adaptTo()`, AEM Mocks automatically triggers the `@PostConstruct` method.
42. **How to test if a Sling Model returns the correct component properties?**
    Load the component node via JSON into `AemContext` and assert the getters.
43. **How to test a service that uses `ResourceResolverFactory` to get a service user?**
    ```java
    // AEM Mocks provides a default factory, so this just works:
    ResourceResolver resolver = factory.getServiceResourceResolver(null);
    ```
44. **How to mock a `Query` execution?**
    Mock the `QueryBuilder`, `Query`, and `SearchResult` objects.
45. **What is the dependency for AEM JUnit 5 testing?**
    `io.wcm.testing.aem-mock.junit5`.
46. **How to test the `SlingHttpServletResponse` output?**
    `String out = context.response().getOutputAsString();`.
47. **How to test a scheduler?**
    Instantiate the Scheduler class and manually call the `run()` method.
48. **How to mock a `Node` object?**
    `Node node = mock(Node.class);` and mock its `getProperty()` calls.
49. **How to test code that uses `PageManager`?**
    `PageManager pm = context.pageManager();` (AEM Mocks provides a fully functional PageManager).
50. **How to test a model that adapts from `SlingHttpServletRequest`?**
    Use `context.request().adaptTo(MyModel.class)`.

---

### Code Snippet: Mocking an OSGi Service inside a Model
If your Sling Model uses `@OSGiService`, you must register the mock in the context **before** adapting.

```java
@ExtendWith(AemContextExtension.class)
class MyModelTest {
    private final AemContext context = new AemContext();
    
    @Mock
    private MyBackendService backendService;

    @BeforeEach
    void setup() {
        MockitoAnnotations.openMocks(this);
        // This makes the mock service available to the Sling Model
        context.registerService(MyBackendService.class, backendService);
        context.create().resource("/content/p1", "sling:resourceType", "myapp/components/c1");
    }

    @Test
    void testModel() {
        when(backendService.getValue()).thenReturn("Mocked Data");
        
        context.currentResource("/content/p1");
        MyModel model = context.request().adaptTo(MyModel.class);
        
        assertEquals("Mocked Data", model.getDataFromService());
    }
}
```
