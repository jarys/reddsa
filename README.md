A minimal [RedDSA][reddsa] implementation for use in Zcash.

Two parameterizations of RedJubjub are used in Zcash, one for
`BindingSig` and one for `SpendAuthSig`. This library distinguishes
these in the type system, using the [sealed] `SigType` trait as a
type-level enum.

In addition to the `Signature`, `SigningKey`, `VerificationKey` types,
the library also provides `VerificationKeyBytes`, a [refinement] of a
`[u8; 32]` indicating that bytes represent an encoding of a RedJubjub
verification key. This allows the `VerificationKey` type to cache
verification checks related to the verification key encoding.

## Examples

Creating a `BindingSig`, serializing and deserializing it, and
verifying the signature:

```rust
# use std::convert::TryFrom;
use rand::thread_rng;
use reddsa::*;

let msg = b"Hello!";

// Generate a secret key and sign the message
let sk = SigningKey::<Binding>::new(thread_rng());
let sig = sk.sign(thread_rng(), msg);

// Types can be converted to raw byte arrays using From/Into
let sig_bytes: [u8; 64] = sig.into();
let pk_bytes: [u8; 32] = VerificationKey::from(&sk).into();

// Deserialize and verify the signature.
let sig: Signature<Binding> = sig_bytes.into();
assert!(
    VerificationKey::try_from(pk_bytes)
        .and_then(|pk| pk.verify(msg, &sig))
        .is_ok()
);
```

## docs

```shell,no_run
cargo doc --features "nightly" --open
```

[reddsa]: https://zips.z.cash/protocol/protocol.pdf#concretereddsa
[zebra]: https://github.com/ZcashFoundation/zebra
[refinement]: https://en.wikipedia.org/wiki/Refinement_type
[sealed]: https://rust-lang.github.io/api-guidelines/future-proofing.html#sealed-traits-protect-against-downstream-implementations-c-sealed
