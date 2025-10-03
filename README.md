<div align="center">
  <img height="170x" src="https://cdn.discordapp.com/attachments/1415994670793756793/1423795254091452626/ChatGPT_Image_Oct_3_2025_11_08_11_PM.png?ex=68e19c29&is=68e04aa9&hm=31fb9b21d449407463d6bea9ed73085a758847475c9afd9be861b2dd8d754bd0&" />

  <h1>Bolana</h1>

  <p>
    <strong>Unified Cross-Chain Program Framework</strong>
  </p>

  <p>
    <a href="https://github.com/bolana/bolana/actions"><img alt="Build Status" src="https://cdn.discordapp.com/attachments/1415994670793756793/1423795254091452626/ChatGPT_Image_Oct_3_2025_11_08_11_PM.png?ex=68e19c29&is=68e04aa9&hm=31fb9b21d449407463d6bea9ed73085a758847475c9afd9be861b2dd8d754bd0&" /></a>
    <a href="https://bolana-lang.com"><img alt="Tutorials" src="https://img.shields.io/badge/docs-tutorials-blueviolet" /></a>
    <a href="https://discord.gg/bolana"><img alt="Discord Chat" src="https://img.shields.io/discord/889577356681945098?color=blueviolet" /></a>
    <a href="https://opensource.org/licenses/Apache-2.0"><img alt="License" src="https://img.shields.io/github/license/bolana/bolana?color=blueviolet" /></a>
  </p>
</div>

## The Vision

Bolana realizes Anatoly Yakovenko's vision of a truly unified blockchain ecosystem. By bridging Solana and BNB Chain, Bolana enables developers to create tokens and deploy programs across both networks using a single, unified API. Write once, deploy everywhere.

## What is Bolana?

Bolana is a groundbreaking framework that connects Solana and BNB Chain, providing developers with seamless tools for writing cross-chain programs and creating tokens on both networks simultaneously.

- **Unified API**: One codebase deploys to both Solana and BNB Chain
- **Cross-Chain Token Creation**: Create tokens on both networks with a single command
- **Rust eDSL**: Familiar Solana-style development experience
- **[IDL](https://en.wikipedia.org/wiki/Interface_description_language) specification**: Generate clients for both chains
- **TypeScript package**: Type-safe clients from IDL for multi-chain interaction
- **CLI and workspace management**: Complete cross-chain application development

Bolana is the first framework to truly unify Solana and BNB Chain development.

> [!NOTE]
> Bolana brings the best of both worlds: Solana's speed and BNB Chain's massive ecosystem. If you're familiar with Anchor, Truffle, or web3.js, you'll feel right at home with Bolana's unified approach to cross-chain development.

## Key Features

- **Single API, Dual Deployment**: Write your program once, deploy to both Solana and BNB Chain
- **Unified Token Standard**: Create tokens that work seamlessly across both networks
- **Cross-Chain State Management**: Synchronize state between Solana and BNB Chain
- **Developer Experience**: Familiar Anchor-like syntax with cross-chain superpowers

## Getting Started

For a quickstart guide and in-depth tutorials, see the [Bolana book](https://book.bolana-lang.com) and the [Bolana documentation](https://bolana-lang.com).

To jump straight to examples, go [here](https://github.com/bolana/bolana/tree/master/examples). For the latest Rust and TypeScript API documentation, see [docs.rs](https://docs.rs/bolana-lang) and the [typedoc](https://www.bolana-lang.com/docs/clients/typescript).

## Packages

| Package                 | Description                                              | Version                                                                                                                          | Docs                                                                                                            |
| :---------------------- | :------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------- |
| `bolana-lang`           | Rust primitives for writing cross-chain programs         | [![Crates.io](https://img.shields.io/crates/v/bolana-lang?color=blue)](https://crates.io/crates/bolana-lang)                     | [![Docs.rs](https://docs.rs/bolana-lang/badge.svg)](https://docs.rs/bolana-lang)                                |
| `bolana-spl`            | CPI clients for SPL and BEP programs                     | [![crates](https://img.shields.io/crates/v/bolana-spl?color=blue)](https://crates.io/crates/bolana-spl)                          | [![Docs.rs](https://docs.rs/bolana-spl/badge.svg)](https://docs.rs/bolana-spl)                                  |
| `bolana-client`         | Rust client for Bolana cross-chain programs              | [![crates](https://img.shields.io/crates/v/bolana-client?color=blue)](https://crates.io/crates/bolana-client)                    | [![Docs.rs](https://docs.rs/bolana-client/badge.svg)](https://docs.rs/bolana-client)                            |
| `@bolana/sdk`           | TypeScript client for Bolana programs                    | [![npm](https://img.shields.io/npm/v/@bolana/sdk.svg?color=blue)](https://www.npmjs.com/package/@bolana/sdk)                     | [![Docs](https://img.shields.io/badge/docs-typedoc-blue)](https://bolana.github.io/bolana/ts/index.html)        |
| `@bolana/cli`           | CLI to support building and managing cross-chain apps    | [![npm](https://img.shields.io/npm/v/@bolana/cli.svg?color=blue)](https://www.npmjs.com/package/@bolana/cli)                     | [![Docs](https://img.shields.io/badge/docs-typedoc-blue)](https://bolana.github.io/bolana/cli/commands.html)    |

## Note

- **Bolana is in active development, so all APIs are subject to change.**
- **This code is unaudited. Use at your own risk.**

## Examples

Here's a cross-chain counter program that deploys to both Solana and BNB Chain, where only the designated `authority` can increment the count on both networks:

```rust
use bolana_lang::prelude::*;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");

#[program]
mod counter {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>, start: u64) -> Result<()> {
        let counter = &mut ctx.accounts.counter;
        counter.authority = *ctx.accounts.authority.key;
        counter.count = start;
        Ok(())
    }

    pub fn increment(ctx: Context<Increment>) -> Result<()> {
        let counter = &mut ctx.accounts.counter;
        counter.count += 1;
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Initialize<'info> {
    #[account(init, payer = authority, space = 48)]
    pub counter: Account<'info, Counter>,
    pub authority: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct Increment<'info> {
    #[account(mut, has_one = authority)]
    pub counter: Account<'info, Counter>,
    pub authority: Signer<'info>,
}

#[account]
pub struct Counter {
    pub authority: Pubkey,
    pub count: u64,
}
```

### Creating Cross-Chain Tokens

```bash
# Create a token on both Solana and BNB Chain with one command
bolana token create --name "MyToken" --symbol "MTK" --networks solana,bnb

# Deploy to both chains
bolana deploy --target all
```

For more, see the [examples](https://github.com/bolana/bolana/tree/master/examples)
and [tests](https://github.com/bolana/bolana/tree/master/tests) directories.

## Architecture

Bolana uses a unified runtime that translates your program logic into native operations for both Solana and BNB Chain. The framework handles:

- **Cross-chain account management**: Seamless state synchronization
- **Token bridging**: Automatic token creation and management across both chains
- **Transaction routing**: Intelligent routing to the appropriate network
- **Unified wallet integration**: Single wallet interface for both chains

## Roadmap

- [ ] Enhanced cross-chain messaging
- [ ] Native DEX integration
- [ ] Advanced bridge mechanics
- [ ] Support for additional chains
- [ ] Cross-chain NFT standards

## License

Bolana is licensed under [Apache 2.0](./LICENSE).

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in Bolana by you, as defined in the Apache-2.0 license, shall be
licensed as above, without any additional terms or conditions.

## Contribution

Thank you for your interest in contributing to Bolana!
Please see the [CONTRIBUTING.md](./CONTRIBUTING.md) to learn how.

## The Future is Cross-Chain

Bolana represents the future that Anatoly envisioned: a world where blockchain networks work together seamlessly, where developers aren't constrained by chain boundaries, and where users experience the best of multiple ecosystems through a single, unified interface.

### Thanks ❤️

<div align="center">
  <a href="https://github.com/bolana/bolana/graphs/contributors">
    <img src="https://contrib.rocks/image?repo=bolana/bolana" width="100%" />
  </a>
</div>



