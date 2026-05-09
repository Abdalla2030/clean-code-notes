# Clean Code Notes

## Part 1: Naming Convention

1. **Use relevant names**
   - ❌ `int d;` → unclear
   - ✅ `int elapsedTimeInDays;` → clear

2. **Use pronounceable names**
   - ❌ `private DateTime modymdhms;` → hard to read
   - ✅ `private DateTime modificationFullDate;` → easy to read

3. **Avoid encodings and prefixes**
   - ❌ `PhoneNumber phoneString;` → name tied to type
   - ✅ `PhoneNumber phone;` → clear
   - ❌ `private string m_dsc;` → unclear
   - ✅ `private string description;` → clear

4. **Classes and objects should be nouns**
   - ✅ `Customer, Account, User`
   - ❌ `Manage, Calculating, Draw`

5. **Methods should be verbs**
   - ✅ `DeletePage, GetName, Save`
   - ❌ `Customer, Account, User`

> Key idea: Names should be descriptive, readable, and follow Java conventions. Classes represent things → nouns; methods represent actions → verbs.

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

Good Example:

public string RenderPageWithSetupsAndTeardowns(PageData pageData, bool isSuite) {
    if (IsTestPage(pageData))
        IncludeSetupAndTeardownPages(pageData, isSuite);
    return pageData.GetHtml();
}
Blocks within if, else, while should be one line long

Bad Example:

if (id != null) {
    PrintStudent();
} else {
    PrintError();
}

Good Example:

if (id != null)
    PrintStudent();
else
    PrintError();
Functions should do one thing, do it well, and do it only

Bad Example:

public void AddEmployee() {
    var employee = new Employee() { FirstName="Mohamed", LastName="Hassan" };
    SaveUserToDatabase(employee);
    PrintSuccessMessage();
}

Good Example:

public bool AddEmployee() {
    var employee = new Employee() { FirstName="Mohamed", LastName="Hassan" };
    return SaveUserToDatabase(employee);
}
Prefer exceptions over returning error codes

Bad Example:
Nested error checks (pseudo):

if (DeletePage(page) == ErrorCode.Error) { ... }

Good Example:
Use try-catch:

try {
    DeletePage(page);
    registry.DeleteReference(page.name);
    configKeys.DeleteKey(page.name.makeKey());
} catch (Exception e) {
    logger.log(e.GetMessage());
}
Don’t Repeat Yourself (DRY)

Bad Example:

private static void DoSomething() { ... }
private static void DoSomethingAgain() { ... }

Good Example:

private static void PrintEmployee(Employee employee) {
    string format = "{0} is {1}, lives in {2}, age {3}";
    Console.WriteLine(format, employee.FirstName, employee.Relation, employee.Address, employee.Age);
}

7. **Avoid dependency magnets**  
   - Bad: returning error codes (e.g., `enum ErrorCode { Ok, Invalid, Locked, Waiting }`)  
     - Any function using this enum becomes tightly coupled. Adding new codes requires recompiling dependent code.  
   - Good: use exceptions instead  
     - New exception types inherit from a base exception class.  
     - Can be added without forcing recompilation or redeployment.  

> Key idea: Functions should be small, focused, readable, and do only one thing. Keep blocks short, use exceptions for errors, and centralize repeated logic to follow DRY.

## Part 3: Comments

1. **Comments don’t make up for bad code**  
   - Focus on writing clear, self-explanatory code instead of relying on comments.

2. **Explain yourself in code**  
   - Use meaningful names, small functions, and clear logic.

3. **Good comments can be:**  
   - **Legal** → copyright, license notices  
   - **Clarification** → explain tricky logic  
   - **Documentation** → public API descriptions, `<summary>` tags  
   - **Informative** → extra context about usage  
   - **TODO** → temporary notes, to be removed after review

4. **Bad comments examples**  
   - **Journal comments** – long historical logs inside the code
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
Commented-out code – old code left in the file
//private static void PrintEmployee(Employee employee)
//{
//    string format = "{0} is {1}, lives in {2}, age {3}";
//    Console.WriteLine(format,
//        employee.FirstName,
//        employee.Relation,
//        employee.Address,
//        employee.Age);
//}

> Key idea: Avoid cluttering your code with historical logs or commented-out code. Comments should clarify, not replace refactoring or meaningful code.
