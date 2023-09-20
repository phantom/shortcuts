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
### 

- For use of Shortcuts we request that all partners follow the following guidelines
    
    ### Your Shortcuts fall into one of the following categories
    
    - Stake
    - Socials (Instagram, Twitter/X, Discord, Telegram)
    - Message feature
    - Mint
    - Tip
    - Vote
    
    **Note**: *If you’d like to do something outside these bounds, please share your plans with @Brian Friel or @Noah Hein so we can discuss your plans with you. We are fine with whatever links you choose but want to be sure you are set up with everything you need to make Shortcuts successful for you.* 
    
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
    
    ![Screenshot 2023-09-07 at 1.50.53 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/d533cfd5-ac13-41a6-8de4-92357d343c41/28036392-9ca5-4a55-a2b2-6b9166e4e288/Screenshot_2023-09-07_at_1.50.53_PM.png)
    
    ![Screenshot 2023-09-07 at 1.50.44 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/d533cfd5-ac13-41a6-8de4-92357d343c41/3afe188c-dd03-4d46-bb01-b661474cc4f0/Screenshot_2023-09-07_at_1.50.44_PM.png)
    
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
    
    🗒️ We respect different paths in addition to the domain. So you can have `[example.com/some-number/shortcuts.json](http://example.com/{artist}/shortcuts.json)` as well. As long as the NFT has the corresponding `external_url`, we will respect it.
    
    

Builders are not limited to static URLs. There are placeholder variables that can be replaced by the wallet providers. These variables include `{{collectionId}}`, `{{tokenId}}`, and `{ownerAddress}`. Please let @Noah Hein know if you want us to expose other variables.

