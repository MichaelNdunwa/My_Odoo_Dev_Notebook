[← Back to Table of Contents](../README.md#table-of-contents)

# 💬 Odoo commit message style

## 🔹 General Structure

Odoo commits generally follow this pattern:

```
[TAG] <module|scope>: <short description>
```

Sometimes the scope (`<module|scope>`) is omitted if the commit affects many modules or is a general change.

---

## 🔹 Common Tags

Here are the **prefix tags** you’ll often see in Odoo commits:

* **\[FIX]** → A bug fix.
  Example:

  ```
  [FIX] packaging: more explicit debian control file
  ```

  Meaning: a bug was fixed in the *packaging* scope.

* **\[IMP]** → An improvement (not a bug, but an enhancement).
  Example:

  ```
  [IMP] github: adapt issue template to 18 release
  ```

  Meaning: improved the GitHub issue template for version 18.

* **\[ADD]** → Adding a new feature, file, or functionality.

* **\[REM]** → Removing obsolete code or functionality.

* **\[REF]** → Refactor (internal code change, no feature or bug).

* **\[I18N]** → Internationalization / translations.
  Example:

  ```
  [I18N] *: fetch translation updates
  ```

  Meaning: update translations across all modules (`*`).

* **\[CLA]** → Contributor License Agreement updates.
  Example:

  ```
  [CLA] Add carlos.lopez to Tecnativa
  ```

  Meaning: Carlos Lopez was added under Tecnativa’s CLA record.

* **\[LEGAL]** → Legal-related changes (licenses, terms, etc.).
  Example:

  ```
  [LEGAL] Switch to LGPLv3 License
  ```

* **\[REV]** → Revert a previous commit.

* **\[TEST]** → Test-related commits.

* **\[MERGE]** → Merge branch commits.

---

## 🔹 Scope

After the tag, you often see a **scope** (module, tool, or area of the repo):

* `packaging`
* `github`
* `base`
* `website_sale`
* `*` (all modules)

---

## 🔹 Description

The short description is:

* Written in lowercase (except proper nouns)
* Concise (typically under 72 chars)
* States what the commit *does* (not why)

---

✅ **Example in practice**:

```
[FIX] website_sale: prevent crash on checkout without address
```

* `[FIX]` → Bug fix
* `website_sale` → Module affected
* `prevent crash on checkout without address` → Short explanation

---

👉🏽 So Odoo’s style is **consistent, structured, and categorized** for easy tracking. It makes browsing history or using `git log` much easier, especially across thousands of modules.

---


# 📝 Odoo Commit Message Cheat Sheet
>a **cheat sheet template** you can use to follow Odoo’s commit message style when writing commits for your own modules or when contributing upstream:

### **Format**

```
[TAG] <module|scope>: <short description>
```

### **Common Tags**

| Tag       | Meaning                                                     |
| --------- | ----------------------------------------------------------- |
| `[FIX]`   | Bug fix (crash, wrong logic, typo causing issues, etc.)     |
| `[IMP]`   | Improvement (better UX, performance, maintainability, etc.) |
| `[ADD]`   | Add new feature, module, file, or functionality             |
| `[REM]`   | Remove obsolete/unnecessary code or files                   |
| `[REF]`   | Refactor code (internal change, no functional impact)       |
| `[I18N]`  | Internationalization / translations                         |
| `[CLA]`   | Contributor License Agreement update                        |
| `[LEGAL]` | Legal/license-related changes                               |
| `[REV]`   | Revert a previous commit                                    |
| `[TEST]`  | Tests only (unit, functional, performance)                  |
| `[MERGE]` | Merge branch commits                                        |

---

### **Guidelines**

1. **Scope (module/area)**:

   * Always specify which module/part is affected: `account`, `sale`, `website_sale`, `packaging`, `github`, etc.
   * Use `*` when affecting multiple/all modules.

2. **Description**:

   * Be concise (max \~72 chars).
   * Describe what the commit *does*, not why.
   * Use the imperative mood (e.g., “add…”, “fix…”, “prevent…”).

3. **Consistency**:

   * Keep lowercase in the description (except proper nouns).
   * Don’t end with a period.

---

### **Examples**

* Fixes:

  ```
  [FIX] account: prevent division by zero in tax report
  [FIX] packaging: more explicit debian control file
  ```

* Improvements:

  ```
  [IMP] website_sale: improve search speed on product listing
  [IMP] github: adapt issue template to 18 release
  ```

* Additions:

  ```
  [ADD] crm: add lead scoring feature
  [ADD] hr: initial import of attendance module
  ```

* Refactors:

  ```
  [REF] stock: simplify picking confirmation logic
  ```

* Internationalization:

  ```
  [I18N] *: fetch translation updates
  ```

* CLA:

  ```
  [CLA] Add michael.ndunwa to the_organixer
  ```

---



[Previous: 🐘 Installing pgAdmin 4 on WSL/Debian](./09-pgadmin4-install.md)

[Next: -]()
