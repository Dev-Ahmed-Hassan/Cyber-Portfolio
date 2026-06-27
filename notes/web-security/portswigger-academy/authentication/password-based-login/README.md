# Vulnerabilities in Password-Based Login

TL;DR notes from PortSwigger Web Security Academy.

Source: https://portswigger.net/web-security/authentication/password-based

---

## Core Idea

Password-based login relies on:

```text
username + secret password = proof of identity
```

If an attacker obtains or guesses valid credentials, the account is compromised.

---

## Main Risk

Password-only authentication is weak when the app does not properly defend against:

```text
brute-force attacks
credential guessing
username enumeration
flawed rate limits
weak account lockout logic
HTTP basic auth issues
```

---

## Brute-Force Attacks

Brute force means guessing credentials through trial and error.

It is often automated with username/password wordlists.

Important point:

```text
Brute force is not always random.
Attackers use patterns, logic, public info, and human password habits.
```

---

## Brute-Forcing Usernames

Look for predictable usernames:

```text
admin
administrator
firstname.lastname@company.com
emails exposed in responses
names from public profiles
support / IT accounts
```

Public profile names or leaked email addresses can help build username lists.

---

## Brute-Forcing Passwords

Password policies do not always stop bad passwords.

Users often modify memorable passwords to satisfy rules:

```text
password      -> Password1!
mypassword    -> Mypassword1!
Mypassword1!  -> Mypassword2!
```

Human patterns make guessing more effective than pure random brute force.

---

## Username Enumeration

Username enumeration happens when app behavior reveals whether a username is valid.

Check differences in:

```text
status code
error message
response length
response timing
account lock behavior
registration messages
```

Common pattern:

```text
Invalid username
```

vs

```text
Incorrect password
```

Even tiny differences can matter.

---

## Response Timing Clue

If the app only checks the password after confirming the username exists:

```text
valid username   -> slower response
invalid username -> faster response
```

A long password can make timing differences easier to notice.

---

## Flawed Brute-Force Protection

Common defenses:

```text
account locking
IP-based rate limiting
```

But flawed logic can weaken them.

---

## Account Locking Issues

Account locking can still leak valid usernames if lock messages differ.

It may also fail against broad attacks where the attacker tries:

```text
many usernames + a few common passwords
```

Credential stuffing can bypass account locking because each username may only be tried once.

---

## IP Rate Limiting Issues

IP-based limits may be bypassed if the app trusts attacker-controlled IP-related headers or allows multiple guesses in one request.

Common idea:

```text
If the rate limit tracks the wrong thing, it can often be worked around.
```

---

## HTTP Basic Authentication

HTTP basic auth sends:

```http
Authorization: Basic base64(username:password)
```

Key issues:

- credentials are sent repeatedly with requests
- weak/no brute-force protection is common
- static token value
- no built-in CSRF protection
- exposed credentials may be reused elsewhere

---

## Quick Testing Checklist

```text
Compare invalid vs valid username behavior
Check status codes and response lengths
Check error message differences
Check response timing
Test account lockout behavior
Test rate limiting logic
Look for predictable usernames
Look for exposed emails/usernames
Watch for HTTP Basic Authorization headers
```

---

## My Reminder

For password-based login, focus on information leaks, guessing resistance, and flawed protection logic.
