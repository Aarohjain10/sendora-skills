---
name: sendora-channels-technical
description: "Technical guide to sending infrastructure via the MCP: email accounts and warmup, domains and DNS, sender pools, deliverability tests, SMS registration, purchases with quotes. Phone numbers and caller IDs are in the voice guide."
---

# Channels and infrastructure: technical guide

Phone numbers and caller IDs are covered in `sendora-voice-technical`. Strategy for staying out of spam is in `sendora-channels-business`.

## 1. Sending accounts and warmup

### Read

`read_channels` `accounts` lists them (filter channel, status). One account with `id` and `include` of `profile, health, limits, strength, incidents, preferences`. Statuses and lifecycle fields tell you if it can send now.

### Change

`edit_channels` `update_account` (body fields): display name, sender name, signature, active hours and weekend mode (`preferences`), and daily `limits` per action. Send only what changes.

### Steps

`edit_channels` `account_step` with `action`:

| Action | Use |
|---|---|
| `ack_reconnect` | Dismiss a reconnect prompt after reconnecting |
| `warmup_start` / `warmup_pause` / `warmup_resume` / `warmup_stop` | Control warmup |
| `resetup` | Re-run setup that failed |
| `uncancel` | Undo a cancelled managed mailbox |

### Health signals

Strength score low, bounces rising, a lifecycle reason other than active, a reconnect prompt. Report these before they cause a pause.

### Disconnect

`delete_resource` `account` disconnects SMTP, WhatsApp or LinkedIn accounts or cancels a managed mailbox. Add `confirm_unassign` only if it is in use and the user agrees. LinkedIn accounts are sensitive: it is better not to push automation limits higher than the defaults.

### Add new accounts

Buying managed mailboxes is a purchase (sendora-channels-technical). Connecting WhatsApp, LinkedIn or your own mailbox is done by the user in the app because it needs their sign in.

## 2. Domains and DNS

### Read

`read_channels` `domains`; one domain with `include: "dns"` shows each record and its state.

### Connect a domain you own

`edit_channels` `connect_domain` with `domain_name`. Then follow the records it returns.

### Verify and repair

`edit_channels` `domain_step` with `action`:

- `verify_dns`: re-check records
- `repair_dns`: fix records Sendora manages
- `check_nameservers` / `verify_nameservers`
- `refresh`

`update_domain` changes DNS records (`dns` add, update, delete), `forwarding_url`, `catch_all_email`, `dmarc_reporting_email`, `mask_forwarding` and tags.

### Remove

`delete_resource` `domain`. Add `confirm_unassign` only if mailboxes still use it and the user agrees.

## 3. Sender pools

### Read

`read_channels` `sender_pools` (filter `channel`), or one pool with its members by `id`.

### Create and change

- `edit_channels` `create_sender_pool` with `name`, `channel` and `strategy` (least_used, round_robin, weighted, sticky_by_lead).
- `update_sender_pool`: rename, change the rule, `add_members`, `remove_members`, `member_settings` (weight, paused).

### Pick a strategy

- `least_used`: even load, good default.
- `round_robin`: simple rotation.
- `weighted`: send more from stronger accounts.
- `sticky_by_lead`: the same lead always hears from the same sender, best for follow ups.

### Guidelines

Keep pools single channel. Remove weak or flagged accounts from the pool rather than deleting them. A pool cannot be deleted while a live campaign uses it (409).

## 4. Deliverability tests

### Tests

- `read_channels` `deliverability_tests` lists tests (filter `test`), or one by `id`.
- `edit_channels` `start_deliverability_test` with `test` of `blacklist` or `placement` and `account_ids`.

Run a placement test before the first campaign and weekly while scaling.

## 5. SMS registration

US texting needs a registered brand and campaign. Carrier review takes days.

### Read

`read_channels` `sms_readiness`: can this workspace text yet, what is next, the allowed form options, and an optional fee quote with `quote`. It helps to read the option lists from it: values are lowercase and exact.

`sms_registrations` lists brands and campaigns, or reads one by `type` and `id` with `include` of `events, numbers, eligible_numbers`.

### Flow

1. Read readiness.
2. `create_sms_registration` as a **brand** draft (legal name, type, tax id where required, address, contact, website). Use the company's real information.
3. Verify contact: `sms_registration_step` `send_code`, then `verify_code`.
4. `update_sms_registration` until complete.
5. Optional trust check on the brand: `run_channels` `request_sms_vetting` (paid).
6. `run_channels` `submit_sms_registration` for the brand (charges the wallet). Get a yes first and state the fee from the quote.
7. After approval, create the **campaign** draft: use case, sample messages, opt in flow description, opt out and help keywords.
8. Submit it.
9. `attach_sms_numbers` (charges) to link numbers.
10. Follow `events` for review status. If rejected, `sms_registration_step` `appeal` or `clone` and fix.

### Delete

`delete_resource` `sms_registration` with `type` brand or campaign.

## 6. Purchases

Anything that costs money goes quote, confirm, buy, verify.

### Steps

1. `edit_channels` `quote_purchase` with `kind` (phone number, domain, mailbox, WhatsApp account) and `selection`. Charges nothing and returns a `quote_id` with the total.
2. Tell the user the item, the price, and what balance it comes from (`read_platform` `billing` with `billing.wallet`).
3. Wait for a clear yes.
4. `run_channels` `buy` with `kind`, `quote_id` and the kind's fields. `pay_with: wallet` spends prepaid credit now.
5. Follow it: `read_platform` `purchases` with `operation_id` until done.
6. Read the new item back.

### Guidelines

- A quote expires. If the buy says it did, quote again and re-confirm.
- It is better not to buy twice because of a timeout: read `purchases` first.
- It is better not to use a card or a payment detail. Payments run on the wallet or the saved method.
- If capacity is full, say so and ask whether to upgrade (`billing.capacity`).
