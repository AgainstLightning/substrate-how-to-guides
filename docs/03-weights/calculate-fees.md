---
sidebar_position: 4
keywords: weights, fees, runtime, FRAME v1
---

# Calculating fees

_Learn how to calculate fees from within your runtime._

## Goal

Customize `WeightToFee` to modify how fees are calculated for your runtime.

## Use cases

Modify the way fees are calculated, instead of using [`IdentityFee`][identityfee-rustdocs] which maps one unit of fee to one unit of weight.

## Overview

Fees are broken down into three components:

- **Byte fee** - A fee proportional to the transaction's length in bytes. The proportionality constant is a parameter in the transaction_payment pallet.
- **Weight fee** - A fee calculated from the transaction's weight. Weights quantify the time spent executing the transaction. Learn more in the recipe on weights. The conversion doesn't need to be linear, although it often is. The same conversion function is applied across all transactions from all pallets in the runtime.
- **Fee Multiplier** - A multiplier for the computed fee, that can change as the chain progresses.

FRAME provides the [transaction payment pallet][transaction-frame] for calculating and collecting fees for executing transactions. It 
can be useful to modify the way fees are calculated to charge fees with more accuracy to your users. This guides steps through the process of customizing `WeightToFee` for your runtime's implementation of `pallet_transaction_payment`. 

## Steps

### 1. Write the `LinearWeightToFee` struct
In `runtime/libs.rs`, create the struct called `LinearWeightToFee` that implements `WeightToFeePolynomial`. It must return
a smallvec of `WeightToFeeCoefficient` integers:

```rust
pub struct LinearWeightToFee<C>(sp_std::marker::PhantomData<C>);

impl<C> WeightToFeePolynomial for LinearWeightToFee<C>
where
	C: Get<Balance>,
{
	type Balance = Balance;

	fn polynomial() -> WeightToFeeCoefficients<Self::Balance> {
		let coefficient = WeightToFeeCoefficient {
			coeff_integer: C::get(),
			coeff_frac: Perbill::zero(),
			negative: false,
			degree: 1,
		};

		smallvec!(coefficient)
	}
}
```

### 2. Configure `pallet_transaction_payment` in your runtime

Convert the dispatch weight
`type WeightToFee` to the chargeable fee `LinearWeightToFee` (replacing `IdentityFee<Balance>;`):

```rust
parameter_types! {
    // Used with LinearWeightToFee conversion.
	pub const FeeWeightRatio: u128 = 1_000;
	// Establish the byte-fee. It is used in all configurations.
	pub const TransactionByteFee: u128 = 1;
}

impl pallet_transaction_payment::Config for Runtime {
	type OnChargeTransaction = CurrencyAdapter<Balances, ()>; 
	type TransactionByteFee = TransactionByteFee;

	// Convert dispatch weight to a chargeable fee.
	type WeightToFee = LinearWeightToFee<FeeWeightRatio>;

	type FeeMultiplierUpdate = ();
}
```

## Examples

- [transaction-payment-pallet][transaction-frame]
- pallet-weights

## Related material
#### How-to guides
- [Conditional weighting struct](./conditional-weight-struct)
- [Linear weighting struct](./linear-weight-struct)
- [Quadratic weighting struct](./quadratic-weight-struct)

#### Knowledgebase

#### Rust docs
- [`WeightToFeeCoefficients`](https://substrate.dev/rustdocs/v3.0.0/frame_support/weights/type.WeightToFeeCoefficients.html)

- [`WeightToFeeCoefficient`](https://substrate.dev/rustdocs/v3.0.0/frame_support/weights/type.WeightToFeeCoefficient.html)
- [`WeightToFeePolynomial`](https://crates.parity.io/frame_support/weights/trait.WeightToFeePolynomial.html)

[transaction-frame]: https://github.com/paritytech/substrate/tree/master/frame/transaction-payment
[identityfee-rustdocs]: https://crates.parity.io/frame_support/weights/struct.IdentityFee.html