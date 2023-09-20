# Table of Contents
- [Wallet Shortcuts](#wallet-shortcuts)
  - [Schema object](#schema-object)
  - [Shortcut object](#shortcut-object)
  - [Implementation](#implementation)
    - [Guide For Builders](#guide-for-builders)
    - [For Platforms (Wallets/Marketplaces)](#for-platforms-walletsmarketplaces)
  - [Security Considerations](#security-considerations)
    - [Recommendations for Implementing Platforms](#recommendations-for-implementing-platforms)
    - [Phantom Implementation](#phantom-implementation)
  - [Examples](#examples)
    - [Mobile](#mobile)
      - [External links (Socials)](#external-links-socials)
      - [Immerse experiences (Gaming)](#immerse-experiences-gaming)
      - [Internal dApp features (Famous Foxes, Staking)](#internal-dapp-features-famous-foxes-staking)
      - [Link back into the wallet itself (Solana pay, arbitrary transactions)](#link-back-into-the-wallet-itself-solana-pay-arbitrary-transactions)
      - [Cross-app integrations (Dialect, Communications)](#cross-app-integrations-dialect-communications)
    - [Extension](#extension)

# Wallet Shortcuts:

Wallet Shortcuts is a specification for how a client should request resources be fetched, and how a server should respond to those requests. Wallet Shortcuts can be easily extended with composition to fully take advantage of a client feature set or providing new functionality.

Wallet Shortcuts requires use of the application/json media type for exchanging data. The full specification can be seen below.

## Schema object

A representation of the possible actions provided by the server response.

| Field | Type | Description |
| --- | --- | --- |
| version | number | Version of the targeted schema. |
| shortcuts | array | The list of shortcuts this project supports. |

- **Example**

    ```json
    {
        "version": 1,
        "shortcuts": []
    }
    ```

## Shortcut object

A shortcut object represents a single action that can be performed in a wallet client. It has the following properties:

| Field | Type | Description |
| --- | --- | --- |
| label | string | The suggested text to display on the link |
| uri | string | URI pointing to the destination of the shortcut |
| icon | string | (Optional) The suggested icon to display on the link. The following options are available: vote, vote-2, stake, stake-2, view, chat, tip, mint, mint-2, discord, twitter, x, instagram, telegram, leaderboard, gaming, gaming-2, generic-link, generic-add |
| prefersExternalTarget | boolean | (Optional) Whether the shortcut prefers to be opened outside of the wallet. Defaults to false |
| preferredPresentation | string | (Optional) How the shortcut prefers to be displayed. Possible values are default and immerse. The wallets would choose how that translate to their UX. Defaults to immerse |
| limitToCollections | string[] | (Optional) A list of collection addresses that should display this shortcut. If provided, the wallet should only show the shortcut on collections that are in this array. Other collections that share the same external_url will not show this shortcut. Addresses should be provided as strings. Defaults to an empty array [] |
| platform | string | (Optional) Indicates to the wallet that this shortcut should only be displayed for the specified platform. Possible options are desktop, mobile, and all. Defaults to “all”. |

- **Example**

    ```json
    {
        label: "Redeem",
        uri: "https://phantom.app/redeem/{{tokenId}}",
        prefersExternalTarget: false,
        icon: "mint"
    }
    ```
In this example, {{tokenId}} will be replaced with the on-chain identifier of the token displaying this shortcut

## Implementation

### Guide For Builders

- For use of Shortcuts we request that all partners follow the following guidelines
    
    ### Your Shortcuts fall into one of the following categories
    
    - Stake
    - Socials (Instagram, Twitter/X, Discord, Telegram)
    - Message feature
    - Mint
    - Tip
    - Vote
    
    **Note**: *If you’d like to do something outside these bounds, please share your plans with us so we can discuss your plans with you. We are fine with whatever links you choose but want to be sure you are set up with everything you need to make Shortcuts successful for you.* 
    
    ### Use one of the pre-approved icons
    
    [**Figma file**](https://www.figma.com/file/qAIYrXDjrrFADZmT6iTEU0/Shortcuts?type=design&mode=design)
    
    { icon: “name” }
    
    1. vote
    2. vote-2
    3. stake
    4. stake-2
    5. view
    6. chat
    7. tip
    8. mint
    9. mint-2
    10. discord
    11. twitter
    12. x
    13. instagram
    14. telegram
    15. leaderboard
    16. gaming
    17. gaming-2
    18. generic-link
    19. generic-add
    
    ### Use copy with the following guidelines
    
    1. All Shortcuts should start with a verb to solidify the action you want the user to take
    2. Try to use 2-3 word phrases 
        1. Ensure the text never wraps to a second line 
    3. If the Shortcut isn’t an action (like “Stake” or “Mint”) you can always use “View ____” that way it still starts with a verb
    4. **Examples** 
        1. “Join [project name] Telegram”
        2. “Mint NFT”
        3. “View mint page”
        4. “Tip artist”

For projects that want to show shortcuts to their users, you will need to start by ensuring that your NFT(s) have an `external_url` present in their metadata. If they do, when a user clicks on an NFT in their collection, the wallet will fetch the `external_url` + `/shortcuts.json`.

- Example
    
    NFT metadata’s `external_url` = `https://example.com`
    wallet will request `https://example.com/shortcuts.json`
    response from `https://example.com/shortcuts.json` looks like 
    
    ```json
    {
      "version": 1,
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
    
    Phantom would render shortcuts like this 👇
    
    [shortcutsguidevid.mp4](https://prod-files-secure.s3.us-west-2.amazonaws.com/d533cfd5-ac13-41a6-8de4-92357d343c41/4ea3ebe6-c1d4-4529-bd87-a9dd666376d9/shortcutsguidevid.mp4)
    
    🗒️ We respect different paths in addition to the domain. So you can have `example.com/some-identifier/shortcuts.json` as well. As long as the NFT has the corresponding `external_url`, we will respect it.
    
    

Builders are not limited to static URLs. There are placeholder variables that can be replaced by the platform providers. These variables include `{{collectionId}}`, `{{tokenId}}`, and `{ownerAddress}`. Please let us know if you want us to expose other variables.

- Example

    In this example, {{tokenId}} will be replaced with the on-chain identifier of the token displaying this shortcut

    ```json
    {
      label: "Redeem",
      uri: "https://example.com/redeem/{{tokenId}}",
      prefersExternalTarget: false,
      icon: "mint",
    }
    ```

### For Platforms (Wallets/Marketplaces)
We recommend adding a privacy layer between the wallet and projects to obfuscate the user, the following flow describes Phantom’s implementation.
//// Phantom diagram here

## Security Considerations

The utilization of the `external_url` property, as stipulated in current token standards, is recognized by Phantom as potentially hazardous. To ensure the safety of users, Phantom presents a cautionary dialogue when users engage with this property. To bolster security, it's essential to enact several preventive measures.

### Recommendations for Implementing Platforms

1. **Trusted Collections**: Only query shortcuts for collections that are neither flagged nor marked as spam. Consider prioritizing collections that have undergone verification.
2. **Allowlist**: Introduce an `allowlist.json` containing a pre-defined list of reputable sources and initial launch collaborators.
3. **Third-Party Verification**: Align with verification mechanisms from platforms like Magic Eden and Open Sea. Only collections verified by these entities should be considered.
4. **User Permissions**: Propose three user settings:
    - Allow shortcuts only from partner collections.
    - Allow shortcuts from all collections.
    - Disallow all shortcuts.
   
   Alternatively, provide users with a prompt on every collection page, letting them decide whether to enable shortcuts. This approach offers more granular permissions compared to a universal setting.
5. **External Link Restrictions**: Only links marked with `prefers_external_target` should be permitted to connect to destinations outside of the `external_url`.
6. **Proxy Requests**: To prevent the unintentional exposure of user IP addresses, all requests should be routed through a proxy. Such a server can also cache responses, mitigating the risk of unintentionally overburdening project servers.

### Phantom Implementation

In light of the aforementioned security concerns, Phantom has instituted a rigorous vetting process for all projects. Currently, each project is manually assessed to ensure its alignment with our security standards.

For projects interested in this integration, please complete our survey for further consideration: [Phantom Features Survey](https://surveymonkey.com/r/phantomfeatures).


## Examples
### Mobile

#### External links (Socials)

```json
{
  "label": "View on instagram",
  "uri": "https://instagram.com/famousfoxfed",
  "prefersExternalTarget": true
}
```

To enable experiences that require the user to go into another app or website we recommend using **prefersExternalTarget**.

[external.mov](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/23e584b7-82e3-49ed-aecc-a8e2b46725f0/external.mov)

#### Immerse experiences (Gaming)

```json
{
  "label": "Play now",
  "uri": "https://shanksy.xyz/play/{{tokenId}}",
  "preferredPresentation": "immerse"
}
```

Taking full advantage of the extra available space. Developers could tailored unique experiences for their users.

[Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/192e332b-3345-4eec-bd99-79c25f70122b/Untitled.qt)

#### Internal dApp features (Famous Foxes, Staking)

```json
{
  "label": "Stake",
  "uri": "https://famousfoxes.com/stake/{{tokenId}}"
}
```

Potentially, most use cases are to redirect users to specific features in their dApps. By providing placeholders, wallets can fill in the required information for the dApp to perform their actions.

In this example `{{tokenID}}` will be replaced with the collectible id

[Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9cb99e0f-87ac-4198-a0c1-2b3b697bfce2/Untitled.qt)

#### Link back into the wallet itself (Solana pay, arbitrary transactions)

```json
{
  "label": "Tip me!",
  "uri": "solana:https%3A%2F%2Fsolana-pay-marty-mcflai.vercel.app%2Fapi%3Frecipient%3DEqaavRGuaN4myvgvqs8fMecQ7Y1vGgQUjgZpLsJWG7Nn%26amount%3D0.001%26label%3DKoffii%26reference%3DEqaavRGuaN4myvgvqs8fMecQ7Y1vGgQUjgZpLsJWG7Nn",
  "prefersExternalTarget": true,
  "platform": "mobile"
}
```

A more advanced of examples, by targeting features that the wallets already supports. Developers could interact with anything the wallet exposes.

- https://phantom.app/ul/send?source={{tokenId}}&amount=1
- https://phantom.app/ul/swap?source={{tokenId}}&destination=0x&amount=1
- etc

To prevent broken experiences an optional platform modifier can be supplied to limit where the shortcut should be displayed.

[Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a9ddf158-c598-4095-b9cb-e992142e568d/Untitled.mp4)

#### Cross-app integrations (Dialect, Communications)

```json
{
  "label": "Join chat",
  "uri": "https://app.dialect.to/join?id={{tokenId}}",
  "prefersExternalTarget": true,
  "platform": "mobile"
}
```

Similar to the Solana pay examples, any Universal Link could be used to generated shortcuts.

Limited to mobile platforms only as Dialect is only supported in mobile as well.

[RPReplay_Final1688678108.MP4](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1851aeee-79be-429d-b907-1c5110734ff8/RPReplay_Final1688678108.mp4)

### Extension

- **Seemless dApp Interaction**
    
    Prominent display on the collectable details and into a target dApp with Auto-Connect
    
    ```jsx
    {
      "label": "I'm feeling lucky!",
      "uri": "https://www.degencoinflip.com/deeplink?amount=0.05&side=T",
      "prefersExternalTarget": true,
      "isPrimary": true
    }
    ```
    
    [quick-flip.mp4](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1a6fa011-cc40-4031-ae47-f778e681311f/quick-flip.mp4)
    
- **Direct users to partners**
    
    Projects can partner with providers to do things like loans and provide quick access for users to leverage the partnership
    
    [sharky.mp4](/sharky.mp4)

