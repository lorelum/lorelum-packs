---
anti_patterns:
  - description: Storing login passwords as plaintext, reversible ciphertext, or fast digests instead of dedicated salted password verifiers.
    id: backend-fundamentals.security.recoverable-or-fast-password-storage
    name: Storing a recoverable or cheaply guessed password
    severity: critical
applies_when: creating, resetting, changing, or verifying locally managed login passwords
id: backend-fundamentals.security.hash-passwords-for-storage
severity: critical
stage: implementation
tech_stack:
  - security
title: Store Passwords with a Dedicated Password Hash
---

## When to apply

Apply when the application owns login-password authentication. Store a verifier that supports authentication without retaining a recoverable password or making offline guesses cheap after database theft. Password strength, sessions, and resource permissions are separate decisions.

## Guidance

1. Establish ownership. If a trusted identity provider manages passwords, keep its supported identity linkage instead of collecting a second password copy. Do not introduce local password storage into an identity-provider-only application.
2. Use a maintained high-level password-hashing library. Prefer Argon2id for new systems where supported; choose another dedicated algorithm only under applicable deployment or compliance constraints and current OWASP guidance. Never substitute a general-purpose digest or custom cryptographic construction.
3. Let the library generate a secure random per-password salt and store its supported encoded verifier, including algorithm, salt, and cost parameters. Do not retain plaintext or a reversible copy for login or recovery. A fixed shared salt is insufficient.
4. Select costs from a documented security baseline and assess deployment memory and expected authentication load. Retain the parameter source rather than copying unexplained iteration counts. Follow documented input limits without silently truncating passwords. Registration, reset, and change must use the same storage policy.
5. Verify through the library's verification API; mismatches fail authentication. Do not generate a fresh salted hash and compare strings. Use the supported rehash check after successful verification to upgrade outdated verifiers under current policy, or the established reset path when migration is unsafe. Keep passwords and verifiers out of responses and logs.

Stop when every local password write stores only the supported verifier, login uses its verification API, and costs and upgrades have an explicit basis.

## Anti-pattern

Registration stores `SHA256(password)` while reset encrypts the password for support recovery. One permits cheap offline guessing; the other exposes recoverable passwords if its key leaks. Adding a shared salt does not supply a dedicated adaptive verifier.

## Why

Offline guesses bypass online rate limits. Dedicated password hashes raise each guess's cost, while unique salts prevent reusing one computation across accounts. Encoded parameters support verification and later upgrades, but cannot make weak passwords unguessable.

## Exceptions and boundaries

Supported alternatives have different cost and input constraints: scrypt, legacy bcrypt, or FIPS-validated PBKDF2 cannot inherit Argon2id settings blindly. Existing weak hashes need migration or reset, not only safer registration. High-entropy tokens and recoverable integration secrets are different storage problems. An optional pepper does not replace salts or a dedicated hash.

## Example

A service uses `argon2-cffi`'s `PasswordHasher` with a documented deployment-appropriate profile. Registration and reset store `hash(password)`; login calls `verify(stored_hash, candidate)`. After success, `check_needs_rehash` selects upgrades. SSO-only accounts retain provider linkage without local passwords.
