---
description: Chronological changelog for cheqd Studio portal releases.
---

# ♻️ Changelog

### Release 5: Issue Credential and support external providers (2025-10-21)

#### Features

* Add Download Credential & Supported Providers env
* Add Credential issuance using cheqd Studio
* Support for JWT and JSON-LD credentials
* Begin integration of dock issuance & credential history
* Create new page for issuance providers
* Update schema type to array & Add member did validations
* Validate Verification methods for Dock

#### Bug Fixes

* Add email validation in credenitals
* Adjusted responsiveness and min-max widths + more conservative resolution parallelisation limit
* Augmented state handling + interactive management
* Client-side favourable DID state management + table sorting
* did dropdowns with consistent naming & schema handling in members
* disable trust validation and tracking
* fix expiry date not appearing correctly
* encoded resourceName handling
* error message on credential issue failure
* Fix ecosystems state update
* invalid did value passed in dropdown
* Minor UI improvements
* Pagination and enable Dock provider
* resizing and assertionMethod toggle
* significant improvement to session handling
* Ui state management improvments

#### Performance Improvements

* Integrate did metadata api

***

### Release 4: Optimisations and State management improvement (2025-09-12)

#### Features

* Display Resource Versions & UI improvements

#### Bug Fixes

* Apply member filters in server
* Create resource operation
* Improved state management & fixes in list operations
* Replace Buffer with Uint8arrays

***

### Release 3: Add support for Organizations and Agents (2025-09-09)

#### Features

* Update Service section to support did name, type fields
* Add organizations and agents as default options for identifiers

#### Bug Fixes

* Url validations & Create DID form fixes

***

### Release 2: Add support for Trust Ecosystems (2025-09-03)

#### Features

* Support Trust ecosystems in UI, including accreditations for members in the ecosystem
* Add authorization for specific schemas for members of ecosystems

#### Bug Fixes

* Release versioning + v1.7.1 beta

***

### Release 2: Add DID-Linked Resources in UI (2025-08-21)

#### Features

* Add create Resource underneath DIDs in UI
* Optimize data rendering in create-resource
* Add new Settings Tabs
* User menu changes

#### Bug Fixes

* nav bar update
* Remove usdc prices

***

### Release 1: Add new dashboard for cheqd Studio metrics (2025-08-13)

#### Features

* Add features for Dashboard and Api access in portal
* Add create DID option in UI
* Improve UI for managing API keys

