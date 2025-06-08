# Chapter 3: Pull request and commit conventions

## Branch Naming:
- Feature branches: `feature/META-123-description`

- Bugfix branches: `bugfix/META-456-description`

- Hotfix branches: `hotfix/META-789-description`

---

## 📝 Commit Conventions
- Use the present tense (“Add feature” not “Added feature”).

- Keep messages concise (max 72 characters recommended).

- Provide additional detail in the commit body (optional) if needed.

- Merge Request Title Format:
  ```text
  <JIRA-ISSUE-ID>: <commit message>

  Example:
  META-123: Implement user authentication with JWT
  ```

---

## Pull request
- Create a pull request with title of the Jira issue

- Include the Jira issue id in the title

- Link the MR to the corresponding JIRA ticket in the description.

```text
  <JIRA-ISSUE-ID>: <JIRA Issue title>

  Example:
  META-123: [MIM] Adding TG macros to ads for active social accounts
  ```

---

## 📌 Next Chapter

- Chapter 4: Design patterns and principles (Dependency Injection, SOLID)