# Spring PetClinic - AI Agent Instructions

## Architecture Overview

This is a Spring Boot 4.0 application demonstrating classic MVC patterns with Spring Data JPA. The codebase is organized by **domain modules** (`owner/`, `vet/`, `system/`), not by technical layers.

### Domain Structure
- **owner/**: Entities (Owner, Pet, Visit, PetType), Controllers, Repositories
- **vet/**: Veterinarian management (Vet, Specialty entities)
- **system/**: Cross-cutting concerns (caching, error handling)

Each package is self-contained with its entities, repositories, and controllers co-located.

## Key Patterns & Conventions

### 1. Controller Pattern with @ModelAttribute
Controllers extensively use `@ModelAttribute` methods to pre-populate model objects **before** request handlers execute. This is critical for understanding the flow:

```java
// Example from VisitController - runs BEFORE @GetMapping/@PostMapping
@ModelAttribute("visit")
public Visit loadPetWithVisit(@PathVariable("ownerId") int ownerId, 
                               @PathVariable("petId") int petId) {
    // Loads owner, validates pet, creates Visit
    // Model objects are READY when handler methods execute
}
```

**Pattern**: Controllers inject repositories via constructor, use `@InitBinder` to protect ID fields, and leverage `@ModelAttribute` for entity loading.

### 2. Repository Interfaces (No @Repository)
All repositories extend `JpaRepository` directly - **no @Repository annotation needed**. Spring Data auto-implements these interfaces:

```java
public interface OwnerRepository extends JpaRepository<Owner, Integer> {
    Page<Owner> findByLastNameStartingWith(String lastName, Pageable pageable);
    // Spring Data generates implementation from method name
}
```

### 3. View Resolution
- Returns String view names: `"owners/findOwners"` → `/templates/owners/findOwners.html`
- Uses Thymeleaf templates in `src/main/resources/templates/`
- Flash attributes for messages: `redirectAttributes.addFlashAttribute("message", "...")`

## Build & Development Workflow

### Primary Commands
```bash
# Build (requires Java 25 to build, runs on 17+)
./mvnw clean package

# Run with live reload (picks up Java changes after recompile)
./mvnw spring-boot:run

# Generate CSS from SCSS (required after modifying petclinic.scss)
./mvnw package -P css

# Run with different database profile
./mvnw spring-boot:run -Dspring-boot.run.profiles=mysql
# or
export SPRING_PROFILES_ACTIVE=postgres && ./mvnw spring-boot:run
```

### Testing Strategy
- **@WebMvcTest**: For controller slice tests (mock repositories)
- **@DataJpaTest**: For repository tests (H2 in-memory)
- **@SpringBootTest**: Full integration tests
- **PetClinicIntegrationTests.main()**: Dev-time test app with H2 + DevTools
- **MySqlTestApplication**: Uses Testcontainers for MySQL integration testing
- **PostgresIntegrationTests**: Uses Docker Compose for Postgres testing

### Database Configuration
- **Default**: H2 in-memory (no setup required)
- **MySQL**: Set profile `spring.profiles.active=mysql`, use docker-compose: `docker compose up mysql`
- **Postgres**: Set profile `spring.profiles.active=postgres`, use: `docker compose up postgres`
- Schema/data scripts in `src/main/resources/db/{h2,mysql,postgres}/`

## Important Implementation Details

### Caching Strategy
- `VetRepository.findAll()` uses `@Cacheable("vets")` - cache configured in `CacheConfiguration`
- Uses Caffeine/JCache with JMX statistics enabled
- Cache name must match configuration: `cm.createCache("vets", ...)`

### Entity Relationships
- **Owner** → **Pet** (OneToMany, bidirectional via `owner.addPet(pet)`)
- **Pet** → **Visit** (OneToMany, EAGER fetch with `@OrderBy("date ASC")`)
- **Pet** → **PetType** (ManyToOne)
- **Vet** → **Specialty** (ManyToMany)

### Pagination Pattern
Controllers use Spring Data's `Pageable` for pagination:
```java
Pageable pageable = PageRequest.of(page - 1, pageSize);
Page<Owner> results = repository.findByLastNameStartingWith(lastName, pageable);
```

### Error Handling
- Controllers throw `IllegalArgumentException` for not-found entities
- Example: `owner.orElseThrow(() -> new IllegalArgumentException("Owner not found with id: " + id))`
- Custom error page at `templates/error.html`

## UI/Frontend

### Modern CSS System
- **Primary styles**: `src/main/resources/static/resources/css/petclinic.css` (9700+ lines)
- **Source**: Generated from `src/main/scss/petclinic.scss` + Bootstrap 5.3.8
- **Modern design**: Purple-blue gradient (`#667eea` → `#764ba2`), rounded corners, smooth animations
- **To modify CSS**: Edit SCSS, run `./mvnw package -P css` to recompile

### Thymeleaf Conventions
- Fragment reuse via `fragments/` directory
- Layout templates use `th:replace` for common elements (navbar, footer)
- Form binding with `th:object` and `th:field`

## Common Development Tasks

### Adding a New Entity
1. Create entity in appropriate domain package (e.g., `owner/`)
2. Extend `BaseEntity` or `NamedEntity` for ID/name fields
3. Create repository interface extending `JpaRepository`
4. Add controller with `@ModelAttribute` for entity loading
5. Create Thymeleaf templates in matching directory

### Testing New Controllers
- Use `@WebMvcTest(YourController.class)` for slice testing
- Mock repositories with `@MockitoBean`
- Note: Tests use `@DisabledInNativeImage` and `@DisabledInAotMode` for GraalVM compatibility
- See `OwnerControllerTests` for comprehensive example

### Database Migration
1. Add SQL scripts to `src/main/resources/db/{h2,mysql,postgres}/`
2. Update `schema.sql` for structure changes
3. Update `data.sql` for test data
4. Test with: `./mvnw spring-boot:run` (H2), then with profiles for other DBs

## Project-Specific Quirks

- **No @Service layer**: Controllers directly inject repositories (simpler demo pattern)
- **Package-private controllers**: Controllers are not public (intentional design)
- **@Nullable annotations**: Extensive use of JSR-305 nullability annotations
- **Runtime hints**: `PetClinicRuntimeHints` for GraalVM native image support
- **Test instruction**: `.github/instructions/test-generation.instructions.md` - don't auto-implement BDD step definitions

## Key Files Reference
- Main entry: `PetClinicApplication.java`
- Properties: `application.properties`, `application-mysql.properties`, `application-postgres.properties`
- Cache config: `system/CacheConfiguration.java`
- Example patterns: `owner/OwnerController.java`, `owner/VisitController.java`
