# Email Automation → Classification, CRM & Draft Replies (n8n)

An n8n workflow that automatically processes incoming emails end to end: it understands each message with an LLM, keeps the inbox organised, registers senders in a CRM, drafts reply suggestions, and notifies about new leads.

Built with n8n, Google Gemini, HubSpot and the Gmail API.

<img width="2076" height="1025" alt="n8n" src="https://github.com/user-attachments/assets/29c049fd-d70d-4412-8cab-a64780309c56" />

## What it does

Whenever a new email arrives, the workflow:

1. **Catches the email** — a Gmail trigger polls the inbox every minute.
2. **Guards against loops** — an *If* node drops the workflow's own automated emails (its "new contact" and "error" notifications) so it never triggers itself.
3. **Classifies the content** — a Google Gemini text classifier reads the message and assigns it to a category: `Work`, `Packages`, `Personal`, or `Other` (fallback).
4. **Organises the inbox** — based on the category, the matching Gmail label is applied to the message.
5. **Feeds the CRM (Work branch)** — the sender of a business email is created or updated as a contact in HubSpot. It's an *upsert* keyed on the email address, so the same person is never duplicated. The address is parsed out of the raw `From` header with a JavaScript expression.
6. **Notifies about new leads** — when the contact is genuinely new (checked via HubSpot's `isNew` flag), an email notification is sent with the sender's address and the original subject.
7. **Drafts a reply (Personal branch)** — an AI Agent (Gemini) with memory and access to previously sent emails writes a suggested reply and saves it as a Gmail draft, matching the user's writing style and the language of the incoming email.

A separate **error workflow** is wired in via the workflow settings: if any node fails during a production run, it fires automatically and sends an email with the failed node and the error message.

## Setup

To run this workflow you need an n8n instance and your own credentials for:

- Gmail (OAuth2)
- Google Gemini (API key)
- HubSpot (private app access token, contacts read/write scope)

Import the `.json` file into n8n, then create and select your own credentials on each node.

## Privacy & security

This export contains **no credentials or secrets** — n8n stores those separately, and the file only references credentials by name. The notification recipient address has been replaced with a placeholder, and no execution/test data is included. Configure your own credentials after importing.

