---
layout: post
categories: themes liquid
---

When we want to show [Add to cart][add-to-cart] button than we need to find
`variant.id`.  Product can have two types of options, for example: color (which
can take some values from: red, blue, green) and size (some value from: L, XL).
Total number of combinations, which is multiplication of number of option
values, in this example 3 x 2 = 6. Number of variants could be less than total
number of combinations (for example there is no green XL). Also variant could be
unavailable (out of stock, sold out).

Since we need `variant.id` when we are adding to the cart, on Shopify product
page we usually show all options (as radio buttons or dropdown select) and use
javascript to select specific variant in
[_getVariantFromOptions](_getVariantFromOptions) javascript function. Notice
that when you disable javascript you can see that dropdown with variants appear
(dropdown is hidden when javascript is enabled).

If we want to enable user to *add to cart* in one click **for each option** than
we need to iterate all product variants.  When we want to group variants (for
example in case of two types of options, color and size, when we want to
organize them in a grid) we need to filter variants based on option value. For
example we show *red L*, *red XL*, new line, *blue L*, *blue XL*, new line,
*green L*, *green XL*.
We can filter array using `product.variants | where: "option1", "red"`. Here is
complete code:

```
# sections/product-template.liquid
{ % assign first_option_with_values = product.options_with_values.first %}
{ % for value_of_first_option_with_values in first_option_with_values.values %}
  { % assign row_of_variants = product.variants | where: "option1", value_of_first_option_with_values %}
  { % for variant in row_of_variants %}
    <form action="/cart/add" method="post">
      <input type="hidden" name="id" value="{{ variant.id }}">
      <button type="submit">{{ variant.options | join }}</button>
    </form>
  { % endfor %}
  { % unless forloop.last %}
    <hr>
  { % endunless %}
{ % endfor %}
```

Here is [demo](link_to_demo) and [source](link_to_source) for example where we
show a button for each variant.

[add-to-cart]: https://shopify.dev/docs/themes/ajax-api/reference/cart#post-cart-add-js
[_getVariantFromOptions]: https://github.com/trkin/shopify-themes/blob/f0a3453c1feba09c2ca3a21c4db87f75b66eb97c/assets/theme.js#L722
[link_to_demo]: https://bootstrap-business-casual-theme.myshopify.com/products/morning-coffee
[link_to_source]: https://github.com/trkin/free-bootstrap-business-casual-shopify-theme/blob/master/sections/product-template.liquid#L47
