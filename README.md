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

Key idea: Functions should be small, focused, readable, and do only one thing. Keep blocks short, use exceptions for errors, and centralize repeated logic to follow DRY.
