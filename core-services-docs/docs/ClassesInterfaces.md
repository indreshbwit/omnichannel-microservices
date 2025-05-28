# Classes and Interfaces

## IUserService

```csharp
public interface IUserService {
    Task<UserDto> GetUserAsync(Guid id);
    Task CreateUserAsync(CreateUserRequest request);
}
```

## UserService : IUserService

Handles business logic for user-related operations.
