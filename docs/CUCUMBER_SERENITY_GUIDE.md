# Cucumber & Serenity BDD Testing Guide

## Overview

This guide demonstrates **Cucumber BDD** and **Serenity BDD** frameworks for automated testing of the Spring PetClinic application.

### What is Cucumber?

**Cucumber** is a Behavior-Driven Development (BDD) tool that allows you to write tests in plain English (Gherkin syntax). It bridges the gap between business stakeholders and technical teams.

**Key Components:**
- **Feature Files**: Plain text files describing application behavior (.feature)
- **Step Definitions**: Java code implementing the feature steps
- **Runners**: Classes that execute the tests

### What is Serenity BDD?

**Serenity BDD** is a reporting and test automation framework that provides:
- Rich, detailed test reports with screenshots
- Test organization and management
- Integration with Cucumber for BDD testing
- Living documentation generated from your tests

## Running the Tests

### Option 1: Run with Maven (Cucumber only)

```bash
# Run all Cucumber tests
./mvnw test -Dtest=CucumberTestRunner

# Run specific feature
./mvnw test -Dtest=CucumberTestRunner -Dcucumber.filter.tags="@owner"

# Run with specific tags
./mvnw test -Dtest=CucumberTestRunner -Dcucumber.filter.tags="not @disabled"
```

### Option 2: Run with Serenity Reports (Recommended)

```bash
# Run tests and generate Serenity reports
./mvnw clean verify -Dtest=SerenityTestRunner

# View reports (after running tests)
open target/site/serenity/index.html
```

### Option 3: Run from IDE

1. **IntelliJ IDEA / Eclipse:**
   - Right-click on `CucumberTestRunner.java`
   - Select "Run CucumberTestRunner"

2. **Run specific feature:**
   - Right-click on any `.feature` file
   - Select "Run Feature"

## Understanding the Test Structure

### 1. Feature Files (Gherkin)

Location: `src/test/resources/features/`

Example from `owner_management.feature`:

```gherkin
Feature: Owner Management
  
  Scenario: Search for an existing owner
    When I search for owners with last name "Franklin"
    Then I should see owner "George Franklin" in the results
    And the owner should have address "110 W. Liberty St."
    And the owner should have city "Madison"
```

**Gherkin Keywords:**
- `Feature`: High-level description of a software feature
- `Scenario`: A specific test case
- `Given`: Preconditions/setup
- `When`: Actions performed
- `Then`: Expected outcomes
- `And/But`: Additional steps
- `Background`: Common steps for all scenarios in a feature
- `Scenario Outline`: Template for data-driven tests with Examples

### 2. Step Definitions

Location: `src/test/java/org/springframework/samples/petclinic/cucumber/steps/`

Example from `OwnerManagementSteps.java`:

```java
@When("I search for owners with last name {string}")
public void iSearchForOwnersWithLastName(String lastName) {
    ownerActions.searchOwnersByLastName(lastName);
}

@Then("I should see owner {string} in the results")
public void iShouldSeeOwnerInTheResults(String fullName) {
    boolean found = ownerActions.isOwnerInResults(fullName);
    assertThat(found).as("Owner %s should be in search results", fullName).isTrue();
}
```

**Key Points:**
- Methods annotated with `@Given`, `@When`, `@Then`, `@And`
- Cucumber expressions match feature file steps
- Parameters extracted from step text using {string}, {int}, etc.
- Step definitions delegate to action classes

### 3. Action Classes (Serenity Steps)

Location: `src/test/java/org/springframework/samples/petclinic/cucumber/actions/`

Example from `OwnerActions.java`:

```java
@Component
public class OwnerActions {
    
    @Autowired
    private OwnerRepository ownerRepository;
    
    @Step("Search for owners by last name: {0}")
    public void searchOwnersByLastName(String lastName) {
        searchResults = ownerRepository.findByLastNameStartingWith(
            lastName, PageRequest.of(0, 10));
    }
}
```

**Benefits:**
- `@Step` annotation creates detailed steps in Serenity reports
- Reusable business logic
- Separation of concerns (tests vs. implementation)
- Spring dependency injection enabled via `@Component` and `@Autowired`

### 4. Configuration Classes

Location: `src/test/java/org/springframework/samples/petclinic/cucumber/config/`

**CucumberSpringConfiguration.java:**
- Annotated with `@CucumberContextConfiguration`
- Loads Spring Boot application context
- Enables dependency injection in tests

## Report Locations

After running tests, reports are generated in:

### Cucumber Reports
```
target/cucumber-reports/
├── cucumber.html   # HTML report
└── cucumber.json   # JSON data
```

### Serenity Reports
```
target/site/serenity/
├── index.html      # Main dashboard
├── features/       # Feature results
└── requirements/   # Requirements traceability
```

## Example Test Execution Flow

Let's trace how a test scenario executes:

### 1. Feature File defines the test:
```gherkin
Scenario: Search for an existing owner
  When I search for owners with last name "Franklin"
  Then I should see owner "George Franklin" in the results
```

### 2. Cucumber finds matching Step Definition:
```java
@When("I search for owners with last name {string}")
public void iSearchForOwnersWithLastName(String lastName) {
    ownerActions.searchOwnersByLastName(lastName);
}
```

### 3. Step Definition calls Action Class:
```java
@Step("Search for owners by last name: {0}")
public void searchOwnersByLastName(String lastName) {
    searchResults = ownerRepository.findByLastNameStartingWith(
        lastName, PageRequest.of(0, 10));
    if (!searchResults.isEmpty()) {
        selectedOwner = searchResults.getContent().get(0);
    }
}
```

### 4. Assertion verifies the result:
```java
@Then("I should see owner {string} in the results")
public void iShouldSeeOwnerInTheResults(String fullName) {
    boolean found = ownerActions.isOwnerInResults(fullName);
    assertThat(found).as("Owner %s should be in search results", fullName).isTrue();
}
```

### 5. Serenity records each step in the report:
- Step name with parameters
- Status (passed/failed)
- Screenshots (if configured)
- Execution time

## Available Test Features

### Owner Management (`owner_management.feature`)
- Search for existing owners
- Create new owners
- Update owner information
- View owner details with pets

### Pet Management (`pet_management.feature`)
- Add new pets to owners
- Update pet information
- Validate pet birth dates (data-driven with Scenario Outline)

### Visit Management (`visit_management.feature`)
- Record new visits
- View visit history
- Verify visits are ordered by date

## Writing New Tests

### Step 1: Create a Feature File

Create a new `.feature` file in `src/test/resources/features/`:

```gherkin
Feature: Veterinarian Management
  As a clinic administrator
  I want to manage veterinarians
  So that I can track available medical staff

  Scenario: Add a new veterinarian
    Given the PetClinic application is running
    When I add a new veterinarian named "Dr. Smith"
    Then the veterinarian should be successfully added
    And the veterinarian should appear in the list
```

### Step 2: Create Step Definitions

Create a new class in `src/test/java/org/springframework/samples/petclinic/cucumber/steps/`:

```java
public class VeterinarianManagementSteps {
    
    @Steps
    VetActions vetActions;
    
    @When("I add a new veterinarian named {string}")
    public void iAddANewVeterinarianNamed(String name) {
        vetActions.addVeterinarian(name);
    }
    
    @Then("the veterinarian should be successfully added")
    public void theVeterinarianShouldBeSuccessfullyAdded() {
        assertThat(vetActions.isVetAdded()).isTrue();
    }
}
```

### Step 3: Create Action Class

Create a new class in `src/test/java/org/springframework/samples/petclinic/cucumber/actions/`:

```java
@Component
public class VetActions {
    
    @Autowired
    private VetRepository vetRepository;
    
    @Step("Add veterinarian: {0}")
    public void addVeterinarian(String name) {
        Vet vet = new Vet();
        vet.setFirstName(name.split(" ")[0]);
        vet.setLastName(name.split(" ")[1]);
        vetRepository.save(vet);
    }
    
    @Step("Verify veterinarian was added")
    public boolean isVetAdded() {
        // Implementation
        return true;
    }
}
```

## Best Practices

### Writing Feature Files

1. **Use business language**: Write scenarios that non-technical stakeholders can understand
2. **Keep scenarios focused**: One scenario should test one behavior
3. **Use meaningful scenario names**: Describe what the test does, not how it does it
4. **Use Background for common setup**: Avoid repetition across scenarios

Example of a well-written scenario:
```gherkin
Background:
  Given the PetClinic application is running

Scenario: Register a new pet owner
  When I register a new owner "John Doe" with telephone "555-1234"
  Then the owner should be successfully registered
  And I should be able to search for "John Doe"
```

### Using Data Tables

For scenarios with multiple parameters:

```gherkin
When I create a new owner with the following details:
  | firstName | John            |
  | lastName  | Doe             |
  | address   | 123 Main St     |
  | city      | Springfield     |
  | telephone | 5551234567      |
```

```java
@When("I create a new owner with the following details:")
public void iCreateANewOwner(DataTable dataTable) {
    Map<String, String> data = dataTable.asMap(String.class, String.class);
    ownerActions.createOwner(data);
}
```

### Using Scenario Outlines

For data-driven tests:

```gherkin
Scenario Outline: Validate pet birth date
  When I try to add a pet with birth date "<birthDate>"
  Then the pet creation should "<result>"
  
  Examples:
    | birthDate  | result  |
    | 2020-01-15 | succeed |
    | 2025-12-31 | fail    |
```

### Writing Step Definitions

1. **Keep steps reusable**: Generic steps work across multiple scenarios
2. **Use meaningful parameter names**: Make code self-documenting
3. **Avoid logic in steps**: Delegate to action classes
4. **Use descriptive assertion messages**: Help debugging when tests fail

```java
@Then("I should see owner {string} in the results")
public void iShouldSeeOwnerInTheResults(String fullName) {
    boolean found = ownerActions.isOwnerInResults(fullName);
    assertThat(found)
        .as("Owner %s should be in search results", fullName)
        .isTrue();
}
```

### Using Serenity Steps

1. **Annotate with @Step**: Provides detailed reporting
2. **Use descriptive step names**: They appear in reports
3. **Parameter placeholders**: Use {0}, {1}, etc. to include parameters in step names
4. **Organize by domain**: Group related actions together

```java
@Step("Search for owners by last name: {0}")
public void searchOwnersByLastName(String lastName) {
    // Implementation
}
```

### Spring Integration

1. **Use @Component on action classes**: Enable Spring dependency injection
2. **Use @Autowired for repositories**: Access data layer
3. **Test data setup**: Create test data in @Given steps
4. **Use in-memory database**: H2 configured for fast tests

## Understanding Reports

### Serenity Report Features

1. **Dashboard**: Overview of test execution
   - Total scenarios
   - Pass/fail rates
   - Execution time
   - Test coverage

2. **Feature Results**: Detailed results per feature
   - Scenario status
   - Step-by-step execution
   - Screenshots on failures
   - Execution timeline

3. **Requirements**: Traceability matrix
   - Features mapped to requirements
   - Test coverage per requirement

4. **Test Details**: Drill-down to individual tests
   - Step execution details
   - Parameter values
   - Execution context
   - Error messages and stack traces

### Interpreting Test Results

**Green (Passed)**: Test executed successfully
- All assertions passed
- No exceptions thrown

**Red (Failed)**: Test failed
- Assertion failure
- Check error message in report
- Review screenshot if available

**Yellow (Pending)**: Step not implemented
- Step definition missing
- Use Cucumber snippets to implement

**Blue (Skipped)**: Step skipped
- Previous step failed
- Test tagged as @disabled

## Troubleshooting

### Tests Not Found
**Problem**: Cucumber can't find step definitions

**Solution**:
- Verify `glue` parameter in runner matches package structure
- Check feature files are in `src/test/resources/features/`
- Ensure step definition classes are in the correct package

### Spring Context Issues
**Problem**: Dependency injection not working

**Solution**:
- Ensure `CucumberSpringConfiguration.java` is present
- Check `@CucumberContextConfiguration` annotation
- Verify action classes are annotated with `@Component`
- Check `@Autowired` fields are correct

### Report Not Generated
**Problem**: Serenity reports not created

**Solution**:
- Run `./mvnw clean verify` instead of just `test`
- Check Serenity plugin configuration in `pom.xml`
- Verify `serenity.properties` exists in `src/test/resources/`

### Undefined Steps
**Problem**: "Undefined step" error

**Solution**:
- Copy Cucumber snippet from console output
- Add to appropriate step definition class
- Ensure step text matches exactly (case-sensitive)

### Database Issues
**Problem**: Data not found or conflicts

**Solution**:
- Tests use in-memory H2 database
- Database is recreated for each test run
- Check `spring.jpa.hibernate.ddl-auto=create-drop` in config

## Advanced Topics

### Tags

Organize and filter tests using tags:

```gherkin
@owner @smoke
Scenario: Search for an existing owner
  When I search for owners with last name "Franklin"
  Then I should see owner "George Franklin" in the results
```

Run specific tags:
```bash
./mvnw test -Dtest=CucumberTestRunner -Dcucumber.filter.tags="@smoke"
./mvnw test -Dtest=CucumberTestRunner -Dcucumber.filter.tags="@owner and not @slow"
```

### Hooks

Execute code before/after scenarios:

```java
@Before
public void setUp() {
    // Runs before each scenario
}

@After
public void tearDown() {
    // Runs after each scenario
}

@Before("@database")
public void setUpDatabase() {
    // Runs only for scenarios tagged with @database
}
```

### Custom Parameter Types

Define reusable parameter types:

```java
@ParameterType(".*")
public Owner owner(String name) {
    return ownerRepository.findByName(name);
}
```

Use in steps:
```gherkin
Given owner "John Doe" exists
```

## Continuous Integration

### GitHub Actions Example

```yaml
name: Run Cucumber Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up JDK
        uses: actions/setup-java@v2
        with:
          java-version: '17'
      - name: Run tests
        run: ./mvnw clean verify -Dtest=SerenityTestRunner
      - name: Publish test results
        uses: actions/upload-artifact@v2
        with:
          name: serenity-reports
          path: target/site/serenity/
```

## Additional Resources

- **Cucumber Documentation**: https://cucumber.io/docs
- **Serenity BDD**: https://serenity-bdd.github.io/
- **Gherkin Syntax**: https://cucumber.io/docs/gherkin/reference/
- **AssertJ**: https://assertj.github.io/doc/

## Next Steps

1. **Add more scenarios** to existing features
2. **Create new feature files** for other domains (vets, specialties)
3. **Implement web UI tests** using Serenity WebDriver
4. **Add API tests** using REST-assured with Serenity
5. **Integrate with CI/CD** pipeline for automated execution
6. **Generate living documentation** from your tests
7. **Add performance tests** using Serenity with JMeter integration

## Quick Reference

### Common Gherkin Keywords
```gherkin
Feature: High-level description
  Background: Common setup for all scenarios
  
  Scenario: Test case name
    Given precondition
    When action
    Then expected result
    And additional step
    But exception case
  
  Scenario Outline: Template with examples
    When I do <action>
    Then I see <result>
    
    Examples:
      | action | result |
      | login  | success |
```

### Common Annotations
```java
// Step Definitions
@Given("step text")
@When("step text")
@Then("step text")
@And("step text")

// Hooks
@Before
@After
@BeforeStep
@AfterStep

// Serenity
@Step("Step description: {0}")
@Steps  // Inject action class

// Spring
@Component
@Autowired
```

### Running Tests
```bash
# All tests
./mvnw test -Dtest=CucumberTestRunner

# With Serenity reports
./mvnw clean verify -Dtest=SerenityTestRunner

# Specific tags
./mvnw test -Dtest=CucumberTestRunner -Dcucumber.filter.tags="@smoke"

# View reports
open target/site/serenity/index.html
```

Happy Testing! 🎉
