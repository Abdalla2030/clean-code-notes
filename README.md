# Clean Code Notes

This repository contains the notes I collected while studying the Udemy course.

The notes summarize the main rules and examples from the course. The examples are written in Java for practice.

## 📚 Course Reference

**Course:** [Clean Code in Arabic - Udemy](https://www.udemy.com/course/clean-code-part-1-c-in-arabic/)

**Instructor:** [Mohamed Hassan](https://www.linkedin.com/in/mohamed-hassan-amer?originalSubdomain=ae)

**Language:** Arabic

**Duration:** 1 hour, 19 lectures

---

## File Structure Overview

The file is organized to reflect the course structure with full content in each part:

### Part 1: Naming Convention

All rules with bad vs good examples.

### Part 2: Functions

Includes large multi-line bad examples, corrected code, DRY, dependency magnets, and exception usage.

### Part 3: Comments

All content preserved including journal logs, commented-out code, and best practices.

### Part 4: Java Exceptions Summary

Full details on exceptions, including checked vs unchecked, fail fast, rollback, cleanup, re-throw, and what to avoid.

### Part 5: Certificate

Full certificate visual included.

Each section contains the full theory, all bad vs good examples, and practical key takeaways.

---

## Part 1: Naming Convention

### 1. Use Relevant Names

Bad:

```java
int d;
```

Good:

```java
int elapsedTimeInDays;
```

The good name explains exactly what the value represents.

---

### 2. Use Pronounceable Names

Bad:

```java
private Date modymdhms;
```

Good:

```java
private Date modificationFullDate;
```

A pronounceable name is easier to read, discuss, and remember.

---

### 3. Avoid Encodings and Prefixes

Bad:

```java
PhoneNumber phoneString;
```

Good:

```java
PhoneNumber phone;
```

Bad:

```java
private String m_dsc;
```

Good:

```java
private String description;
```

Avoid adding unnecessary prefixes, type hints, or unclear abbreviations to names.

---

### 4. Classes and Objects Should Be Nouns

Good examples:

```java
Customer
Account
User
```

Bad examples:

```java
Manage
Calculating
Draw
```

Classes represent things, so their names should usually be nouns.

---

### 5. Methods Should Be Verbs

Good examples:

```java
deletePage()
getName()
save()
```

Bad examples:

```java
Customer
Account
User
```

Methods represent actions, so their names should usually be verbs.

---

### Key Takeaway

Names should be descriptive, readable, and follow Java conventions. Classes represent things, while methods represent actions.

---

## Part 2: Functions

### 1. Function Implementation Should Be as Small as Possible

Keep the number of lines and parameters low to improve readability.

Bad example:

```java
public String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) {
    boolean isTestPage = pageData.hasAttribute("Test");
    if (isTestPage) {
        WikiPage testPage = pageData.getWikiPage();
        StringBuilder newPageContent = new StringBuilder();
        includeSetupPages(testPage, newPageContent, isSuite);
        newPageContent.append(pageData.getContent());
        includeTeardownPages(testPage, newPageContent, isSuite);
        pageData.setContent(newPageContent.toString());
    }
    return pageData.getHtml();
}
```

Good example:

```java
public String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) {
    if (isTestPage(pageData)) {
        includeSetupAndTeardownPages(pageData, isSuite);
    }
    return pageData.getHtml();
}
```

The good version hides the details behind meaningful helper methods.

---

### 2. Blocks Within `if`, `else`, and `while` Should Be Small

Bad example:

```java
if (id != null) {
    printStudent();
    logStudentAccess();
    updateLastSeenDate();
} else {
    printError();
    logError();
}
```

Good example:

```java
if (id != null) {
    handleExistingStudent();
} else {
    handleMissingStudent();
}
```

The main point is that each block should stay short and easy to understand.

---

### 3. Functions Should Do One Thing, Do It Well, and Do It Only

Bad example:

```java
public void addEmployee() {
    Employee employee = new Employee("Mohamed", "Hassan");
    saveUserToDatabase(employee);
    printSuccessMessage();
}
```

Good example:

```java
public boolean addEmployee() {
    Employee employee = new Employee("Mohamed", "Hassan");
    return saveUserToDatabase(employee);
}
```

The good version focuses on one clear responsibility.

---

### 4. Prefer Exceptions Over Returning Error Codes

Bad example:

```java
if (deletePage(page) == ErrorCode.ERROR) {
    // handle error
}
```

Good example:

```java
try {
    deletePage(page);
    registry.deleteReference(page.getName());
    configKeys.deleteKey(page.getName().makeKey());
} catch (Exception e) {
    logger.log(e.getMessage());
}
```

Exceptions make the main flow cleaner and prevent callers from forgetting to check error codes.

---

### 5. Don’t Repeat Yourself (DRY)

Bad example:

```java
private static void doSomething() {
    // repeated logic
}

private static void doSomethingAgain() {
    // same repeated logic
}
```

Good example:

```java
private static void printEmployee(Employee employee) {
    String format = "%s is %s, lives in %s, age %d";
    System.out.println(String.format(
        format,
        employee.getFirstName(),
        employee.getRelation(),
        employee.getAddress(),
        employee.getAge()
    ));
}
```

Repeated logic should be centralized in one place.

---

### 6. Avoid Dependency Magnets

Bad idea: returning error codes.

```java
enum ErrorCode {
    OK,
    INVALID,
    LOCKED,
    WAITING
}
```

Problems:

- Any function using this enum becomes tightly coupled to it.
- Adding new codes may require recompiling dependent code.

Better idea: use exceptions instead.

Benefits:

- New exception types can inherit from a base exception class.
- New exceptions can be added without forcing all dependent code to change.

---

### Key Takeaway

Functions should be small, focused, readable, and do only one thing. Keep blocks short, use exceptions for errors, and centralize repeated logic to follow DRY.

---

## Part 3: Comments

### 1. Comments Don’t Make Up for Bad Code

Comments should not be used to explain unclear code that could be improved by better naming, smaller functions, or clearer structure.

---

### 2. Explain Yourself in Code

Use meaningful names, small functions, and clear logic so the code explains itself as much as possible.

---

### 3. Good Comments Can Be Useful

Good comments include:

- **Legal comments**: copyright or license notices.
- **Clarification comments**: explain tricky logic.
- **Documentation comments**: public API descriptions or JavaDoc.
- **Informative comments**: extra context about usage.
- **TODO comments**: temporary notes that should be reviewed later.

---

### 4. Bad Comments Examples

#### Journal Comments

Avoid keeping long historical logs inside the code.

```java
/* Changes (from 11-Oct-2001)
 * 05-Nov-2018 : Added a getDescription() method, and eliminated NotableDate class (DG);
 * 12-Nov-2018 : IBD requires setDescription() method, now that NotableDate class is gone (DG); Changed getPreviousDayOfWeek(), getFollowingDayOfWeek() and getNearestDayOfWeek() to correct bugs (DG);
 * 05-Dec-2019 : Fixed bug in SpreadsheetDate class (DG);
 * 29-May-2020 : Moved the month constants into a separate interface (MonthConstants) (DG);
 * 29-May-2020 : Fixed bug in addMonths method (DG);
 * 04-Sep-2020 : Implemented Comparable. Updated the isInRange javadocs (DG);
 * 05-Jan-2020 : Fixed bug in addYears() method (150025) (DG);
 */
```

#### Commented-Out Code

Avoid leaving old commented-out code in the file.

```java
//private static void printEmployee(Employee employee)
//{
//    String format = "%s is %s, lives in %s, age %d";
//    System.out.println(String.format(
//        format,
//        employee.getFirstName(),
//        employee.getRelation(),
//        employee.getAddress(),
//        employee.getAge()
//    ));
//}
```

---

### Key Takeaway

Avoid cluttering your code with historical logs or commented-out code. Comments should clarify, not replace refactoring or meaningful code.

---

## Part 4: Java Exceptions Summary

This section summarizes Java exceptions, when to throw them, when to catch them, and what to avoid while handling them.

---

### What Does an Exception Mean?

In Java, when an error occurs inside a method, the method creates an exception object and throws it to the runtime system.

There are two main categories:

1. **Checked exceptions**: must be declared or handled.

Examples:

```java
IOException
SQLException
```

2. **Unchecked exceptions**: runtime exceptions that do not require explicit handling.

Examples:

```java
NullPointerException
ArithmeticException
```

Important base classes:

```java
java.lang.Exception
java.lang.RuntimeException
```

`java.lang.Exception` is the base class for exceptions, and `java.lang.RuntimeException` is the base class for unchecked exceptions.

---

### When to Throw Exceptions

#### 1. Fail Fast

```java
static void findWinner(int[] winners) {
    if (winners == null) {
        throw new IllegalArgumentException("Parameter 'winners' cannot be null");
    }
    otherMethodThatUsesTheArray(winners);
}
```

Benefits:

- Prevent continuing execution with invalid inputs.
- Save resources.
- Make debugging easier.

---

#### 2. Check Object Status

```java
void writeLog(File logFile) {
    if (!logFile.canWrite()) {
        throw new IllegalStateException("Log file cannot be written to because it is read-only");
    }

    // Write data to the log file
}
```

Always validate object state before operations to prevent invalid results or failures.

---

#### 3. Throw Specific Exceptions Only

```java
static int getValueFromArray(int[] array, int index) {
    try {
        return array[index];
    } catch (ArrayIndexOutOfBoundsException ex) {
        throw ex;
    }
}
```

Avoid throwing generic exceptions or creating new ones when the framework already provides a suitable exception.

---

#### 4. Don’t Return Error Codes

Throw exceptions instead of returning error codes.

Reason:

- Calling code may forget to check the returned error code.
- Exceptions make failure clearer and harder to ignore.

---

#### 5. Exceptions Are Expensive

```java
if (conn.getState() != ConnectionState.CLOSED) {
    conn.close();
}
```

Avoid using exceptions for normal control flow.

Reason:

- Exception creation is expensive.
- Stack trace collection using `fillInStackTrace` is expensive.
- Exceptions should be used only for exceptional situations.

---

### When to Catch Exceptions

#### 1. Catch Exceptions Only When You Can Handle Them Meaningfully

```java
try {
    File file = new File(filePath);
    Scanner scanner = new Scanner(file);
} catch (FileNotFoundException e) {
    System.out.println("File not found, please enter another path.");
    promptUserForAnotherFilePath();
}
```

Catch the exception only if you can do something useful with it.

---

#### 2. Catch Specific Exceptions Before Generic Ones

Specific exceptions should come before general exceptions.

Bad example:

```java
try {
    int value = numbers[index];
} catch (Exception e) {
    System.out.println("General error");
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Invalid index");
}
```

Good example:

```java
try {
    int value = numbers[index];
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Invalid index");
} catch (Exception e) {
    System.out.println("General error");
}
```

The generic exception should not be placed before the specific exception.

---

#### 3. Partial Handling and Re-throw

```java
try {
    File file = new File(filePath);
    Scanner scanner = new Scanner(file);
} catch (FileNotFoundException e) {
    logger.log(Level.SEVERE, "File not found", e);
    throw e;
}
```

Log and clean up if needed, but re-throw if you cannot fully handle the exception.

---

#### 4. Rollback and Cleanup

```java
try {
    account.deposit(amount);
    dbConnection.save();
} catch (Exception e) {
    dbConnection.rollback();
    throw e;
} finally {
    closeResources();
}
```

Use rollback and cleanup to keep the system in a consistent state.

---

#### 5. Swallow Exceptions Only at the Highest Layer

```java
try {
    mediator.send(new UpdatePostCommand(viewModel));
    return view(viewModel);
} catch (Exception e) {
    logger.log(Level.SEVERE, "Error updating post", e);
    return view(viewModel);
}
```

Only swallow exceptions at the topmost layer, such as UI or API layer. Lower layers should usually re-throw.

---

### Avoid in Handling Exceptions

#### 1. Do Not Change Program Flow Using Exceptions

Do not use exceptions to control normal program logic.

Good example:

```java
boolean isValid = validateProduct(product);
if (!isValid) {
    return view(product);
}

createProduct(product);
return view(product);
```

Exceptions should signal truly unexpected errors.

---

#### 2. Don’t Use Exceptions in Prototypes

Avoid passing or returning exceptions as normal parameters or return values.

Use this only in specialized patterns, such as an exception factory.

```java
Exception analyzeHttpError(int errorCode) {
    if (errorCode < 400) {
        throw new NotAnErrorException();
    }

    switch (errorCode) {
        case 403:
            return new ForbiddenException();
        case 404:
            return new NotFoundException();
        case 500:
            return new InternalServerErrorException();
        default:
            return new UnknownHttpErrorCodeException(errorCode);
    }
}
```

---

#### 3. Do Nothing: Pokémon Exception Handling

Never leave empty catch blocks.

Bad example:

```java
try {
    processData();
} catch (Exception e) {
    // Do nothing - avoid this
}
```

Empty catch blocks hide bugs and make debugging harder.

---

#### 4. Don’t Miss `finally` for Cleanup

`finally` blocks are used to always clean up resources, regardless of exceptions.

```java
FileOutputStream file = null;
try {
    file = new FileOutputStream("file.txt");
    file.write(0xFF);
} finally {
    if (file != null) {
        file.close();
    }
}
```

---

### Key Takeaway

Validate inputs, fail fast, throw specific exceptions, catch only when you can handle the error meaningfully, clean up resources, and never use exceptions for normal control flow.

---

## Part 5: Certificate

<img width="1600" height="1190" alt="UC-5851ca79-8b80-497b-9f75-3b8d44a27e9b" src="https://github.com/user-attachments/assets/c2f204bb-f5e1-45ee-8f49-fa8ed5285931" />
