# 🔐 xsukax NTFY Encrypted Chat

A lightweight, account-free, end-to-end encrypted chat client that uses [ntfy](https://ntfy.sh/) as a message relay.

**No registration. No API keys. No database. No backend. No dependencies.**

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-2ea44f)](https://xsukax.github.io/xsukax-NTFY-Encrypted-Chat/)

### 🌐 [Open Live Demo](https://xsukax.github.io/xsukax-NTFY-Encrypted-Chat/)
### 🌐 [Open GPG / PGP Live Demo](https://xsukax.github.io/xsukax-NTFY-Encrypted-Chat/GPG_PGP_Chat.html)

---

## Overview

**xsukax NTFY Encrypted Chat** is a single-file browser application for private two-user messaging.

The application encrypts messages locally before sending them through an ntfy server. The relay receives encrypted message bodies rather than readable chat text or display names.

By default, the app uses:

```text
https://ntfy.sh
```

A compatible self-hosted ntfy server can also be used.

The project is built entirely with:

* HTML
* CSS
* Vanilla JavaScript
* Web Crypto API
* ntfy HTTP/SSE APIs

No installation, package manager, framework, account, API key, or application server is required.

---

## ✨ Features

* 🔐 End-to-end encrypted message content
* 👤 Encrypted display names
* 🔑 Shared-password room encryption
* 🎲 Cryptographically random room secrets
* 🛡️ Safety code for verifying both users have the same room/password
* 🔗 One-click invitation links
* 📋 Copy room code and shared password
* 🌐 Default `ntfy.sh` support
* 🖥️ Custom compatible ntfy server support
* ⚡ Real-time messages using Server-Sent Events
* 🔄 Automatic SSE reconnection
* 🕓 Recent encrypted relay history
* 👀 Direct inspection of the encrypted ntfy topic
* 📱 Responsive desktop, tablet, and mobile interface
* 🌙 GitHub-inspired dark design
* 🚫 No registration or user accounts
* 📦 Entire application contained in one `index.html`

---

## 🔐 Encryption

Messages are encrypted directly inside the browser before being published to ntfy.

The application currently uses:

| Component         | Implementation              |
| ----------------- | --------------------------- |
| Encryption        | AES-256-GCM                 |
| Password KDF      | PBKDF2-HMAC-SHA-256         |
| PBKDF2 iterations | 600,000                     |
| AES key size      | 256-bit                     |
| AES-GCM IV        | Random 12 bytes per message |
| Hashing           | SHA-256                     |
| Cryptography API  | Browser Web Crypto API      |

Encrypted relay messages use a format similar to:

```text
xc1.<iv>.<ciphertext>
```


The GPG / PGP application currently uses:

| Component | Implementation |
|---|---|
| Encryption standard | OpenPGP / PGP |
| OpenPGP library | OpenPGP.js 5.10.2 |
| Key model | Shared PGP private key used by both users |
| Encryption key | Public component derived internally from the shared private key |
| Decryption key | Shared OpenPGP private key |
| Signing | Shared OpenPGP private key |
| Signature verification | Public component derived internally from the same shared private key |
| Default generated key type | ECC / Curve25519 |
| Private-key passphrase | Optional; supports protected and passphrase-less private keys |
| Message format | Binary OpenPGP packet |
| Relay encoding | Base64URL with `xpgp1.` prefix |
| Topic hashing | SHA-256 |
| Verification-code hashing | SHA-256 |
| Random generation | Browser `crypto.getRandomValues()` |
| Cryptography implementation | OpenPGP.js with browser cryptographic capabilities |
| Private-key storage | Not stored in `localStorage`; held only during the active browser session |
| Transport | NTFY over HTTPS / SSE |
| Relay visibility | NTFY receives only the OpenPGP-encrypted message envelope |

### Key Model

Both users use the same OpenPGP private key.

The application does not require users to import or exchange a separate public key. The public component needed for standard OpenPGP encryption is derived internally from the shared private key.

```text
Shared OpenPGP Private Key
        │
        ├── Private component → Decryption and signing
        │
        └── Public component  → Encryption and signature verification
```

The plaintext message and display name are contained inside the encrypted payload.

The ntfy topic itself is derived from a cryptographically random room secret using SHA-256, producing a pseudorandom topic instead of a readable room name.

---

## 🚀 Usage

### 1. Open the application

Visit:

**https://xsukax.github.io/xsukax-NTFY-Encrypted-Chat/**

### 2. Enter a display name

For example:

```text
Alice
```

### 3. Choose the ntfy server

The default is:

```text
ntfy.sh
```

Most users can leave this unchanged.

### 4. Create a room

Click **New**.

A random room secret will be generated.

### 5. Generate a strong password

Click:

**Generate strong password**

Passwords must contain at least 20 characters.

Using the generated random password is strongly recommended.

### 6. Share the invitation

Use:

* **Copy one-click invite**
* **Room code**
* **Copy link + password**

Share the invitation only with the intended participant.

### 7. Enter the room

Both participants click:

**Enter room**

### 8. Compare the safety code

Both users should see the same safety code, for example:

```text
A813-00E4-91D5-7C20
```

If the codes do not match, verify the room secret and shared password before sending sensitive information.

### 9. Start chatting

Messages are encrypted before being sent to the ntfy relay.

Press **Enter** to send or **Shift + Enter** for a new line.

---

## 🛡️ Safety Code

The safety code is derived from both:

* Room secret
* Password-derived encryption key

Both participants should see exactly the same code.

A different code means the users are not using the same room/password combination.

For sensitive conversations, compare the code through a separate trusted channel.

---

## 👀 Verify the Relay Encryption

After entering a room, the application provides:

* **Open NTFY topic**
* **Open encrypted JSON feed**
* **Copy relay URL**

Opening the actual ntfy topic should show encrypted data beginning with:

```text
xc1.
```

The plaintext conversation and display names should not appear in the relay message body.

---

## 🌐 Custom ntfy Server

A compatible self-hosted ntfy server can be entered instead of `ntfy.sh`.

Example:

```text
https://ntfy.example.com
```

Remote servers must use HTTPS.

For local testing, HTTP is allowed for:

```text
http://localhost
http://127.0.0.1
```

The ntfy server must allow the browser to publish and subscribe to the selected topic.

The current client is designed primarily for ntfy servers that allow anonymous publishing and subscribing.

---

## 🔗 Invitation Links

One-click invitations contain the room information inside the URL fragment:

```text
#v=2&r=ROOM&s=SERVER&p=PASSWORD
```

URL fragments are handled by the browser and are not normally included in HTTP requests to the website hosting the application.

After reading an invitation, the application removes the fragment from the visible address bar.

### Important

A complete invitation can contain the shared password and room secret.

**Anyone who obtains the full invitation may be able to decrypt the conversation.**

Treat invitation links like passwords and share them privately.

---

## 🔒 Security

The project is designed to protect **message content**, but encrypted messaging does not eliminate every security or privacy risk.

### What is encrypted

The application encrypts:

* Message text
* Display name
* Message identifier
* Sender session identifier
* Client message timestamp

Encryption happens before the message is sent to ntfy.

### What the relay can still observe

The ntfy server and network infrastructure may still be able to observe metadata such as:

* IP addresses
* Connection times
* Derived ntfy topic
* Message timing
* Message frequency
* Ciphertext size
* Network request information

The project therefore provides encrypted message content, **not network anonymity**.

### Public ntfy topics are not authentication

A random topic is difficult to guess, but topic secrecy should not be treated as a replacement for encryption or access control.

Message confidentiality depends primarily on:

* The room secret
* The shared password
* The browser-side encryption
* The integrity of the application code

### Endpoint security matters

Encryption cannot protect a conversation if a participant's device is compromised.

Potential risks include:

* Malicious browser extensions
* Malware
* Compromised operating systems
* Modified application code
* Clipboard monitoring
* Keyloggers
* Screen capture
* Browser vulnerabilities

### Hosting integrity matters

The encryption code runs inside the browser.

A compromised copy of the website could theoretically serve modified JavaScript that steals passwords or plaintext messages.

For highly sensitive use, users should consider reviewing the source code and hosting a trusted copy themselves.

### Password security

Use the built-in random password generator whenever possible.

Avoid predictable passwords such as:

```text
password123
mysecretroom
```

A strong random password provides substantially better resistance to password guessing.

### No absolute security guarantee

This application does **not** claim to provide “100% security.”

Security may be affected by:

* Weak passwords
* Leaked invitations
* Compromised devices
* Malicious browser extensions
* Browser vulnerabilities
* Modified source code
* User mistakes
* Traffic analysis
* Relay outages
* Denial-of-service attacks
* Undiscovered implementation vulnerabilities

Users with high-risk or mission-critical security requirements should independently review the software and its threat model.

---

## 🕓 Message History

The client requests recent encrypted messages from the relay when joining.

The current application requests approximately:

```text
12 hours
```

of recent relay history.

Actual retention depends on the ntfy server configuration.

The chat should therefore **not be considered permanent message storage**.

---

## ⚠️ Limitations

* No user accounts or identity system
* No public-key identity verification
* No Signal-style Double Ratchet
* No forward-secrecy protocol
* Shared-password security model
* Relay metadata remains observable
* Public ntfy availability and rate limits are outside this project's control
* Messages are subject to ntfy payload-size limits
* Current plaintext message limit is 2,500 characters
* Both users should use the same compatible application version

The application is intended as a lightweight encrypted chat client, not a replacement for a professionally audited secure-messaging platform.

---

## 📦 Project Structure

```text
xsukax-NTFY-Encrypted-Chat/
├── index.html
├── README.md
└── LICENSE
```

### `index.html`

Contains the complete application:

* User interface
* Responsive styling
* Room generation
* Password generation
* AES-GCM encryption/decryption
* PBKDF2 key derivation
* Safety-code generation
* ntfy publishing
* SSE receiving
* Invitation handling
* Relay inspection

No compilation or build process is required.

---

## 🖥️ Running Locally

Clone the repository:

```bash
git clone https://github.com/xsukax/xsukax-NTFY-Encrypted-Chat.git
cd xsukax-NTFY-Encrypted-Chat
```

Run a simple local web server.

Using Python:

```bash
python3 -m http.server 8080
```

Then open:

```text
http://localhost:8080
```

Or with PHP:

```bash
php -S localhost:8080
```

The application can also be hosted on any normal HTTPS static web host.

---

## 🤝 Contributing

Contributions, bug reports, security reviews, accessibility improvements, and compatibility fixes are welcome.

Please keep contributions aligned with the project's goals:

* Keep dependencies minimal
* Prefer browser-native APIs
* Preserve the lightweight single-file design where practical
* Never transmit plaintext chat messages
* Never log or expose shared passwords
* Do not weaken cryptographic settings
* Maintain mobile and desktop compatibility
* Avoid unnecessary tracking or persistent identifiers

Security-sensitive changes should be carefully reviewed and tested.

Pull requests can be submitted through:

**https://github.com/xsukax/xsukax-NTFY-Encrypted-Chat/pulls**

---

## 🐛 Issues & Support

For bugs, questions, and feature requests:

**https://github.com/xsukax/xsukax-NTFY-Encrypted-Chat/issues**

When reporting a bug, include the browser, operating system, ntfy server, and steps needed to reproduce the problem.

**Never post room secrets, passwords, private invitation links, or sensitive conversation contents in a public issue.**

---

## 🚨 Security Vulnerabilities

Security vulnerabilities should be reported privately whenever possible.

Do not publish working exploits, active room credentials, passwords, or sensitive vulnerability details in a public GitHub Issue before the problem can be reviewed.

Use GitHub's private vulnerability reporting feature if it is enabled for the repository.

---

## 📜 License

This project is licensed under the **GNU General Public License v3.0 (GPL-3.0)**.

Users are free to use, study, modify, and redistribute the software under the terms of the GPL-3.0.

See the `LICENSE` file for the complete license terms.

---

## 👨‍💻 Author / Maintainer

**xsukax**

GitHub:
https://github.com/xsukax

Repository:
https://github.com/xsukax/xsukax-NTFY-Encrypted-Chat

---

## 🔗 Links

**Live Demo**
https://xsukax.github.io/xsukax-NTFY-Encrypted-Chat/

**Source Code**
https://github.com/xsukax/xsukax-NTFY-Encrypted-Chat

**Issues / Support**
https://github.com/xsukax/xsukax-NTFY-Encrypted-Chat/issues

---

<div align="center">

### 🔐 xsukax NTFY Encrypted Chat

**Simple · Account-Free · Browser-Based · Encrypted**

⭐ If you find the project useful, consider starring the repository.

</div>
