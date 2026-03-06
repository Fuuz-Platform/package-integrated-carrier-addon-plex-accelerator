# package-integrated-carrier-addon-plex-accelerator

**Version:** 0.0.1
**Spec Version:** 2.0.0

---

## Overview

This package extends the Integrated Carrier suite with Plex ERP-specific integration features. It installs the `Load Plex Integrated Shipper` flow (which populates carrier account and shipper data from Plex into Fuuz), an `Integrated Carrier Shipment (Extension)` screen that adds Plex-specific fields and actions to the base shipment workflow, and an `ExternalApplicationConfiguration` record that enables the Fuuz Browser Extension to interact with the shipping screen from within the Plex ERP web interface.

This addon is optional — install it only in environments where Plex ERP is the primary business system and operators initiate shipments from within Plex.

---

## Package Contents

```
integrated-carrier-addon-plex/
├── manifest.json
├── package-data.json
├── install/                     6 install steps
├── preinstall/                  4 preinstall verification steps
└── postinstall/                 1 postinstall step
```

---

## Installed Components

### Load Plex Integrated Shipper (`Integration` flow)

Syncs Plex shipper (vendor/customer) configuration into Fuuz `IntegratedCarrierAccount` records:
- Queries Plex for configured shipping accounts and carrier credentials
- Maps Plex carrier account data to Fuuz `IntegratedCarrierAccount` fields
- Creates or updates carrier account records with Plex-sourced credentials
- Enables Plex-managed carrier accounts to be used in Fuuz shipment flows without manual re-entry

### Integrated Carrier Shipment (Extension) Screen

An extended version of the base Integrated Carrier Shipment screen with Plex-specific additions:
- **Plex Container / Shipment context** — Auto-populates shipment fields from Plex Container or Sales Order context when opened from Plex via the browser extension
- **Plex-linked recipient** — Auto-fills ship-to address from Plex Customer record
- **Post-label callback** — After label generation, posts the tracking number and carrier details back to the Plex Sales Order or Container record via Plex API
- **Plex shipment reference** — Captures Plex shipment/container ID in `IntegratedCarrierRequest.externalId` for cross-system reconciliation

### ExternalApplicationConfiguration: `mfgxBrowserExtensionIntegratedCarrierShipping`

Configures the Fuuz Browser Extension to recognize Plex ERP shipment pages and inject the `Integrated Carrier Shipment (Extension)` screen as an overlay. Enables operators to generate carrier labels without leaving Plex.

---

## Install Process

**Preinstall (4 steps):** Verifies that the `Load Plex Integrated Shipper` flow, the extension screen (by ID and version ID), and the `ExternalApplicationConfiguration` record don't already exist before installing.

**Install (6 steps):** Creates flow header + version, creates screen header + version, creates `ExternalApplicationConfiguration` record, deploys all.

**Postinstall (1 step):** Registers the extension configuration with the Fuuz platform to activate browser extension recognition.

---

## Installation

1. Install the full Integrated Carrier core suite: `package-integrated-carrier-core-schema-accelerator`, `package-integrated-carrier-core-flows-accelerator`, `package-integrated-carrier-core-screens-accelerator`
2. Install at least one carrier package (`package-integrated-carrier-fedex-accelerator` or `package-integrated-carrier-ups-accelerator`)
3. Import this package via Fuuz Package Manager
4. Ensure the Fuuz Browser Extension is installed in the Plex-facing browsers
5. Run `Load Plex Integrated Shipper` to sync carrier accounts from Plex
6. Test by opening a Plex Sales Order and verifying the shipping extension overlay appears

---

## Dependencies

- **`package-integrated-carrier-core-schema-accelerator`** — required
- **`package-integrated-carrier-core-flows-accelerator`** — required
- **`package-integrated-carrier-core-screens-accelerator`** — required
- **`package-integrated-carrier-fedex-accelerator`** and/or **`package-integrated-carrier-ups-accelerator`** — at least one carrier required
- **Plex ERP** with API access enabled (for shipper sync and tracking number writeback)
- **Fuuz Browser Extension** installed in operator browsers for the screen overlay feature

---

## Part of the Integrated Carrier Suite

| Package | Description |
|---------|-------------|
| `integrated-carrier-core-schema` | Data models |
| `integrated-carrier-core-flows` | Router and print flows |
| `integrated-carrier-core-screens` | Shipment management screens |
| `integrated-carrier-fedex` | FedEx seed data and label flows |
| `integrated-carrier-ups` | UPS seed data and label flows |
| **integrated-carrier-addon-plex** (this) | Plex ERP integration extension |

---

*Built on the [Fuuz Industrial Operations Platform](https://fuuz.com)*
