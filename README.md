# Shortcuts

## Table of Contents

- [Shortcuts](#shortcuts)
  - [Introduction](#introduction)
  - [Specification](#specification)
    - [Schema object](#schema-object)
    - [Shortcut object](#shortcut-object)
  - [Implementation](#implementation)
    - [Guide For Token Creators](#guide-for-token-creators)
    - [Tips for hosting shortcuts](#tips-for-hosting-shortcuts)
  - [Security Considerations](#security-considerations)
    - [Recommendations for Implementing Platforms (Wallets/Marketplaces)](#recommendations-for-implementing-platforms-walletsmarketplaces)
    - [Phantom Implementation](#phantom-implementation)
  - [Examples](#examples)
    - [Mobile](#mobile)
    - [Extension](#extension)
  - [Changelog](#changelog)

## Introduction

[Shortcuts](https://phantom.app/learn/blog/shortcuts) is a feature that enables both fungible and non-fungible tokens to surface a curated list of links to their holders. Wallets, and other platforms such as NFT marketplaces, can request shortcuts for a given token by querying the `external_url` field specified in the token's metadata and appending a `/shortcuts.json` path. Shortcuts are hosted and maintained by token creators themselves, and should adhere to the following specification.

[shortcuts](https://github.com/phantom/shortcuts/assets/60185486/ad5fa479-11b2-4c09-b727-74934d5d45a8)

## Specification

### Schema object

A representation of all possible shortcuts for a given project.

| Field     | Type   | Description                                             |
| --------- | ------ | ------------------------------------------------------- |
| version   | number | Version of the targeted schema. Current version is `2`. |
| shortcuts | array  | The list of shortcuts this project supports.            |

**Example**

```json
{
  "version": 2,
  "shortcuts": []
}
```

### Shortcut object

A shortcut object represents a single action that can be performed in a client such as Phantom. It has the following properties:

| Field                 | Type     | Description                                                                                                                                                                                                                                                                                                                          |
| --------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| label                 | string   | The suggested text to display on the link                                                                                                                                                                                                                                                                                            |
| uri                   | string   | URI pointing to the destination of the shortcut                                                                                                                                                                                                                                                                                      |
| type                  | string   | (Optional) The type of token this shortcut applies to. Possible values are `fungible` or `collectible`. If omitted, the token will be assumed to be a `collectible`.                                                                                                                                                                 |
| icon                  | string   | (Optional) The suggested icon to display on the link. The following options are available: `vote`, `vote-2`, `stake`, `stake-2`, `view`, `chat`, `tip`, `mint`, `mint-2`, `discord`, `twitter`, `x`, `instagram`, `telegram`, `leaderboard`, `gaming`, `gaming-2`, `generic-link`, `generic-add`                                     |
| prefersExternalTarget | boolean  | (Optional) Whether the shortcut prefers to be opened outside of the client (e.g. Outside of Phantom's in-app browser). Defaults to `false`                                                                                                                                                                                           |
| preferredPresentation | string   | (Optional) How the shortcut prefers to be displayed. Possible values are `default` and `immerse`. The platform would choose how that translates to their UX. Defaults to `immerse`                                                                                                                                                   |
| limitToCollections    | string[] | (Optional) A list of collection addresses that should display this shortcut. If provided, the client should only show the shortcut on collections that are in this array. Other collections that share the same `external_url` will not show this shortcut. Addresses should be provided as strings. Defaults to an empty array `[]` |
| platform              | string   | (Optional) Indicates to the client that this shortcut should only be displayed for the specified platform. Possible options are `desktop`, `mobile`, and `all`. Defaults to `all`.                                                                                                                                                   |

When designing shortcuts, creators are not limited to static URLs. Instead, creators can specify placeholder variables that can be replaced by a given client. These variables include `{{collectionId}}`, `{{tokenId}}`, and `{{ownerAddress}}`. If you are interested in using more placeholder variables, please let us know by opening an issue on this repository.

**Example**

```json
{
  "label": "Redeem",
  "uri": "https://phantom.app/redeem/{{tokenId}}",
  "type": "collectible",
  "prefersExternalTarget": false,
  "icon": "mint"
}
```

In this example, `{{tokenId}}` will be replaced with the on-chain identifier of the token displaying this shortcut.

## Implementation

### Guide For Token Creators

For use of Shortcuts we request that all creators adhere to the following guidelines:

#### Your Shortcuts fall into one of the following categories

- Staking
- Socials (Instagram, Twitter/X, Discord, Telegram)
- Messaging features
- Minting
- Tipping
- Voting
- Gaming

**Note**: _If you’d like to do something outside these guidelines, please reach out to us. We are open to evolving these guidelines overtime and want to make sure that we can make Shortcuts successful for your project._

#### Use one of the pre-approved icons

1. `vote`
2. `vote-2`
3. `stake`
4. `stake-2`
5. `view`
6. `chat`
7. `tip`
8. `mint`
9. `mint-2`
10. `discord`
11. `twitter`
12. `x`
13. `instagram`
14. `telegram`
15. `leaderboard`
16. `gaming`
17. `gaming-2`
18. `generic-link`
19. `generic-add`

![Wallet Shortcut Icons (Social)](https://github.com/phantom/shortcuts/assets/60185486/74f9ab69-8aa9-40ef-9c96-2052a0b94a2e)
![Wallet Shortcut Icons (Actions)](https://github.com/phantom/shortcuts/assets/60185486/86736fb1-a323-4a6b-b1ae-a19d5717ca1e)

**Example**

```json
{
  "icon": "vote"
}
```

#### Use copy with the following guidelines

- All Shortcuts should start with a verb to solidify the action you want the user to take
- Try to use 2-3 word phrases
  - Ensure that labels never wrap to a second line
- If the Shortcut isn’t an action (e.g. “Stake” or “Mint”) you can always use “View \_\_\_\_” that way it still starts with a verb

**Examples**

- “Join [project name] Telegram”
- "Lend on [project name]"
- “Mint NFT”
- “Tip artist”

#### Tips for hosting shortcuts

Projects that want to show shortcuts to their users should ensure that their token(s) have an `external_url` present in their metadata. If they do, wallets will be able to fetch the `external_url` + `/shortcuts.json` when a user clicks into the token's detail page.

**Example**

- NFT metadata’s `external_url` = `https://example.com`
- Wallet will request `https://example.com/shortcuts.json`
- Response from `https://example.com/shortcuts.json` looks like:

  ```json
  {
    "version": 2,
    "shortcuts": [
      {
        "label": "Tip Artist",
        "uri": "solana:ART5dr4bDic2sQVZoFheEmUxwQq5VGSx9he7JxHcXNQD?label=Drip+Thanks",
        "platform": "mobile"
      },
      {
        "label": "View Artist Page",
        "uri": "https://drip.haus/degenpoet"
      },
      {
        "label": "View Vault",
        "uri": "https://drip.haus/vault"
      }
    ]
  }
  ```

In the above example, Phantom would render shortcuts like this 👇

[shortcuts-with-path-external-url](https://github.com/phantom/shortcuts/assets/60185486/718c0779-49f2-411f-a5eb-562edcc9a8da)

💡 **Tip:** Phantom will respect different paths in addition to the domain. For example, you can have `example.com/some-identifier/shortcuts.json`. As long as the NFT has that path in its corresponding `external_url`, Phantom will respect it.

## Security Considerations

The utilization of the `external_url` property, as stipulated in current token standards, is recognized by Phantom as potentially hazardous. To ensure the safety of users, Phantom presents a cautionary dialogue when users engage with this property. To bolster security, it's essential to enact several preventive measures.

### Recommendations for Implementing Platforms (Wallets/Marketplaces)

1. **Trusted Tokens**: Only query shortcuts for tokens that are neither flagged nor marked as spam. Consider prioritizing tokens that have undergone verification.
2. **Allowlist**: Introduce an `allowlist.json` containing a pre-defined list of reputable sources.
3. **Third-Party Verification**: Align with verification mechanisms from platforms like Magic Eden and OpenSea. Consider only showing shortcuts on tokens verified by these entities.
4. **User Permissions**: Consider surfacing an array of user settings such as:

   - Allow shortcuts only from partner tokens.
   - Allow shortcuts from all tokens.
   - Disallow all shortcuts.

   Alternatively, provide users with a prompt on every collection page, letting them decide whether to enable shortcuts. This approach offers more granular permissions compared to a universal setting.

5. **External Link Restrictions**: Only links marked with `prefersExternalTarget` should be permitted to connect to destinations outside of the `external_url`.
6. **Proxy Requests**: To prevent the unintentional exposure of user IP addresses, all requests should be routed through a proxy. Such a server can also cache responses, mitigating the risk of unintentionally overburdening project servers.
   <img src='https://github.com/phantom/shortcuts/assets/60185486/cf0fdea9-ea08-44d6-a26e-abcff94f54ca' alt='phantom privacy proxy diagram'/>

### Phantom Implementation

In light of the aforementioned security concerns, Phantom has instituted a rigorous vetting process for all projects. Currently, each project is manually assessed to ensure its alignment with our security standards.

For projects interested in this integration, please complete our [Advanced Feature Interest Form](https://surveymonkey.com/r/phantomfeatures).

## Examples

Composition is the true power of Shortcuts. These are some examples to get you started.

### Mobile

#### External links (Socials)

```json
{
  "label": "View on instagram",
  "uri": "https://instagram.com/famousfoxfed",
  "prefersExternalTarget": true
}
```

To enable experiences that require the user to go into another app or website we recommend using `prefersExternalTarget`.

[external-flow](https://github.com/phantom/shortcuts/assets/60185486/1d625533-fa34-4538-b436-0fdf5e3d7030)

#### Immerse experiences (Gaming)

```json
{
  "label": "Play now",
  "uri": "https://shanksy.xyz/play/{{tokenId}}",
  "preferredPresentation": "immerse"
}
```

By setting a shortcuts's `preferredPresentation` to `immerse`, developers can take full advantage of the extra available and tailor unique experiences for their users.

[Immerse Experience](https://github.com/phantom/shortcuts/assets/60185486/50846eaf-7c6a-4afb-a52d-e4f0b7c3c658)

#### Internal dApp features (Famous Foxes, Staking)

```json
{
  "label": "Stake",
  "uri": "https://famousfoxes.com/stake/{{tokenId}}"
}
```

Many use cases involve redirecting users to specific features in a dApp. By providing placeholders, platforms can fill in the required information for the dApp to perform their actions.

In this example `{{tokenID}}` will be replaced with the collectible id

[internal-dapp](https://github.com/phantom/shortcuts/assets/60185486/7ec68228-71c5-474b-bae2-77943bc437d0)

#### Link back into the wallet itself (Solana pay, arbitrary transactions)

```json
{
  "label": "Tip me!",
  "uri": "solana:https%3A%2F%2Fsolana-pay-marty-mcflai.vercel.app%2Fapi%3Frecipient%3DEqaavRGuaN4myvgvqs8fMecQ7Y1vGgQUjgZpLsJWG7Nn%26amount%3D0.001%26label%3DKoffii%26reference%3DEqaavRGuaN4myvgvqs8fMecQ7Y1vGgQUjgZpLsJWG7Nn",
  "prefersExternalTarget": true,
  "platform": "mobile"
}
```

For a slew of more advanced examples, you can target features that wallets already support. Developers can interact with any property the wallet exposes.
To prevent broken experiences, an optional platform modifier can be supplied to limit where the shortcut should be displayed.

[Link to Solana Pay](https://github.com/phantom/shortcuts/assets/60185486/48a255bf-5d82-45a8-9e41-4bf82f416edc)

#### Cross-app integrations (Dialect, Communications)

```json
{
  "label": "Join chat",
  "uri": "https://app.dialect.to/join?id={{tokenId}}",
  "prefersExternalTarget": true,
  "platform": "mobile"
}
```

Similar to the Solana pay examples, any Universal Link can be used to generated shortcuts.

Limited to only mobile platforms as Dialect is only supported on mobile as well.

[Cross-app integrations](https://github.com/phantom/shortcuts/assets/60185486/199b4ce6-3097-44e5-835c-efdb4111d0e1)

### Extension

#### Direct users to partners

Projects can partner with providers to do things like loans and provide quick access for users to leverage the partnership

[sharky.mp4](https://github.com/phantom/shortcuts/assets/60185486/575675db-350b-49e4-9aca-42772bc46530)

## Changelog

#### v2

Released February 2024.

- Add `type` field that supports `fungible` and `collectible` tokens

#### v1

Released September 2023.

- Initial specification
