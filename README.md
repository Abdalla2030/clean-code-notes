# clean-code-notes

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
