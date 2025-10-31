# Known Issues – Admin Panel Sandbox

Environment: local Docker (FE + BE), Postgres/PGAdmin  
Source of checks: TestRail suite (Add User, View Users, Update User, Delete User, Auth/Token, Roles/UI, API/Integration)

This document describes current mismatches between the course specification and the actual behavior of the app.

---

## 1. Add User

### KI-01 – User can be created with empty required field
- **Related TC:** TC-03: Create user with empty required fields
- **Description:** It is possible to save a new user without filling a required field (e.g. `Name`).
- **Expected:** Form should validate required fields and block creation.
- **Actual:** User is created successfully.
- **Impact:** Data quality issue in `USERS` table.

### KI-02 – “Role” is not a dropdown / allows free text
- **Related TC:** TC-14: Roles are available in “Роль” dropdown  
  **Related TC:** TC-16: Add user – role must be selected from predefined list (spec)
- **Description:** The spec says the role must be selected from a predefined list (Security(1), Administrator(2), Guest(3), Editor(4)). In UI this field is a free-text input and accepts any value.
- **Expected:** Dropdown/select with predefined roles; no arbitrary input.
- **Actual:** User can enter any text as role.
- **Impact:** UI does not protect `role_id` from invalid values.

### KI-03 – DB is correct but UI does not enforce role list
- **Related TC:** TC-17: DB stores correct role_id
- **Description:** When correct role is entered, DB stores it properly. The issue is only on the UI side (no validation / no dropdown).

---

## 2. View Users

### KI-04 – “Get user by ID” is missing in UI
- **Related TC:** TC-06: Get user by ID (existing)  
  **Related TC:** TC-07: Get user by ID (not existing)  
  **Related TC:** TC-19: View user by ID
- **Description:** The specification describes a way to retrieve user data by a specific ID. The UI page “Users list” has only the button “Get all users”; there is no control to search by ID.
- **Expected:** Input for user ID + button to get this user.
- **Actual:** Only “Get all users”.
- **Result:** These test cases should be marked **Blocked** (feature is absent in UI).

### KI-05 - UI always displays role = 4 for all users
- **Related TC:** TC-18: Role on UI matches role in DB for EACH user
- **Description:** In DB there are users with different `role_id`, but UI shows the same role value (4) for all rows.
- **Expected:** For every user, UI shows the role from DB.
- **Actual:** UI shows role 4 for all.
- **Impact:** Admin cannot visually distinguish user access levels from the UI.

---

## 3. Update User

### KI-06 – Partial update does not work
- **Related TC:** TC-08: Update user – change only email
- **Description:** When updating only one field (for example, email) while providing correct user ID, no changes are saved.
- **Expected:** Only the filled field should be updated.
- **Actual:** Nothing changes in UI/DB.

### KI-07 – Update without ID is not handled
- **Related TC:** TC-09: Update user – no ID provided
- **Description:** If user tries to update data without specifying **User Id**, the UI does not show an error and does not explain what is wrong.
- **Expected:** Validation / message like “Please specify user ID”.
- **Actual:** “Nothing happens”.
- **Impact:** Hard to understand why update failed.

### KI-08 – UI still shows wrong role after update
- **Related TC:** TC-20: Change role and verify UI still shows wrong value
- **Description:** Even if role is successfully changed in DB (via update), the “Users list” page still displays role 4.
- **Expected:** After update, UI reflects new role.
- **Actual:** UI still shows 4 (same root cause as KI-05).

### KI-09 – Update of non-existing user does not show error
- **Related TC:** TC-21: Update user – non-existing user ID
- **Description:** When updating a user with an ID that doesn’t exist, the UI does not show “user not found” or any error.
- **Expected:** Clear error / info message.
- **Actual:** No message, no update.

---

## 4. Delete User

### KI-10 – Delete from UI does not delete in DB
- **Related TC:** TC-10: Delete user by ID (existing)
- **Description:** Deleting an existing user from the UI does not remove the record from DB.
- **Expected:** After clicking “Delete user” the record is removed from `USERS`.
- **Actual:** Data remains unchanged.
- **Note:** In a separate API test (see TC-22) deletion via API works → problem is on UI side.

---

## 5. Auth / Token / UI

### KI-11 – Token page button text is wrong
- **Related TC:** TC-23: Token page – button label matches specification
- **Description:** The spec says the button should be “Сгенерировать токен”. The actual button text is “Log in”.
- **Expected:** “Сгенерировать токен”.
- **Actual:** “Log in”.

### KI-12 – Token page button color does not match common style
- **Related TC:** TC-24: Token page – button style follows common button style
- **Description:** The style in the spec is primary button (#007bff, white text, rounded). Actual button uses another color `#8400ff`.
- **Expected:** `#007bff`, no border, border-radius 5px, white text.
- **Actual:** Different color / styling.

### KI-13 – “Get all users” button has no style
- **Related TC:** TC-25: “Get all users” button uses primary style
- **Description:** On the “Users list” page the main action button does not use the primary style from the spec.
- **Expected:** Same primary button style as on other pages.
- **Actual:** Default/no style.

---

## 6. API / Integration (context)
- **Related TC:** TC-15: API – GET /users returns actual UI list  
- **Observation:** API returns correct data, DB contains correct roles, but UI displays them incorrectly → most of the issues above are UI-mapping/styling problems, not backend problems.
- **Related TC:** TC-22: Delete user – API (control check)  
  - Delete via API works → confirms KI-10 is UI-only.

---
