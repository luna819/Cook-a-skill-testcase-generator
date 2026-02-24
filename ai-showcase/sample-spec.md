# Feature Spec: User Registration

> Module: Authentication
> Version: 1.0
> Author: QC Team (sample)

---

## 1. Overview

New users can create an account on the platform by filling in a registration form. After submitting, the system sends a verification email. The account is only activated after the user clicks the verification link.

---

## 2. User Flow

1. User visits the Registration page (`/register`)
2. User fills in the registration form
3. User clicks "Create Account"
4. System validates the input
   - If invalid → show inline error messages, do not submit
   - If valid → create account (inactive), send verification email, redirect to a "Check your email" page
5. User opens their email and clicks the verification link
6. System activates the account and redirects user to the Login page with a success message: "Your account has been verified. Please log in."

---

## 3. Registration Form Fields

| Field | Type | Required | Validation Rules |
|---|---|---|---|
| Full Name | Text | Yes | 2–50 characters; letters and spaces only; no numbers or special characters |
| Email | Email | Yes | Must be a valid email format; must not already be registered in the system |
| Password | Password | Yes | 8–32 characters; must contain at least 1 uppercase letter, 1 lowercase letter, 1 number, and 1 special character |
| Confirm Password | Password | Yes | Must exactly match the Password field |
| Date of Birth | Date picker | Yes | User must be at least 18 years old |
| Phone Number | Text | No | If provided: 10 digits, numbers only, must start with 0 |
| Terms & Conditions | Checkbox | Yes | Must be checked to submit |

---

## 4. Business Rules

- **BR-01:** Each email address can only be registered once. If a user tries to register with an already-registered email, show the error: "This email is already in use. Please log in or use a different email."
- **BR-02:** The verification email link is valid for **24 hours**. After expiry, the link becomes invalid.
- **BR-03:** If the link expires, the user can request a new verification email from the "Check your email" page by clicking "Resend verification email."
- **BR-04:** An unverified account cannot log in. Attempting to log in with an unverified account shows: "Please verify your email before logging in."
- **BR-05:** Passwords are stored hashed. Plain text passwords are never stored or logged.

---

## 5. Verification Email

- **Subject:** "Verify your email address"
- **Sender:** no-reply@platform.com
- **Contains:** A unique verification link (valid 24 hours) and the user's Full Name in the greeting
- **Link format:** `https://platform.com/verify?token=[unique-token]`

---

## 6. UI / UX Notes

- Password field has a show/hide toggle (eye icon)
- Form shows inline error messages immediately on blur (when the user leaves a field)
- The "Create Account" button is disabled until all required fields are filled
- On successful submission, the button shows a loading spinner before redirect

---

## 7. Out of Scope

- Social login (Google, Facebook)
- Email domain blacklist (e.g., blocking disposable emails)
- CAPTCHA
- Admin-created accounts
