---
gid: 80d51c7e-fafe-11eb-9a03-0242ac130003
title: ProductProvider
description: The ProductProvider component sets up a context with state that tracks the selected variant and options.
hidden: true
---

> Note:
> `ProductProvider` is only available as part of the [Hydrogen UI](https://shopify.dev/custom-storefronts/hydrogen/alternate-frameworks) package, which is in beta. If you’re building with Hydrogen, then use [`ProductOptionsProvider`](https://shopify.dev/api/hydrogen/components/product-variant/productoptionsprovider)

The `ProductProvider` component sets up a context with state that tracks the selected variant and options. Descendants of this component can use the [`useProductOptions`](https://shopify.dev/api/hydrogen/hooks/product-variant/useproductoptions) hook.

## Example code

```tsx
import {ProductProvider, gql} from '@shopify/hydrogen-react';
import {gql} from '@shopify/hydrogen';

const QUERY = gql`
  query product($handle: String!, $includeReferenceMetafieldDetails: Boolean!) {
    product: product(handle: $handle) {
      compareAtPriceRange {
        maxVariantPrice {
          currencyCode
          amount
        }
        minVariantPrice {
          currencyCode
          amount
        }
      }
      descriptionHtml
      handle
      id
      media(first: 10) {
        nodes {
          ... on MediaImage {
            mediaContentType
            image {
              id
              url
              altText
              width
              height
            }
          }
          ... on Video {
            mediaContentType
            id
            previewImage {
              url
            }
            sources {
              mimeType
              url
            }
          }
          ... on ExternalVideo {
            mediaContentType
            id
            embedUrl
            host
          }
          ... on Model3d {
            mediaContentType
            id
            alt
            mediaContentType
            previewImage {
              url
            }
            sources {
              url
            }
          }
        }
      }
      metafields(first: 10) {
        nodes {
          id
          type
          namespace
          key
          value
          createdAt
          updatedAt
          description
          reference @include(if: $includeReferenceMetafieldDetails) {
            __typename
            ... on MediaImage {
              id
              mediaContentType
              image {
                id
                url
                altText
                width
                height
              }
            }
          }
        }
      }
      priceRange {
        maxVariantPrice {
          currencyCode
          amount
        }
        minVariantPrice {
          currencyCode
          amount
        }
      }
      title
      variants(first: 250) {
        nodes {
          id
          title
          availableForSale
          image {
            id
            url
            altText
            width
            height
          }
          unitPriceMeasurement {
            measuredType
            quantityUnit
            quantityValue
            referenceUnit
            referenceValue
          }
          unitPrice {
            currencyCode
            amount
          }
          priceV2 {
            currencyCode
            amount
          }
          compareAtPriceV2 {
            currencyCode
            amount
          }
          selectedOptions {
            name
            value
          }
          metafields(first: 10) {
            nodes {
              id
              type
              namespace
              key
              value
              createdAt
              updatedAt
              description
              reference @include(if: $includeReferenceMetafieldDetails) {
                __typename
                ... on MediaImage {
                  id
                  mediaContentType
                  image {
                    id
                    url
                    altText
                    width
                    height
                  }
                }
              }
            }
          }
          sellingPlanAllocations(
            first: 10
          ) {
            nodes {
              priceAdjustments {
                compareAtPrice {
                  currencyCode
                  amount
                }
                perDeliveryPrice {
                  currencyCode
                  amount
                }
                price {
                  currencyCode
                  amount
                }
                unitPrice {
                  currencyCode
                  amount
                }
              }
              sellingPlan {
                id
                description
                name
                options {
                  name
                  value
                }
                priceAdjustments {
                  orderCount
                  adjustmentValue {
                    ... on SellingPlanFixedAmountPriceAdjustment {
                      adjustmentAmount {
                        currencyCode
                        amount
                      }
                    }
                    ... on SellingPlanFixedPriceAdjustment {
                      price {
                        currencyCode
                        amount
                      }
                    }
                    ... on SellingPlanPercentagePriceAdjustment {
                      adjustmentPercentage
                    }
                  }
                }
                recurringDeliveries
              }
            }
          }
        }
      }
      sellingPlanGroups(first: 10) {
        nodes {
          sellingPlans(first: 10) {
            nodes {
              id
              description
              name
              options {
                name
                value
              }
              priceAdjustments {
                orderCount
                adjustmentValue {
                  ... on SellingPlanFixedAmountPriceAdjustment {
                    adjustmentAmount {
                      currencyCode
                      amount
                    }
                  }
                  ... on SellingPlanFixedPriceAdjustment {
                    price {
                      currencyCode
                      amount
                    }
                  }
                  ... on SellingPlanPercentagePriceAdjustment {
                    adjustmentPercentage
                  }
                }
              }
              recurringDeliveries
            }
          }
          appName
          name
          options {
            name
            values
          }
        }
      }
    }
  }
`;

export function Product() {
  const {data} = useShopQuery({query: QUERY, variables: { handle, includeReferenceMetafieldDetails }});

  return (
    <ProductProvider data={data.product} initialVariantId="some-id">{/* Your JSX */}</ProductProvider>
  );
}
```

## Props

| Name              | Type                                                                              | Description                                                                                                                                                                                                     |
| ----------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| children          | <code>ReactNode</code>                                                            | A `ReactNode` element.                                                                                                                                                                                          |
| data              | <code>PartialDeep&#60;ProductType&#62;</code>                                     | An object with fields that correspond to the Storefront API's [`Product` object](/api/storefront/reference/products/product).                                                                  |
| initialVariantId? | <code>Parameters&#60;typeof useProductOption&#62;['0']['initialvariantid']</code> | The initially selected variant. <br></br>The following logic applies to `initialVariantId`:<ul><li>If `initialVariantId` is provided, then it's used, even if the variant is out of stock.</li><li>If `initialVariantId` is provided, but is `null`, then no variant is used.</li><li>If nothing is passed to `initialVariantId`, and you're in a `ProductProvider` component, then `selectedVariant.id` is used.</li><li>If nothing is passed to `initialVariantId` and you're not in a `ProductProvider` component, then the first available or in-stock variant is used.</li><li>If nothing is passed to `initialVariantId`, you're not in a `ProductProvider` component, and no variants are in stock, then the first variant is used.</li></ul> |

## Component type

The `ProductProvider` component is a client component, which means that it renders on the client. For more information about component types, refer to [React Server Components](https://shopify.dev/custom-storefronts/hydrogen/framework/react-server-components).

## Related components

- [`ProductPrice`](https://shopify.dev/api/hydrogen/components/product-variant/productprice)
- [`ProductOptionsProvider`](https://shopify.dev/api/hydrogen/components/product-variant/productoptionsprovider)

## Related hooks

- [`useProductOptions`](https://shopify.dev/api/hydrogen/hooks/product-variant/useproductoptions)
