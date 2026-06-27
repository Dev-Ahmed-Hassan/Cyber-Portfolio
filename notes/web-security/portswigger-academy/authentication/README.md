# Authentication

TL;DR notes from PortSwigger Web Security Academy.

Source: https://portswigger.net/web-security/authentication

---

## Core Idea

Authentication verifies **who the user/client is**.

Robust authentication matters because web apps are exposed to anyone on the internet.

---

## Authentication Factors

```text
Something you know  -> password / security answer
Something you have  -> phone / security token
Something you are   -> biometrics / behavior
```

---

## Authentication vs Authorization

```text
Authentication = Are you really this user?
Authorization  = Are you allowed to do this action?
```

Example:

```text
Login proves identity.
Permissions decide allowed actions.
```

---

## How Auth Vulnerabilities Usually Happen

Most authentication vulnerabilities come from:

```text
1. Weak protection against brute-force attacks
2. Logic flaws that allow auth bypass
```

PortSwigger also refers to this kind of flawed logic as broken authentication.

---

## Impact

If authentication fails, attackers may be able to:

- access another user's account
- access sensitive data
- use account functionality as the victim
- compromise high-privileged accounts
- reach internal / authenticated-only attack surface

High-privileged account compromise can affect the whole application.

---

## Main Areas in This Topic

```text
password-based login
multi-factor authentication
other auth mechanisms
OAuth authentication
securing auth mechanisms
```

---

## Quick Mental Checklist

```text
Can login be bypassed?
Can credentials be guessed?
Can usernames be enumerated?
Can MFA be skipped?
Can password reset be abused?
Can sessions or tokens be reused?
```

---

## My Reminder

Authentication bugs are usually high-impact because they directly affect identity, access, and account control.
