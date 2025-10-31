# Test Cases – Admin Panel Sandbox

This file contains test cases based on the course specification and actual behavior of the sandbox app.  
Environment: local Docker (FE + BE), Postgres, PGAdmin, Postman.

---

## 01. Add User

### TC-01 – Add user (happy path)
**Preconditions:** Add User page is available.  
**Steps:**
1. Open “Add user”.
2. Enter valid name.
3. Enter valid email.
4. Enter password.
5. Select / enter a valid role.
6. Click “Save”.
**Expected result:**  
- New user is created.  
- User appears in DB table `USERS`.  

---

### TC-02 – Add user with duplicate email
**Preconditions:** User with email `test.user@example.com` exists in `USERS`.  
**Steps:**
1. Open “Add user”.
2. Enter any name.
3. Enter email `test.user@example.com`.
4. Enter password.
5. Choose role.
6. Click “Save”.
**Expected result:**  
- User is **not** created.  
- Error is shown: “Current user already exists in the system. Contact administrator!” (or similar).  

---

### TC-03 – Add user with empty required fields
**Preconditions:** Add User page is available.  
**Steps:**
1. Open “Add user”.
2. Leave one of the required fields empty (name / email / password).
3. Click “Save”.
**Expected result:**  
- User is **not** created.  
- Validation / error is shown.  
- No new record in `USERS`.  

---

### TC-04 – Add user with Cyrillic name
**Preconditions:** Add User page is available.  
**Steps:**
1. Open “Add user”.
2. In “Name” enter `Алексей Тест`.
3. Fill valid email, password, role.
4. Click “Save”.
**Expected result:**  
- User is created.  
- Cyrillic name is stored and displayed correctly.  

---

### TC-14 – Roles are available in “Role” dropdown (per spec)
**Preconditions:** Spec says: role must be selected from a dropdown.  
**Steps:**
1. Open “Add user”.
2. Focus / open the “Role” field.
**Expected result:**  
- Role is a **dropdown**.  
- It contains roles from the spec:
  - Security (1)
  - Administrator (2)
  - Guest (3)
  - Editor (4)
**Actual (current app):** field is a free-text input → **Failed**.

---

### TC-16 – Add user – role must be selected from predefined list
**Preconditions:** Add User page is available.  
**Steps:**
1. Open “Add user”.
2. In “Role” try to type random text: `superrole123`.
3. Click “Save”.
**Expected result:**  
- User cannot be created with a role that is not in the predefined list.  
- Ideally, the field should not allow arbitrary text.  
**Actual:** field allows any text → **Failed**.

---

### TC-17 – Add user – DB stores correct `role_id`
**Preconditions:** PGAdmin access is available.  
**Steps:**
1. Create a user on UI with role = 2 (Administrator).
2. Open PGAdmin → table `USERS`.
3. Find this user.
**Expected result:**  
- `role_id` for this user in DB equals `2`.  

---

## 02. View Users

### TC-05 – View all users
**Preconditions:** At least 1 user exists in DB.  
**Steps:**
1. Open “Users list”.
2. Click “Получить список всех пользователей”.
**Expected result:**  
- UI shows list of all users returned by backend `/users`.  

---

### TC-18 – View users – newly created user is displayed
**Preconditions:** A user was created in the same environment.  
**Steps:**
1. Open “Users list”.
2. Click “Get all users”.
**Expected result:**  
- The recently created user is present in the list.  

---

### TC-19 – View users – role on UI matches role in DB
**Preconditions:**  
- In DB there are several users with different `role_id` values (e.g. 2 and 3).  
**Steps:**
1. Open “Users list”.
2. Click “Get all users”.
3. In PGAdmin run: `SELECT id, role_id FROM users;`
4. Compare roles on UI with roles from DB.
**Expected result:**  
- For each user, the role shown in UI equals the `role_id` in DB.  
- Different users can have different roles on UI.

---

### TC-20 – View user by ID – control is missing
**Preconditions:** Spec: “Admin can get data by user ID.”  
**Steps:**
1. Open “Users list”.
2. Try to find input for user ID and a button to fetch this user.
**Expected result:**  
- There is an input to enter user ID.  
- There is a button “Get data by user”.  

---

## 03. Update User

### TC-08 – Update user – change only email
**Preconditions:** User with id = X exists.  
**Steps:**
1. Open “Update user”.
2. Enter user ID = X.
3. Leave name empty.
4. Enter new valid email.
5. Leave password empty.
6. Click “Save”.
**Expected result:**  
- Only email for user X is updated.  
- Other fields stay unchanged.

---

### TC-09 – Update user – no ID provided
**Preconditions:** Update page is available.  
**Steps:**
1. Open “Update user”.
2. Leave “User Id” empty.
3. Fill any other field.
4. Click “Save”.
**Expected result:**  
- Update is not performed.  
- Error like “Specify user ID” is shown.  

---

### TC-21 – Update user – change role and verify UI
**Preconditions:**  
- User with id = X exists.  
- You know his current `role_id`.  
**Steps:**
1. Open “Update user”.
2. Enter user ID = X.
3. Change role to another valid value (e.g. 2 → 3).
4. Click “Save”.
5. Check DB `USERS` for user X.
6. Open “Users list” and get all users.
**Expected result:**  
- In DB, user X has **new** `role_id`.  
- On UI, user X is shown with the same (new) role.

---

### TC-22 – Update user – non-existing user ID
**Preconditions:** Update page is available.  
**Steps:**
1. Open “Update user”.
2. Enter user ID = 9999 (non-existing).
3. Fill any field.
4. Click “Save”.
**Expected result:**  
- System shows “user not found” / error.  
- No data in DB is changed.

---

## 04. Delete User

### TC-10 – Delete user by existing ID (UI)
**Preconditions:** User with id = X exists.  
**Steps:**
1. Open “Delete user”.
2. Enter user ID = X.
3. Click “Delete user”.
4. Check DB / users list.
**Expected result:**  
- User X is deleted from DB.  
- User X is not shown in UI.

---

### TC-11 – Delete user by non-existing ID (UI)
**Preconditions:** User with id = 9999 does not exist.  
**Steps:**
1. Open “Delete user”.
2. Enter `9999`.
3. Click “Delete user”.
**Expected result:**  
- System shows info/error that user does not exist.  
- No changes in DB.

---

### TC-23 – Delete user – empty ID
**Preconditions:** Delete page is available.  
**Steps:**
1. Open “Delete user”.
2. Leave ID field empty.
3. Click “Delete user”.
**Expected result:**  
- System blocks deletion / shows validation.  
- No request is sent.  

---

### TC-24 – Delete user – UI
**Preconditions:** User with id = X exists.  
**Steps:**
1. Open “Delete user”.
2. Enter user ID = X.
3. Click “Delete user”.
4. Check DB or refresh users list.
**Expected result:**  
- UI sends delete request.  
- User X is removed from DB. 

---

## 05. Auth / Token

### TC-12 – Generate token for valid user
**Preconditions:** User with known name/email and password exists.  
**Steps:**
1. Open “Get token” page.
2. Enter valid name/email.
3. Enter valid password.
4. Click “Generate token”.
**Expected result:**  
- Token is returned in response / shown on UI.  
- New record is stored in `TOKENS` with this user_id and `expire_at` (+30 min).

---

### TC-13 – Generate token with wrong password
**Preconditions:** User exists.  
**Steps:**
1. Open “Get token” page.
2. Enter valid name/email.
3. Enter **wrong** password.
4. Click “Generate token”.
**Expected result:**  
- Token is **not** generated.  
- Error is shown.  
- No new record in `TOKENS`.  

---

### TC-25 – Generate token – empty fields
**Preconditions:** Token page is available.  
**Steps:**
1. Leave name/email empty.
2. Leave password empty.
3. Click “Generate token”.
**Expected result:**  
- System does not try to generate token.  
- Validation/error is shown.  

---

### TC-26 – Generate token – token saved with `expire_at`
**Preconditions:** Valid user exists.  
**Steps:**
1. Generate token for this user.
2. In PGAdmin open table `TOKENS`.
3. Find last inserted row.
**Expected result:**  
- `user_id` = this user’s id.  
- `token` is not null.  
- `expire_at` is set and is ~30 minutes ahead.  

---

## 06. UI & Styling

### TC-27 – Navigation – all admin pages are available
**Steps:**
1. Open app.
2. Click through: Home, Add user, Users list, Update user, Delete user, Get token.
**Expected result:**  
- Each link opens the correct page.  
- No 404 / blank page.

---

### TC-28 – Footer is displayed on all pages
**Steps:**
1. Open any page.
2. Scroll to the bottom.
**Expected result:**  
- Footer text is visible.  
- Style is consistent on all pages.

---

### TC-30 – Token page – button label matches spec
**Preconditions:** Spec says: button text should be “Сгенерировать токен”.  
**Steps:**
1. Open token page.
2. Look at the main action button.
**Expected result:**  
- Button text = “Сгенерировать токен”.

---

### TC-31 – Token page – button style follows common style
**Preconditions:** Button style in spec: background `#007bff`, white text, rounded.  
**Steps:**
1. Open token page.
2. Inspect button style.
**Expected result:**  
- Button matches primary style from spec.  

---

### TC-32 – Users list page – “Get all users” button uses primary style
**Preconditions:** Users list page is available.  
**Steps:**
1. Open “Users list”.
2. Look at the button “Получить список всех пользователей”.
**Expected result:**  
- Button uses the same primary style as other buttons (`#007bff`, white text, rounded).  

---

## 07. API / Integration

### TC-15 – API – GET `/users` returns same data as UI
**Steps:**
1. In UI get all users.
2. In Postman call `GET /users`.
3. Compare number of records and main fields.
**Expected result:**  
- UI and API return the same set of users.

---

### TC-29 – API – UI delete does not send proper request
**Preconditions:** Browser DevTools is available.  
**Steps:**
1. Open “Delete user”.
2. Open DevTools → Network.
3. Enter existing user ID.
4. Click “Delete user”.
5. Check if DELETE request was sent.
**Expected result:**  
- UI sends DELETE (or specified) request to backend with user ID.  

---

### TC-33 – API – Delete user via Postman
**Preconditions:** User with id = X exists.  
**Steps:**
1. In Postman send `DELETE /deleteUser` with userId = X.
2. Check response.
3. Check DB.
**Expected result:**  
- API deletes user X.  
- User is no longer in `USERS`.  
