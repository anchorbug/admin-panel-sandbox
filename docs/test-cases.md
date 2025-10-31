# Test Cases – Admin Panel Sandbox

Environment: local Docker (FE + BE), Postgres/PGAdmin, Postman  
Source: course spec “Admin panel – user management”  
Structure below fully matches TestRail sections and case numbers.

---

## 1. Add User

### TC-01: Create user with valid data
**Preconditions:**
- “Add user” page is available.

**Steps:**
1. Open “Add user”.
2. Enter valid name.
3. Enter unique valid email.
4. Enter password.
5. Enter/select valid role.
6. Click **Save**.

**Expected result:**
- New user is created.
- User is stored in DB table `USERS`.
- User can be seen on “Users list” page.

---

### TC-02: Create user with duplicate email
**Preconditions:**
- User with email `test@example.com` already exists in DB.

**Steps:**
1. Open “Add user”.
2. Enter any name.
3. Enter email `test@example.com`.
4. Enter password.
5. Enter/select role.
6. Click **Save**.

**Expected result:**
- System does **not** create a user with the same email.
- Error / validation message is shown.

---

### TC-03: Create user with empty required fields
**Preconditions:**
- “Add user” page is available.

**Steps:**
1. Open “Add user”.
2. Leave one of the required fields empty (e.g. **Name**).
3. Fill other fields.
4. Click **Save**.

**Expected result:**
- User is **not** created.
- Form shows validation / error about required field.

---

### TC-04: Create user with Cyrillic name
**Preconditions:**
- “Add user” page is available.

**Steps:**
1. Open “Add user”.
2. In **Name** enter `Алексей Тест`.
3. Fill valid email.
4. Fill password.
5. Enter/select role.
6. Click **Save**.

**Expected result:**
- User is created.
- Cyrillic name is stored in DB and displayed correctly in UI.

---

### TC-16: Add user – role must be selected from predefined list (spec)
**Preconditions:**
- Spec says: role is chosen from a predefined list (Security(1), Administrator(2), Guest(3), Editor(4)).

**Steps:**
1. Open “Add user”.
2. Go to **Role** field.
3. Try to enter custom/nonexistent value.
4. Click **Save**.

**Expected result:**
- User cannot be created with a role that is not in the predefined list.
- Ideally, the field is a dropdown, not a free-text input.

---

### TC-17: DB stores correct role_id
**Preconditions:**
- Access to PGAdmin / DB.

**Steps:**
1. Create a user on UI with a specific role (e.g. Administrator = 2).
2. In PGAdmin open table `USERS`.
3. Find the created user.

**Expected result:**
- Field `role_id` in DB equals the role chosen on UI.

---

## 2. View Users

### TC-05: Get all users list
**Preconditions:**
- At least 1 user exists in DB.

**Steps:**
1. Open “Users list”.
2. Click **Получить список всех пользователей**.

**Expected result:**
- UI displays the list of all users returned by backend.

---

### TC-06: Get user by ID (existing)
**Preconditions:**
- User with ID = X exists in DB.

**Steps:**
1. Open “Users list”.
2. Enter ID = X in the “search by ID” control.
3. Click button to get user by ID.

**Expected result:**
- UI displays data of user with ID = X only.

---

### TC-07: Get user by ID (not existing)
**Preconditions:**
- User with ID = 9999 does **not** exist.

**Steps:**
1. Open “Users list”.
2. Enter ID = 9999.
3. Click button to get user by ID.

**Expected result:**
- System shows message like “user not found” / empty result.
- No error on UI.

---

### TC-18: Role on UI matches role in DB for EACH user
**Preconditions:**
- In DB there are several users with different `role_id` values.

**Steps:**
1. Open “Users list”.
2. Click **Get all users**.
3. In DB run `SELECT id, role_id FROM users;`
4. Compare roles for each user in UI and in DB.

**Expected result:**
- For every user the role shown in UI equals the `role_id` stored in DB.
- Different users may have different roles on UI.

---

### TC-19: View user by ID
**Preconditions:**
- UI supports getting user by ID (per spec).

**Steps:**
1. Open “Users list”.
2. Enter user ID.
3. Click to fetch user.

**Expected result:**
- UI shows data of that user only.

---

## 3. Update User

### TC-08: Update user – change only email
**Preconditions:**
- User with ID = X exists.

**Steps:**
1. Open “Update user”.
2. Enter **User Id** = X.
3. Leave name empty.
4. Enter new email.
5. Leave other fields empty.
6. Click **Save**.

**Expected result:**
- Only email of user X is updated.
- Other fields stay unchanged.

---

### TC-09: Update user – no ID provided
**Preconditions:**
- Update page is available.

**Steps:**
1. Open “Update user”.
2. Leave **User Id** empty.
3. Fill any other field.
4. Click **Save**.

**Expected result:**
- System does **not** update anything.
- Validation/error “Please specify user ID” is shown.

---

### TC-20: Change role and verify UI still shows wrong value
**Preconditions:**
- User with ID = X exists.

**Steps:**
1. Open “Update user”.
2. Enter **User Id** = X.
3. Change **Role** to any other valid role.
4. Click **Save**.
5. Open “Users list”.
6. Get all users.

**Expected result:**
- In DB user X has updated role.
- In UI user X is displayed with the same (updated) role.

---

### TC-21: Update user – non-existing user ID
**Preconditions:**
- User with ID = 9999 does not exist.

**Steps:**
1. Open “Update user”.
2. Enter **User Id** = 9999.
3. Fill any field.
4. Click **Save**.

**Expected result:**
- System shows “user not found” (or similar).
- No data in DB is changed.

---

## 4. Delete User

### TC-10: Delete user by ID (existing)
**Preconditions:**
- User with ID = X exists.

**Steps:**
1. Open “Delete user”.
2. Enter ID = X.
3. Click **Delete user**.
4. Check users list / DB.

**Expected result:**
- User X is deleted from DB.
- User X is not shown in the list.

---

### TC-11: Delete user by ID (not existing)
**Preconditions:**
- User with ID = 9999 does not exist.

**Steps:**
1. Open “Delete user”.
2. Enter ID = 9999.
3. Click **Delete user**.

**Expected result:**
- System shows message that user does not exist / nothing to delete.
- No changes in DB.

---

## 5. Auth / Token

### TC-12: Generate token for valid user
**Preconditions:**
- User exists with known email/name and password.

**Steps:**
1. Open “Get token” page.
2. Enter valid email/name.
3. Enter correct password.
4. Click **Generate token**.

**Expected result:**
- Token is generated and displayed.
- Token is saved in DB (`TOKENS`) with `user_id` and `expire_at`.

---

### TC-13: Generate token with wrong password
**Preconditions:**
- User exists.

**Steps:**
1. Open “Get token”.
2. Enter valid email/name.
3. Enter **wrong** password.
4. Click **Generate token**.

**Expected result:**
- Token is **not** generated.
- Error / “invalid credentials” message is shown.
- No new record in `TOKENS`.

---

## 6. Roles / UI

### TC-14: Roles are available in “Роль” dropdown
**Steps:**
1. Open “Add user”.
2. Click on the **Role** field.

**Expected result:**
- Field is a **dropdown**.
- It contains roles from spec:
  - Security / Служба безопасности (1)
  - Administrator (2)
  - Guest (3)
  - Editor (4)

---

### TC-23: Token page – button label matches specification
**Steps:**
1. Open “Get token” page.
2. Check the button text.

**Expected result:**
- Button text: **“Сгенерировать токен”** (as in spec).

---

### TC-24: Token page – button style follows common button style
**Steps:**
1. Open “Get token”.
2. Inspect the main button.

**Expected result:**
- Style matches shared style from spec:
  - background-color: `#007bff`
  - border: none
  - border-radius: 5px
  - text color: `#fff`

---

### TC-25: “Get all users” button uses primary style
**Steps:**
1. Open “Users list”.
2. Look at the button **“Получить список всех пользователей”**.

**Expected result:**
- Button uses the same primary style as other buttons in the app.

---

## 7. API / Integration

### TC-15: API – GET /users returns actual UI list
**Steps:**
1. In UI open “Users list” and click **Get all users**.
2. In Postman call `GET /users`.
3. Compare the number of records and main fields.

**Expected result:**
- Data returned by API matches the data shown in UI.

---

### TC-22: Delete user – API (control check)
**Preconditions:**
- User with ID = X exists.

**Steps:**
1. In Postman call `DELETE /deleteUser` with ID = X.
2. Check response.
3. Check DB.

**Expected result:**
- API request deletes user X from DB.
- User no longer appears in the UI list after refresh.
