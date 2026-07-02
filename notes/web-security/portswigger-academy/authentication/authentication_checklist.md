# How to Secure Your Site's Authentication — Quick Checklist

> **Purpose:** Quick revision sheet for authentication security.  
> **Use this when:** You want to quickly remember what to check before, during, or after testing an authentication mechanism.

---

## 0. Main Idea

Authentication is sensitive because one small mistake can lead to account takeover.

Do not only think about the login page. Check the whole authentication flow:

- login
- registration
- logout
- password reset
- password change
- remember-me / stay-logged-in
- MFA / 2FA
- session handling
- API login endpoints

A site can have a strong login form but still be vulnerable because another authentication feature is weak.

---

## 1. Protect User Credentials

Check that credentials are not leaked, reflected, logged, or sent insecurely.

- [ ] Credentials are never sent over plain HTTP.
- [ ] HTTPS is enforced everywhere.
- [ ] HSTS is enabled so users cannot be downgraded to HTTP.
- [ ] Passwords are not sent in URLs or query parameters.
- [ ] Credentials are not reflected in responses.
- [ ] Credentials are not stored in logs, analytics, browser history, or Referer headers.
- [ ] Sensitive cookies use `Secure`, `HttpOnly`, and `SameSite` where appropriate.

**Bad idea:**

```text
/login?username=ahmed&password=Password123
```

**Better:**

```text
POST /login
username=ahmed&password=Password123
```

---

## 2. Do Not Count on Users for Security

Users will often choose weak or reused passwords, so the application should enforce better behavior.

- [ ] Use a password strength checker.
- [ ] Block very common passwords.
- [ ] Block known breached passwords if possible.
- [ ] Allow long passwords and passphrases.
- [ ] Do not rely only on rules like uppercase + lowercase + number + symbol.
- [ ] Encourage or enforce MFA for sensitive accounts.

Weak but policy-compliant passwords can still exist:

```text
Password1!
Summer2026!
Ahmed123@
```

The point is not only whether the password matches a pattern. The real question is whether it is guessable.

---

## 3. Prevent Username Enumeration

Username enumeration happens when the application reveals whether an account exists.

Check for differences in:

- [ ] error messages
- [ ] HTTP status codes
- [ ] response length
- [ ] response timing
- [ ] redirects
- [ ] JSON structure
- [ ] account lockout messages
- [ ] password reset responses
- [ ] registration responses

**Bad:**

```text
Invalid username
Incorrect password
```

**Better:**

```text
Invalid username or password
```

For password reset, avoid this:

```text
No account found with this email.
```

Better:

```text
If an account exists for this email, a reset link will be sent.
```

---

## 4. Robust Brute-Force Protection

The goal is to make automated guessing difficult and painful.

- [ ] Rate limit by IP address.
- [ ] Rate limit by account/username.
- [ ] Add delays after repeated failures.
- [ ] Use CAPTCHA after a threshold, not as the only defense.
- [ ] Detect password spraying.
- [ ] Detect credential stuffing.
- [ ] Log and alert on repeated failed attempts.
- [ ] Do not let attackers lock out real users too easily.

IP-based blocking alone is not enough if the app trusts spoofable headers like:

```http
X-Forwarded-For: 127.0.0.1
```

Only trust forwarding headers from trusted proxies/load balancers, not directly from user requests.

---

## 5. Triple-Check Verification Logic

Authentication checks should happen on the server and should be checked at every important step.

Ask:

- [ ] Is the server tracking which user is logging in?
- [ ] Is the server checking that the password step was completed?
- [ ] Is the server checking that MFA was completed?
- [ ] Can I skip the second step and directly visit `/my-account`?
- [ ] Can I change a cookie like `verify=carlos` or `user=carlos`?
- [ ] Can I change hidden fields or POST parameters?
- [ ] Does the final POST request re-check the token or only the first GET request?

Important idea:

> Never trust the browser to tell the server which user has been authenticated.

Bad logic example:

```text
Cookie: verify=carlos
```

If the server trusts this blindly, the attacker may be able to attack another user's MFA flow.

---

## 6. Secure Supplementary Functions

Supplementary functions are often weaker than the main login form.

Check:

- [ ] password reset
- [ ] password change
- [ ] email change
- [ ] remember-me
- [ ] account recovery
- [ ] MFA reset
- [ ] invite links
- [ ] magic links

The login form might be secure, but account takeover can still happen through these features.

---

## 7. Password Reset Security

Password reset is an alternative authentication flow, so it must be treated as sensitive.

- [ ] Do not send actual passwords by email.
- [ ] Use high-entropy reset tokens.
- [ ] Tokens should not reveal the username or user ID.
- [ ] Tokens should expire quickly.
- [ ] Tokens should be single-use.
- [ ] Store the token mapping on the backend.
- [ ] Re-check the token when the password reset form is submitted.
- [ ] Do not allow users to change `username`, `email`, or `userId` in the reset POST request.
- [ ] Do not generate reset links from untrusted headers like `Host`.

Password reset poisoning red flag:

```http
Host: attacker.com
```

If the reset email uses this host to build the reset link, the attacker may steal the victim's token.

---

## 8. Password Change Security

Password change pages can also lead to account takeover.

Check:

- [ ] User must be logged in.
- [ ] Current password should be required.
- [ ] New password and confirm password should be validated properly.
- [ ] The server should check which account is being changed from the session, not from a user-controlled parameter.
- [ ] Password change attempts should have brute-force protection.
- [ ] Failed password change attempts should not leak whether the current password was correct in a useful way.

Red flag:

```text
username=carlos&current-password=FUZZ&new-password=test123
```

If this can be brute-forced without proper limits, it can lead to takeover.

---

## 9. Remember-Me / Stay-Logged-In Security

Remember-me cookies are dangerous if predictable.

Check if the cookie is based on:

- [ ] username
- [ ] timestamp
- [ ] password
- [ ] MD5 hash
- [ ] Base64 encoding
- [ ] unsalted hash

Bad examples:

```text
base64(username:password)
base64(username:md5(password))
username:timestamp
```

Better approach:

- [ ] Use a random high-entropy token.
- [ ] Store the token server-side.
- [ ] Bind it to the user securely.
- [ ] Expire and rotate tokens.
- [ ] Invalidate tokens after logout or password change.
- [ ] Apply brute-force protection to remember-me cookie guesses too.

Important:

> Base64 is encoding, not encryption.

---

## 10. Password Storage

Even if login is secure, password storage matters if the database leaks.

- [ ] Never store plaintext passwords.
- [ ] Do not use reversible encryption for passwords.
- [ ] Use a slow password hashing algorithm.
- [ ] Use a unique salt per password.
- [ ] Good options: `Argon2id`, `scrypt`, `bcrypt`, `PBKDF2`.
- [ ] Avoid fast hashes like `MD5`, `SHA1`, or plain `SHA256`.

Bad:

```text
md5(password)
sha256(password)
```

Better:

```text
password + unique salt -> slow password hashing algorithm -> stored hash
```

---

## 11. Multifactor Authentication / 2FA

MFA is useful only if the factors are actually different.

- [ ] App/device-generated OTP is better than email OTP.
- [ ] Email OTP is weaker because email access often depends on another password.
- [ ] SMS OTP can be vulnerable to SIM swapping and interception.
- [ ] The server must track which user passed the password step.
- [ ] The server must verify that MFA was completed before allowing access.
- [ ] MFA codes should expire quickly.
- [ ] MFA codes should be single-use.
- [ ] MFA verification should have brute-force protection.
- [ ] MFA recovery/reset should be protected as strongly as login.

Common bypass test:

```text
1. Login with password.
2. Reach MFA page.
3. Try directly visiting /my-account.
```

If the account page loads, the MFA check is broken.

---

## 12. Session Handling

A valid session is basically proof of login, so protect it properly.

- [ ] Session IDs should be random and high entropy.
- [ ] Session cookies should not contain predictable user data.
- [ ] Use `Secure`, `HttpOnly`, and `SameSite` cookie attributes.
- [ ] Regenerate session ID after login.
- [ ] Invalidate sessions after logout.
- [ ] Invalidate old sessions after password change.
- [ ] Set reasonable session expiry.
- [ ] Do not expose session tokens in URLs.

Red flag:

```text
session=base64(username)
```

---

## 13. Logging and Monitoring

Prevention is important, but detection also matters.

Log and monitor:

- [ ] repeated failed logins
- [ ] password spraying
- [ ] credential stuffing
- [ ] many password reset requests
- [ ] many MFA failures
- [ ] suspicious IP/location changes
- [ ] new device logins
- [ ] password changes
- [ ] email changes
- [ ] MFA disabled/reset events

Do not log actual passwords, tokens, or sensitive secrets.

---

## 14. Quick Testing Checklist

When testing or reviewing authentication, ask these questions:

- [ ] Can I enumerate valid users?
- [ ] Can I brute-force passwords?
- [ ] Can I bypass brute-force protection?
- [ ] Can I skip MFA?
- [ ] Can I change cookies or parameters to become another user?
- [ ] Can I abuse password reset?
- [ ] Can I abuse password change?
- [ ] Can I predict or brute-force remember-me cookies?
- [ ] Are tokens random, high entropy, short-lived, and single-use?
- [ ] Does the server re-check important tokens on POST requests?
- [ ] Are sessions protected properly?
- [ ] Are supplementary features as secure as the main login form?

---

## 15. One-Line Revision Summary

Authentication security is not just about checking a password. Every login-related feature must avoid information leaks, resist brute force, use strong server-side verification, protect tokens/sessions, and keep recovery features as secure as the main login flow.
