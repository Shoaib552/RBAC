# Role-Based Access Control (RBAC)

## What is RBAC?

Role-Based Access Control, or RBAC, is a simple way of deciding **who can do what** in a system.

Instead of giving every person their own list of permissions, we do it in two steps:

1. We create **roles** (for example, "Manager" or "Viewer") and decide what each role is allowed to do.
2. We give each person one or more roles.

That's it. A person can do something only because their role allows it.

Think of it like a job title. Your job title decides which rooms you can enter, which tools you can use, and which decisions you can make. When you change jobs, your access changes with it, and nobody has to redo your permissions one by one.

---

## The Four Building Blocks

| Term | Plain meaning | Example |
|------|---------------|---------|
| **User** | A person (or service) using the system | Priya |
| **Role** | A job title that groups permissions | Doctor |
| **Permission** | One specific thing that can be done | "View patient record" |
| **Resource** | The thing being protected | Patient records, bills, medicine stock |

The relationship looks like this:

```
User  ──has──>  Role  ──contains──>  Permissions  ──apply to──>  Resources
```

---

## A Real-World Example: A Hospital

Imagine a hospital with a computer system that stores patient records, bills, and medicine stock.

Not everyone should see or change everything. A receptionist has no business reading a patient's medical history. A doctor does not need to edit billing amounts. RBAC solves this cleanly.

### Step 1: Define the roles and what each can do

| Role | What they can do |
|------|------------------|
| **Doctor** | View and update patient medical records, write prescriptions |
| **Nurse** | View patient records, update vitals (temperature, blood pressure) |
| **Receptionist** | Register patients, book appointments. Cannot see medical history |
| **Billing Staff** | View and edit bills and payments. Cannot see medical details |
| **Pharmacist** | View prescriptions, update medicine stock |
| **Hospital Admin** | Add or remove staff, assign roles, view reports |

### Step 2: Assign roles to people

| Person | Role |
|--------|------|
| Dr. Mehta | Doctor |
| Anita | Nurse |
| Rahul | Receptionist |
| Sunita | Billing Staff |
| Vikram | Hospital Admin |

### Step 3: The system checks the role, not the person

When Rahul (Receptionist) tries to open a patient's medical history, the system asks:

> "Does the Receptionist role have permission to view medical history?"

The answer is **No**, so access is denied.

When Dr. Mehta opens the same record, the system asks the same question for the Doctor role. The answer is **Yes**, so access is granted.

### What happens when things change?

- **A new nurse joins:** Give them the Nurse role. Done. No need to set up ten separate permissions.
- **Anita is promoted to Head Nurse:** Change her role. Her old access goes away and her new access appears.
- **Rahul leaves the hospital:** Remove his role (or disable his account). All of his access disappears at once.
- **Hospital decides nurses can now also view lab results:** Add that one permission to the Nurse role. Every nurse gets it immediately.

This is the real strength of RBAC: you manage a handful of roles instead of hundreds of individual people.

---

## Why Use RBAC?

- **Security:** People only get the access they need for their job. If an account is compromised, the damage is limited.
- **Easy to manage:** Change a role once and everyone in that role is updated.
- **Easy onboarding and offboarding:** Add or remove a role instead of editing many settings.
- **Clear and auditable:** You can answer "who can do what?" simply by looking at the roles.
- **Fewer mistakes:** No one is accidentally given too much access, or forgotten when they leave.

---

## Key Principles

**1. Least Privilege**
Give each role the minimum access needed to do the job, nothing more. If a receptionist does not need medical records, they do not get them.

**2. Deny by Default**
If a role has not been explicitly given a permission, the answer is "no".

**3. Separation of Duties**
Sensitive actions should not sit with one role alone. For example, the person who creates a payment should not be the same person who approves it.

**4. Roles Describe Jobs, Not People**
Create roles around what people do ("Billing Staff"), not who they are ("Sunita's access"). Roles should stay stable even when staff change.

---

## How It Works in an Application

A typical flow when someone uses an app protected by RBAC:

1. The user **logs in** (authentication: "who are you?").
2. The system looks up the user's **role(s)**.
3. The user tries an action, such as "edit bill".
4. The system checks whether any of the user's roles has that **permission** (authorization: "are you allowed?").
5. If yes, the action goes ahead. If no, the user sees an "Access denied" message.

Authentication proves who you are. RBAC decides what you are allowed to do afterward. They are two different steps.

### Simple pseudo-example

```text
roles = {
  "doctor":       ["view_record", "edit_record", "write_prescription"],
  "nurse":        ["view_record", "update_vitals"],
  "receptionist": ["register_patient", "book_appointment"],
  "billing":      ["view_bill", "edit_bill"],
  "admin":        ["manage_users", "assign_roles", "view_reports"]
}

users = {
  "dr_mehta": "doctor",
  "rahul":    "receptionist"
}

can(user, action):
    role = users[user]
    return action in roles[role]

can("dr_mehta", "edit_record")   -> true
can("rahul",    "view_record")   -> false
```

---

## Good Practices

- Start with a small number of clear roles and add more only when needed.
- Avoid the temptation to create one special role per person. That defeats the purpose.
- Review who holds which roles on a regular schedule and remove access that is no longer needed.
- Keep the Admin role to as few people as possible.
- Log important actions, such as role changes and access denials, so you can audit them later.
- Always enforce permissions on the **server side**. Hiding a button in the screen is not security.

---

## Common Mistakes to Avoid

- **Giving everyone Admin** because it is easier. This removes all protection.
- **Too many roles.** If you have more roles than people, the design needs rethinking.
- **Checking only in the UI.** A hidden button can still be reached by a direct request if the server does not check the role.
- **Never cleaning up.** People change teams, but their old roles stay on, a problem often called "permission creep".

---

## Quick Summary

> Don't give permissions to people. Give permissions to **roles**, and give roles to **people**.

Like a job title in a company, a role tells the system what someone is trusted to do. Change the role and the access follows automatically.
