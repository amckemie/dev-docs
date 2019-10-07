# GraphQL Storefront API Overview

<div class="otp" id="no-index">

### On this Page

- [See it in Action](#see-it-in-action)
- [Accessing the GraphQL Playground](#accessing-the-graphql-playground)
- [Using the GraphQL Playground](#using-the-graphql-playground)
- [Authentication](#authentication)
- [Querying from a Stencil Theme](#querying-from-a-stencil-theme)
- [Resources](#resources)

</div>

BigCommerce's GraphQL Storefront API makes it possible to query storefront data from from within a [Stencil](https://devcenter-production.docs.stoplight.io/stencil-docs/getting-started/about-stencil) theme or remote site. This means information previously only available on the back-end via [Stencil's template logic](https://devcenter-production.docs.stoplight.io/stencil-docs/reference-docs/global-objects-and-properties) can now be accessed via front-end javascript. For example, with the Storefront API, it is possible to:

* Access product options, variations, and custom fields for any product from any page
* Request any product's images at any resolution
* Ask for customer details such as name, email address, and attributes (if logged in)
* Look up objects (e.g. categories or brands) by URL, and fetch their details
* Build front-end applications on top of a BigCommerce [Stencil](https://devcenter-production.docs.stoplight.io/stencil-docs/getting-started/about-stencil) theme or on a remote site

Additionally, by leveraging the power of [GraphQL](https://graphql.org/), data for multiple resources can be returned from a single API call, which simplifies integration and increases performance so that developers can focus on building delightful shopper experiences.

This article is a general overview of the capabilities and usage of BigCommerce's GraphQL Storefront API; it includes sections on authentication and how to access a store's GraphQL Playground. To see specific examples of how GraphQL can be used to query storefront data, see [GraphQL Storefront API Code Samples](https://developer.bigcommerce.com/api-docs/storefront/graphql-api/graphql-code-samples).

<div class="HubBlock--callout">
<div class="CalloutBlock--warning">
<div class="HubBlock-content">
    
<!-- theme: warning -->

### Note

> * GraphQL Storefront API is currently in open beta and only sandbox stores are being accepted into the program at this time
> * Only Stencil themes are supported at this time.

</div>
</div>
</div>

---

<a id="sectionId" class="devdocsAnchor"></a>

## See it in Action

To see the GraphQL storefront API in action, checkout the [Bootstrap + Vanilla JS Storefront API Example](https://bigcommerce.github.io/storefront-api-examples/html-bootstrap-vanillajs/) hosted on GitHub -- This example shows how a static HTML site can be used to render dynamic product information via the GraphQL Storefront API.

Simply open the link and click submit with the sample data in the form. To see the example page with your store's data, [create a Storefront API Token](https://developer.bigcommerce.com/api-reference/storefront/storefront-token-api/storefront-api-auth/createtoken) against your store and paste the token into the example form (be sure to create a token valid for this origin: `https://bigcommerce.github.io`).

For a full list of examples, see the [Storefront API Examples repo](https://github.com/bigcommerce/storefront-api-examples).

---

<a id="sectionId" class="devdocsAnchor"></a>

## Accessing the GraphQL Playground

To access the GraphQL Storefront API Playground<sup>1</sup> and documentation:

1. Log into a BigCommerce store enrolled in the beta
2. Navigate to **Advanced Settings** > **Storefront API Playground**<sup>2</sup>

The GraphQL Storefront API Playground will be opened:

![GraphQL Storefront API Playground](https://raw.githubusercontent.com/bigcommerce/dev-docs/master/assets/img/graphql-storefront-api-playground.png "GraphQL Storefront API Playground")

<div class="HubBlock--callout">
<div class="CalloutBlock--info">
<div class="HubBlock-content">
    
<!-- theme: info -->

### Note
> 1. GraphQL Playground is GraphQL IDE built on Electron. For more information, see [GraphQL Playground](https://electronjs.org/apps/graphql-playground) on [electrongjs.org](https://electronjs.org)

> 2. If the **Storefront API Playground** link is not visible, the store is not enrolled in the Beta program. To enroll, [contact support](https://support.bigcommerce.com/SubmitCase) (only sandbox stores are being accepted at this time).


</div> 
</div>
</div>

---

<a id="sectionId" class="devdocsAnchor"></a>

## Using the GraphQL Playground

To use the request runner, input queries on the left side and then click the play button. Query results will be displayed on the the right side:

![GraphQL Playground Query](https://raw.githubusercontent.com/bigcommerce/dev-docs/master/assets/img/graphql-storefront-api-playground2.png "GraphQL Playground Query")

Here's a sample Query to get you started:


```javascript
query MyFirstQuery {
  site {
    settings {
      storeName
    }
    products {
      edges {
        node {
          name
          sku
          prices {
            retailPrice {
              value
              currencyCode
            }
            price {
              value
              currencyCode
            }
          }
        }
      }
    }
  }
}
```

To explore the storefront GraphQL schema, checkout the Docs and Schema tabs on the right:

![GraphQL Playground Docs](https://raw.githubusercontent.com/bigcommerce/dev-docs/master/assets/img/graphql-playground-docs.png "GraphQL Playground Docs")

Click changelog in the top right to view a list of recent changes to the storefront API:

![GraphQL Playground Changelog](https://raw.githubusercontent.com/bigcommerce/dev-docs/master/assets/img/graphql-playground-changelog.png "GraphQL Playground Changelog")

<div class="HubBlock--callout">
<div class="CalloutBlock--info">
<div class="HubBlock-content">

<!-- theme: info -->

### Note
> * The changelog is updated with each deployment. 
> * Breaking changes are possible during the beta, so its recommended to keep an eye on the changelog

</div> 
</div>
</div>



---

<a id="sectionId" class="devdocsAnchor"></a>

## Authentication

GraphQL Storefront API requests are authenticated by passing a `Bearer` token in the `Authorization` header:

```bash
curl 'https://www.{bigcommerce_storefront_domain}.com/graphql'\
  # ... additional headers ...
  -H 'Authorization: Bearer simple_eyp4mnu1ox...3p2mnt0btlf6ku'\
  --data-binary '{"query":"..."}'\
  --compressed
```

<div class="HubBlock--callout">
<div class="CalloutBlock--info">
<div class="HubBlock-content">
<!-- theme: info -->

> Click **COPY CURL** in the GraphQL Playground to copy the curl command for last request made in the IDE

</div> 
</div>
</div>

Client code in BigCommerce Stencil themes can be passed a token at render time with the `{{ settings.storefront_api.token }}` handlebars object:

```html
<script>
fetch('/graphql', {
       method: 'POST',
       headers: {
           'Content-Type': 'application/json',
           'Authorization': 'Bearer {{ settings.storefront_api.token }}'
       },
       body: JSON.stringify({
           query: `
            query MyFirstQuery {
              ...
            }
  `
// ...
</script>
``` 

Tokens can also be generated using the [Storefront API Token endpoint](https://developer.bigcommerce.com/api-reference/storefront/storefront-token-api/storefront-api-auth/createtoken):

**`POST`** `https://{yourbigcommercedomain}/storefront/api-token`

```javascript
{
  "channel_id": {int_channel_id},            // ID of the corresponding channel (required)
  "expires_at": {double_unix_utc_timestamp}, // UTC date and time the token should expire (required)
  "allowed_cors_origins": [                  // array of allowed CORS origins
    "{allowed_remote_origin_1}",
    "{allowed_remote_origin_2}"
  ]
}
```

**Response:**

```json
{
  "token":"...eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NiJ9....",
  "meta": {
    // ...
  }
}
```

<div class="HubBlock--callout">
<div class="CalloutBlock--warning">
<div class="HubBlock-content">
<!-- theme: warning -->

### channel_id
> * `1` can be passed in for the `channel_id` for generating tokens for use on the storefront itself.
> * To get a `channel_id` for a remote site, first create a channel for the site. For more information on doing so, see [Create Channel](https://developer.bigcommerce.com/api-reference/cart-checkout/channels-listings-api/channels/createchannel) in the API Reference.

</div> 
</div>
</div>

---

<a id="sectionId" class="devdocsAnchor"></a>

## Querying from a Stencil Theme

GraphQL Storefront API calls can be made directly from within a Stencil theme or a from a script in [Storefront > Script Manager](https://support.bigcommerce.com/s/article/Using-Script-Manager).

Here's an an example request using the  `{{ settings.storefront_api.token }}` handlebars object and `fetch()`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

<script>
   fetch('/graphql', {
       method: 'POST',
       headers: {
           'Content-Type': 'application/json',
           'Authorization': 'Bearer {{ settings.storefront_api.token }}'
       },
       body: JSON.stringify({
           query: `
            query MyFirstQuery {
            site {
                settings {
                storeName
                }
                products {
                edges {
                    node {
                      name
                      sku
                      prices {
                        retailPrice {
                          value
                          currencyCode
                        }
                        price {
                          value
                          currencyCode
                        }
                      }
                    }
                  }
                }
              }
            }
            `
       }),
   })
   .then(res => res.json())
   .then(json => console.log(json));
</script>

The code above can be copied and pasted directly into a script in [Storefront > Script Manager](https://support.bigcommerce.com/s/article/Using-Script-Manager). Once done, the output from `console.log(json)` is viewable via the browser's Javascript Console.

In addition to using `fetch()`, there's a other ways to query the API:
1. **Using [Apollo Client](https://www.apollographql.com/docs/react/)** - Apollo is a popular GraphQL client that's easy to use in BigCommerce themes. For a a quick example of adding Apollo Client to cornerstone, checkout this [Cornerstone commit](https://github.com/bigcommerce/cornerstone/commit/508feeb1b00d2bb2940771e5e91250a08b6be4d9) on GitHub.
2. **Using any GraphQL Client** - GraphQL is standard with client libraries in many languages, so feel free to explore your options. The focus of the beta is on using the API from frontend JavaScript within Stencil; however, in the future, the API will also be opened up for server-to-server requests.

<div class="HubBlock--callout">
<div class="CalloutBlock--info">
<div class="HubBlock-content">
    
<!-- theme: info -->

### Note
> The above code must be used in a place where the `{{settings.storefront_api.token}}` handlebars variable can be accessed in order to get credentials for the API request. 

</div>
</div>
</div>

---

<a id="sectionId" class="devdocsAnchor"></a>

## Resources

### Examples
* [Bootstrap + Vanilla JS Storefront API Example](https://bigcommerce.github.io/storefront-api-examples/html-bootstrap-vanillajs/) (bigcommerce.github.io)
* [All BigCommerce Storefront API Examples](https://github.com/bigcommerce/storefront-api-examples) (github.com)
* [GraphQL Storefront API Community Group](https://support.bigcommerce.com/s/group/0F91B000000bo3TSAQ/storefront-api-beta) (support.bigcommerce.com)

### Pull Requests
* [Simple GraphQL Example Using Apollo Client with Cornerstone](https://github.com/bigcommerce/cornerstone/compare/graphQL-example)

### Additional Resources
* [GraphQL Cheat Sheet](https://devhints.io/graphql) (devhints.io)
* [GraphQL IDE](https://github.com/andev-software/graphql-ide) (github.com)
* [GraphQL Playground](https://www.npmjs.com/package/graphql-playground-react) (npmjs.com)