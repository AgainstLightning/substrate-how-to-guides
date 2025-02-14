---
sidebar_position: 2
keywords: 
    - storage migration
    - runtime
---

# Trigger a storage migration
_Ready, steady ... migrate!_

## Goal

Trigger a migration using Polkadot-js apps.

## Use cases
On-chain runtime upgrades that require a storage migration. 
## Overview

This simple guide presents the steps for triggering a runtime migration using Polkadot-JS apps. It
assumes that migration code is already written and that the new runtime has already been compiled.

## Steps

### 1. Add custom types
In the Polkadot-js apps UI, go to `Settings > Developer` to add your custom types from `types.json`. You can either upload the file directly or paste the types right into the UI. Save it to add them.
### 2. Upload your runtime 

In `Developer > Sudo`, make a `system.setCode` call by uploading your runtime (for example, `./target/release/wbuild/node-template-runtime/node_template_runtime.compact.wasm`). 

Check the _"with weight override"_ toggle to ignore block weights and set it to some arbitrary value. 

### 3. Trigger the call 

Hit _"Submit Sudo Unchecked"_ and sign the transaction to trigger the call.  


## Examples

- [Migrating the Nicks pallet](https://github.com/substrate-developer-hub/migration-example/pull/2/files).

## Resources

#### Other
- [Polkadot-JS Apps](https://polkadot.js.org/apps/)
- [Polkadot JS documentation](https://polkadot.js.org/docs/)
