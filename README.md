# Clean Code Notes

This repository is a summary of the Udemy course: [Clean Code in Arabic](https://www.udemy.com/course/clean-code-part-1-c-in-arabic/).
It covers the main rules and examples from the course in **three parts**: Naming Convention, Functions, and Comments. All examples are translated into Java for practice.

---

## Part 1: Naming Convention

1. **Use relevant names**

   * ❌ `int d;` → unclear
   * ✅ `int elapsedTimeInDays;` → clear

2. **Use pronounceable names**

   * ❌ `private DateTime modymdhms;` → hard to read
   * ✅ `private DateTime modificationFullDate;` → easy to read

3. **Avoid encodings and prefixes**

   * ❌ `PhoneNumber phoneString;` → name tied to type
   * ✅ `PhoneNumber phone;` → clear
   * ❌ `private string m_dsc;` → unclear
   * ✅ `private string description;` → clear

4. **Classes and objects should be nouns**

   * ✅ `Customer, Account, User`
   * ❌ `Manage, Calculating, Draw`

5. **Methods should be verbs**

   * ✅ `DeletePage, GetName, Save`
   * ❌ `Customer, Account, User`

> Key idea: Names should be descriptive, readable, and follow Java conventions. Classes represent things → nouns; methods represent actions → verbs.

---

## Part 2: Functions

1. **Function implementation should be as small as possible**
   Keep line count and parameters low for readability.

**Bad Example:**

```java
public string RenderPageWithSetupsAndTeardowns(PageData pageData, bool isSuite) {
    bool isTestPage = pageData.HasAttribute("Test");
    if (isTestPage) {
        WikiPage testPage = pageData.GetWikiPage();
        StringBuffer newPageContent = new StringBuffer();
        IncludeSetupPages(testPage, newPageContent, isSuite);
        newPageContent.append(pageData.GetContent());
        IncludeTeardownPages(testPage, newPageContent, isSuite);
        pageData.setContent(newPageContent.ToString());
    }
    return pageData.GetHtml();
}
```

**Good Example:**

```java
public string RenderPageWithSetupsAndTeardowns(PageData pageData, bool isSuite) {
    if (IsTestPage(pageData))
        IncludeSetupAndTeardownPages(pageData, isSuite);
    return pageData.GetHtml();
}
```

2. **Blocks within `if`, `else`, `while` should be one line long**

**Bad Example:**

```java
if (id != null) {
    PrintStudent();
} else {
    PrintError();
}
```

**Good Example:**

```java
if (id != null)
    PrintStudent();
else
    PrintError();
```

3. **Functions should do one thing, do it well, and do it only**

**Bad Example:**

```java
public void AddEmployee() {
    var employee = new Employee() { FirstName="Mohamed", LastName="Hassan" };
    SaveUserToDatabase(employee);
    PrintSuccessMessage();
}
```

**Good Example:**

```java
public bool AddEmployee() {
    var employee = new Employee() { FirstName="Mohamed", LastName="Hassan" };
    return SaveUserToDatabase(employee);
}
```

4. **Prefer exceptions over returning error codes**

**Bad Example:**

```java
if (DeletePage(page) == ErrorCode.Error) { ... }
```

**Good Example:**

```java
try {
    DeletePage(page);
    registry.DeleteReference(page.name);
    configKeys.DeleteKey(page.name.makeKey());
} catch (Exception e) {
    logger.log(e.GetMessage());
}
```

5. **Don’t Repeat Yourself (DRY)**

**Bad Example:**

```java
private static void DoSomething() { ... }
private static void DoSomethingAgain() { ... }
```

**Good Example:**

```java
private static void PrintEmployee(Employee employee) {
    string format = "{0} is {1}, lives in {2}, age {3}";
    Console.WriteLine(format, employee.FirstName, employee.Relation, employee.Address, employee.Age);
}
```

6. **Avoid dependency magnets**

* Bad: returning error codes (e.g., `enum ErrorCode { Ok, Invalid, Locked, Waiting }`)

  * Any function using this enum becomes tightly coupled. Adding new codes requires recompiling dependent code.
* Good: use exceptions instead

  * New exception types inherit from a base exception class.
  * Can be added without forcing recompilation or redeployment.

> Key idea: Functions should be small, focused, readable, and do only one thing. Keep blocks short, use exceptions for errors, and centralize repeated logic to follow DRY.

---

## Part 3: Comments

1. **Comments don’t make up for bad code**

   * Focus on writing clear, self-explanatory code instead of relying on comments.

2. **Explain yourself in code**

   * Use meaningful names, small functions, and clear logic.

3. **Good comments can be:**

   * **Legal** → copyright, license notices
   * **Clarification** → explain tricky logic
   * **Documentation** → public API descriptions, `<summary>` tags
   * **Informative** → extra context about usage
   * **TODO** → temporary notes, to be removed after review

4. **Bad comments examples**

   * **Journal comments** – long historical logs inside the code

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

* **Commented-out code** – old code left in the file

```java
//private static void PrintEmployee(Employee employee)
//{
//    string format = "{0} is {1}, lives in {2}, age {3}";
//    Console.WriteLine(format,
//        employee.FirstName,
//        employee.Relation,
//        employee.Address,
//        employee.Age);
//}
```

> **Key idea:** Avoid cluttering your code with historical logs or commented-out code. Comments should clarify, not replace refactoring or meaningful code.

---

# Java Exceptions Summary

This section summarizes Java exceptions and when to throw them. It builds upon the Clean Code principles we have been adding to the README.

## What Does an Exception Mean?

* In Java, when an error occurs within a method, the method **creates an exception object** and throws it to the runtime system.
* There are **two main categories**:

  1. **Checked exceptions** – must be declared or handled (e.g., `IOException`, `SQLException`).
  2. **Unchecked exceptions** – runtime exceptions that don’t require explicit handling (e.g., `NullPointerException`, `ArithmeticException`).
* `java.lang.Exception` is the **base class** for all exceptions.
* `java.lang.RuntimeException` is the base for unchecked exceptions.

## When to Throw Exceptions (Fail Fast Principle)

### 1. Fail Fast

```java
static void findWinner(int[] winners) {
    if (winners == null) {
        throw new IllegalArgumentException("Parameter 'winners' cannot be null");
    }
    otherMethodThatUsesTheArray(winners);
}
```

* Prevent continuing execution with invalid inputs.
* Failing early saves resources and simplifies debugging.

### 2. Check Object Status

```java
void writeLog(File logFile) {
    if (!logFile.canWrite()) {
        throw new IllegalStateException("Log file cannot be written to (read-only)");
    }
    // Else write data to the log
}
```

* Always validate object state before operations to prevent invalid results or failures.
* Saves resources and debugging time.

### 3. Throw Specific Exceptions Only

```java
static int getValueFromArray(int[] array, int index) {
    try {
        return array[index];
    } catch (ArrayIndexOutOfBoundsException ex) {
        throw ex; // Use default exception in framework if sufficient
    }
}
```

* Avoid throwing generic exceptions or creating new ones when the framework provides a suitable exception.
* Improves readability and reduces unnecessary wrapping.

### 4. Don’t Return Error Codes

* Always throw exceptions instead of returning error codes.
* Safer because the calling code cannot forget to check the error.

### 5. Exceptions Are Expensive

```java
if (conn.getState() != ConnectionState.Closed) {
    conn.close();
}
```

* Avoid using exceptions for normal control flow.
* Exception creation and stack trace collection (`fillInStackTrace`) is expensive.
* Use exception only for truly exceptional situations.

> Key idea: Validate inputs, fail fast, throw specific exceptions, and avoid exceptions for regular control flow to maintain performance and readability.

## Part 4: Java Exceptions – When to Throw and Catch

### What Does an Exception Mean?

* In Java, when an error occurs within a method, the method **creates an exception object** and throws it to the runtime system.
* Two main categories:

  1. **Checked exceptions** – must be declared or handled (`IOException`, `SQLException`).
  2. **Unchecked exceptions** – runtime exceptions that don’t require explicit handling (`NullPointerException`, `ArithmeticException`).
* `java.lang.Exception` is the base class for all exceptions; `java.lang.RuntimeException` is the base for unchecked exceptions.

### When to Throw Exceptions (Fail Fast Principle)

1. **Fail Fast**

```java
static void findWinner(int[] winners) {
    if (winners == null) {
        throw new IllegalArgumentException("Parameter 'winners' cannot be null");
    }
    otherMethodThatUsesTheArray(winners);
}
```

* Validate inputs early, save resources, and simplify debugging.

2. **Check Object Status**

```java
void writeLog(File logFile) {
    if (!logFile.canWrite()) {
        throw new IllegalStateException("Log file cannot be written to (read-only)");
    }
    // Else write data to the log
}
```

* Always check object state before operations to prevent invalid results or failures.

3. **Throw Specific Exceptions Only**

```java
static int getValueFromArray(int[] array, int index) {
    try {
        return array[index];
    } catch (ArrayIndexOutOfBoundsException ex) {
        throw ex; // Use framework default if sufficient
    }
}
```

* Avoid throwing generic exceptions or unnecessary wrapping.

4. **Don’t Return Error Codes**

* Throw exceptions instead of returning error codes. Safer because calling code cannot forget to check.

5. **Exceptions Are Expensive**

```java
if (conn.getState() != ConnectionState.Closed) {
    conn.close();
}
```

* Avoid using exceptions for normal control flow; only for exceptional situations.

6. **When to Catch Exceptions**

* Catch exceptions **only when you can handle them meaningfully**.
* Always catch **specific exceptions before generic ones**.

```java
try {
    File file = new File(filePath);
    Scanner sc = new Scanner(file);
} catch (FileNotFoundException e) {
    System.out.println("File not found, please enter another path.");
    promptUserForAnotherFilePath();
}
```

7. **Partial Handling and Re-throw**

```java
try {
    File file = new File(filePath);
    Scanner sc = new Scanner(file);
} catch (FileNotFoundException e) {
    logger.log(Level.SEVERE, "File not found", e);
    throw e;
}
```

* Log and clean up if needed, but re-throw if you cannot fully handle the exception.

8. **Rollback / Cleanup**

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

* Clean up side effects or rollback to maintain consistent state.

9. **Swallow Exceptions at Highest Layer**

```java
try {
    mediator.send(new UpdatePostCommand(viewModel));
    return view(viewModel);
} catch (Exception e) {
    logger.log(Level.SEVERE, "Error updating post", e);
    return view(viewModel);
}
```

* Only swallow exceptions at the topmost layer (UI or API). Lower layers should re-throw.

## Part 5: Avoid in Handling Exceptions

### 1. Do Not Change Program Flow Using Exceptions

* Do not use exceptions to control normal program logic.
* Exceptions should signal truly unexpected errors.

```java
boolean isValid = validateProduct(product);
if (!isValid) return view(product);

createProduct(product);
return view(product);
```

### 2. Don’t Use Exceptions in Prototypes

* Avoid passing or returning exceptions as parameters or return values.
* Only use exceptions in specialized patterns like an exception factory.

```java
Exception analyzeHttpError(int errorCode) {
    if (errorCode < 400) throw new NotAnErrorException();
    switch(errorCode) {
        case 403: return new ForbiddenException();
        case 404: return new NotFoundException();
        case 500: return new InternalServerErrorException();
        default: return new UnknownHttpErrorCodeException(errorCode);
    }
}
```

### 3. Do Nothing (Pokémon Exception Handling)

* Never leave empty catch blocks.
* Empty catch blocks hide bugs and make debugging harder.

```java
try {
    processData();
} catch (Exception e) {
    // Do nothing – avoid!
}
```

### 4. Don’t Miss Finally for Cleanup

* `finally` blocks are used to **always clean up resources**, regardless of exceptions.

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

> Key idea: Always handle exceptions correctly, clean up resources, and never use them for controlling normal flow.

---

## 🎓 Certificate

<img width="1600" height="1190" alt="UC-5851ca79-8b80-497b-9f75-3b8d44a27e9b" src="https://github.com/user-attachments/assets/c2f204bb-f5e1-45ee-8f49-fa8ed5285931" />

