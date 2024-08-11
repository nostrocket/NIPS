# Products
#### Kind: 1908

#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|name of product|`["name", <name of product>]`|
|SHOULD|description of product|`["description", <markdown>]`|
|SHOULD|main image to be displayed when rendering the product|`["cover", <image url, blossom, etc>]`|
|SHOULD|Rocket|`["a", 31108:<creator pubkey>:<rocket d tag>]`|

#### Product Groups
To include multiple products in the same group (for example different sizes for the same tshirt) put the product name in square brackets, and the option outside of the brackets. For example:

 `[Separation of Business and State Shirt] SIZE: S`