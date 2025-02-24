# Testing Practices Cheat Sheet

## Testing Libraries and Frameworks

- Jest: Primary testing framework
- Mockito: Mocking library for Java
- PowerMock: Extended mocking capabilities for static methods and constructors

## Mocking and Stubbing Strategies

### Jest Mocks

```javascript
jest.mock('../path/to/module');
const mockFunction = jest.fn();
```

### Mockito Mocks

```java
@Mock
private DependencyClass mockDependency;

when(mockDependency.someMethod()).thenReturn(expectedValue);
```

### PowerMock for Static Methods

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest(StaticClass.class)
public class TestClass {
    @Test
    public void testStaticMethod() {
        PowerMockito.mockStatic(StaticClass.class);
        when(StaticClass.staticMethod()).thenReturn(expectedValue);
    }
}
```

## Fake Implementations

### In-Memory Repositories

```java
public class InMemoryUserRepository implements UserRepository {
    private Map<String, User> users = new HashMap<>();

    @Override
    public User findById(String id) {
        return users.get(id);
    }

    @Override
    public void save(User user) {
        users.put(user.getId(), user);
    }
}
```

### Test Doubles

```java
public class TestHttpClient implements HttpClient {
    @Override
    public HttpResponse sendRequest(HttpRequest request) {
        // Simulate network response
        return new HttpResponse(200, "Test response");
    }
}
```

## Testing Patterns

### Arrange-Act-Assert (AAA)

```java
@Test
public void testUserRegistration() {
    // Arrange
    UserService userService = new UserService(mockUserRepository);
    User user = new User("john@example.com", "password");

    // Act
    boolean result = userService.registerUser(user);

    // Assert
    assertTrue(result);
    verify(mockUserRepository).save(user);
}
```

### Given-When-Then (BDD Style)

```java
@Test
public void givenExistingUser_whenUpdatingProfile_thenProfileIsUpdated() {
    // Given
    User existingUser = new User("1", "john@example.com");
    when(mockUserRepository.findById("1")).thenReturn(existingUser);

    // When
    userService.updateProfile("1", "John Doe", "123-456-7890");

    // Then
    verify(mockUserRepository).save(argThat(user -> 
        user.getName().equals("John Doe") && 
        user.getPhone().equals("123-456-7890")
    ));
}
```

## Test Coverage

- Use JaCoCo for Java code coverage
- Configure minimum coverage thresholds in build tools (e.g., Maven, Gradle)

## Parameterized Tests

```java
@ParameterizedTest
@CsvSource({
    "1, 1, 2",
    "2, 3, 5",
    "10, -5, 5"
})
void testAddition(int a, int b, int expected) {
    assertEquals(expected, Calculator.add(a, b));
}
```

## Exception Testing

```java
@Test
void testDivisionByZero() {
    assertThrows(ArithmeticException.class, () -> {
        Calculator.divide(10, 0);
    });
}
```

## Integration Tests

- Use `@SpringBootTest` for Spring Boot applications
- Configure test containers for database integration tests

## Best Practices

1. Keep tests independent and isolated
2. Use descriptive test method names
3. Avoid logic in tests
4. Maintain test data separately
5. Use setup and teardown methods for common operations
6. Mock external dependencies and services
7. Use assertions to validate expected outcomes
8. Write both positive and negative test cases
9. Aim for high test coverage, but focus on critical paths
10. Regularly run and maintain tests