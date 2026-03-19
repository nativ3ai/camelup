# Third-Party Notices

## Google Research CaMeL References

This repository references and installs a Hermes-native runtime integration inspired by the following research:

- Paper: `Defeating Prompt Injections by Design`
  - https://arxiv.org/abs/2503.18813
  - https://arxiv.org/pdf/2503.18813
- Reference repository:
  - https://github.com/google-research/camel-prompt-injection

## Relationship To This Repository

`camelup` is an installer and wiring tool. It does not present itself as Google's CaMeL implementation, nor as a full reproduction of the paper's evaluation stack.

The guarded Hermes build that `camelup` installs follows the trust-boundary principles described in the paper, but adapts them to Hermes' runtime, tool loop, and operational model.

To the best of this repository's authorship intent, Google source code is not vendored into this installer repository unless explicitly noted in future changes.
