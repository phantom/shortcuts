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
| icon | string | (Optional) The suggested icon to display on the link. The following options are available:

vote, vote-2, stake, stake-2, view, chat, tip, mint, mint-2, discord, twitter, x, instagram, telegram, leaderboard, gaming, gaming-2, generic-link, generic-add |
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

[...]

