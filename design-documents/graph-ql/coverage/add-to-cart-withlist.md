# Statements 
* we have to add operations/mutations per use case not per entity/product type.
* we may have as many operations/mutations as many use cases we have.

# Definition
* Product may have options and buyer inputs.
* Options are sets of predefined values that a buyer can add to cart/wishlist in addition to a product.
* Some of the options can be required by its nature but not by API signature.
* Set of these options is predefined by Magento and consist of customizable, downloadable, gift card amounts.
* Bundle Dynamic, grouped and configurable product are not a "real" products,
both of them are templates which helps a merchant select a set of simple products predefined by a merchant.
* buyer input is an input that buyer can specify within the scope of a specific product.
As usual, this is a broad definition from file to the money amount, description of buyer input is out of the scope of this document.
* Options and buyer inputs are optional by the API signature.
* Buyer can add not configured item to the wishlist
* The differences in payloads between wishlist and cart are:
  * wishlist does not have a quantity.
  * wishlist has all fields as optional.
* From checkout/wishlist standpoint here not differences between bundle fixed and simple with options.
The difference happens only at the backend, when we have to manage inventory.

# Assumptions
* All options values can be uniquely in a scope of a Magento instance.
* variant can be described as serialization of `/product:sku/option:id/variant:id`

# Scenarios/Operations


```json
[
  {
    "sku": "sku",
    "qty": 1,
    "options" : [
      "variant1",
      "variant2"
    ],
    "buyerInput": "TBD not in the scope of this document, we can use an input format that we have right now which is just a string" 
  }
]
```
Supports simple, virtual, downloadable, giftcard, bundle fixed
Note, bundle fixed acts exactly as simple products with customizable options so that it can be added to cart in the same manner.
The main difference between the complex and simple product is a possibility to specify qty for an option.  

```json
[
  {
    "sku": "sku",
    "parent": "parent-sku",
    "qty": 1,
    "options" : [
      "variant1",
      "variant2"
    ],
    "buyerInput": "TBD not in the scope of this document, we can use an input format that we have right now which is just a string"
  }
]
```
Supports configurable and bundle dynamic, (bundle items will be aggregated into single line items per bundle SKU per mutation call)
If parent is an optional field then this API supports in addition simple, virtual, downloadable, giftcard.

# GraphQL

```graphql
input WishlistProduct
{
    sku: String
    parent: String,
    options: [String!]
    buyerInput: [String!]
}

mutation addProductToCart($cartId :String, $products: [CartProduct!]!) : Cart
mutation addProductToWishlist($wishlistId :String, $products: [WishlistProduct!]!) : Wishlist
```