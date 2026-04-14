In OSGi Declarative Services, the `target` attribute within the `@Reference` annotation is used to filter which specific service implementation should be injected based on the service's properties.

### Example Scenario

Imagine you have a `MessageService` interface with two different implementations: one for **Email** and one for **SMS**.

#### 1. The Service Interface
```java
public interface MessageService {
    void send(String message);
}
```

#### 2. The Implementations (with properties)
Each implementation defines a unique property so they can be identified.

```java
@Component(service = MessageService.class, property = {"transport=sms"})
public class SmsService implements MessageService {
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

@Component(service = MessageService.class, property = {"transport=email"})
public class EmailService implements MessageService {
    public void send(String message) {
        System.out.println("Sending Email: " + message);
    }
}
```

#### 3. The Consumer (using the target filter)
If you want to ensure your component **only** receives the SMS implementation, you use the `target` attribute to filter by the `transport` property.

```java
@Component(service = NotificationManager.class)
public class NotificationManager {

    // This will only inject the service where transport=sms
    @Reference(target = "(transport=sms)")
    private MessageService smsService;

    public void notifyUser(String msg) {
        smsService.send(msg);
    }
}
```

### Key Points:
*   **LDAP Syntax:** The `target` value uses standard LDAP filter syntax. For example: `(property=value)`.
*   **Multiple Properties:** You can use complex filters like `(&(type=fast)(provider=aws))` to match multiple properties.
*   **Dynamic Configuration:** In many systems (like Adobe Experience Manager), these target filters can also be overridden via OSGi Configuration (PID) in the web console, allowing you to change which service is injected without changing the code.
