# Webasyst API Reference & Template Functions

## Table of Contents
1. [Core Template Functions](#core-template-functions)
2. [Application-Specific Functions](#application-specific-functions)
3. [Smarty Template Variables](#smarty-template-variables)
4. [Template Modifiers](#template-modifiers)
5. [Localization Functions](#localization-functions)
6. [User and Authentication Functions](#user-and-authentication-functions)
7. [URL and Route Functions](#url-and-route-functions)
8. [Cache and Performance Functions](#cache-and-performance-functions)
9. [Event System and Hooks](#event-system-and-hooks)
10. [Best Practices and Examples](#best-practices-and-examples)

## Core Template Functions

### Universal Webasyst Functions

#### `{$wa->title()}`
Returns the page title for the current context.

```smarty
<title>{$wa->title()}</title>
<!-- Output: Homepage - My Store -->

{* Custom title with fallback *}
<title>{$wa->title()|default:"Welcome to My Store"}</title>
```

#### `{$wa->meta($name)}`
Returns meta tag content for SEO.

```smarty
<meta name="description" content="{$wa->meta('description')|escape}">
<meta name="keywords" content="{$wa->meta('keywords')|escape}">

{* Check if meta exists *}
{if $wa->meta('description')}
    <meta name="description" content="{$wa->meta('description')|escape}">
{/if}
```

#### `{$wa->header()}`
Outputs required head elements from applications.

```smarty
<head>
    <meta charset="utf-8">
    <title>{$wa->title()}</title>
    
    {* Required: outputs CSS, JS, and other head elements *}
    {$wa->header()}
</head>
```

#### `{$wa->footer()}`
Outputs required footer JavaScript and elements.

```smarty
<body>
    <!-- Page content -->
    
    {* Required: outputs JavaScript and tracking codes *}
    {$wa->footer()}
</body>
```

#### `{$wa->app()}`
Returns current application ID.

```smarty
<body class="app-{$wa->app()}">
    {if $wa->app() == 'shop'}
        {* Shop-specific content *}
    {elseif $wa->app() == 'site'}
        {* Site-specific content *}
    {/if}
</body>
```

#### `{$wa->appExists($app_id)}`
Checks if an application is installed and available.

```smarty
{if $wa->appExists('shop')}
    <a href="{$wa_app_url}shop/">Shop</a>
{/if}

{if $wa->appExists('blog')}
    <a href="{$wa_app_url}blog/">Blog</a>
{/if}
```

#### `{$wa->locale()}`
Returns current locale code.

```smarty
<html lang="{$wa->locale()}">

{* Conditional content based on locale *}
{if $wa->locale() == 'ru_RU'}
    <div class="russian-content">Русский контент</div>
{else}
    <div class="english-content">English content</div>
{/if}
```

#### `{$wa->user()}`
Returns current user object with authentication methods.

```smarty
{if $wa->user()->isAuth()}
    <span>Welcome, {$wa->user()->getName()}</span>
    <a href="{$wa_url}?action=logout">Logout</a>
{else}
    <a href="{$wa_app_url}shop/login/">Login</a>
{/if}

{* User avatar *}
{if $wa->user()->isAuth()}
    <img src="{$wa->user()->getPhoto()}" alt="{$wa->user()->getName()}">
{/if}
```

#### `{$wa->accountName()}`
Returns the account/site name.

```smarty
<h1>{$wa->accountName()}</h1>
<!-- Output: My Store Name -->

{* Logo with site name fallback *}
{if $logo_url}
    <img src="{$logo_url}" alt="{$wa->accountName()}">
{else}
    <span class="site-name">{$wa->accountName()}</span>
{/if}
```

#### `{$wa->version()}`
Returns Webasyst framework version.

```smarty
{* Cache busting for assets *}
<link href="{$wa_theme_url}css/style.css?v={$wa->version()}" rel="stylesheet">
<script src="{$wa_theme_url}js/script.js?v={$wa->version()}"></script>
```

#### `{$wa->currentUrl()}`
Returns current page URL.

```smarty
<link rel="canonical" href="{$wa->currentUrl()}">

{* Social sharing *}
<meta property="og:url" content="{$wa->currentUrl()}">

{* Print current URL *}
<div class="current-url">Current page: {$wa->currentUrl()}</div>
```

#### `{$wa->csrf()}`
Generates CSRF token for forms.

```smarty
<form action="/submit/" method="post">
    {$wa->csrf()}
    <input type="text" name="message">
    <button type="submit">Send</button>
</form>
```

## Application-Specific Functions

### Shop-Script Functions

#### Product Functions

```smarty
{* Get product by ID *}
{$product = $wa->shop->getProduct($product_id)}

{* Product price formatting *}
<span class="price">{$product.price|wa_currency}</span>
<span class="price">{$product.price|wa_currency:$product.currency}</span>

{* Product images *}
<img src="{$product.image_url}" alt="{$product.name|escape}">

{* Product variants *}
{foreach $product.skus as $sku}
    <div class="variant">
        <span class="price">{$sku.price|wa_currency}</span>
        <span class="stock">{$sku.count} in stock</span>
    </div>
{/foreach}

{* Product features *}
{foreach $product.features as $feature}
    <div class="feature">
        <span class="feature-name">{$feature.name}:</span>
        <span class="feature-value">{$feature.value}</span>
    </div>
{/foreach}
```

#### Category Functions

```smarty
{* Get category tree *}
{$categories = $wa->shop->getCategories()}

{* Category navigation *}
{foreach $categories as $category}
    <a href="{$wa_app_url}shop/category/{$category.url}/">
        {$category.name}
    </a>
    
    {if $category.childs}
        <ul class="subcategories">
            {foreach $category.childs as $subcategory}
                <li>
                    <a href="{$wa_app_url}shop/category/{$subcategory.url}/">
                        {$subcategory.name}
                    </a>
                </li>
            {/foreach}
        </ul>
    {/if}
{/foreach}
```

#### Cart Functions

```smarty
{* Cart information *}
<div class="cart-info">
    <span class="cart-count">{$cart.count}</span>
    <span class="cart-total">{$cart.total|wa_currency}</span>
</div>

{* Cart items *}
{foreach $cart.items as $item}
    <div class="cart-item">
        <h4>{$item.name}</h4>
        <span class="quantity">{$item.quantity}</span>
        <span class="price">{$item.price|wa_currency}</span>
        <span class="total">{$item.total|wa_currency}</span>
    </div>
{/foreach}
```

### Site App Functions

#### Page Functions

```smarty
{* Get page content *}
{$page = $wa->site->getPage($page_id)}

{* Page hierarchy *}
{$pages = $wa->site->getPages()}
{foreach $pages as $page}
    <a href="{$page.url}">{$page.title}</a>
{/foreach}

{* Breadcrumbs *}
{if $breadcrumbs}
    <nav class="breadcrumbs">
        {foreach $breadcrumbs as $crumb}
            {if !$crumb@last}
                <a href="{$crumb.url}">{$crumb.title}</a>
            {else}
                <span>{$crumb.title}</span>
            {/if}
        {/foreach}
    </nav>
{/if}
```

#### Blog Functions

```smarty
{* Get blog posts *}
{$posts = $wa->blog->getPosts()}

{foreach $posts as $post}
    <article class="blog-post">
        <h2><a href="{$post.url}">{$post.title}</a></h2>
        <div class="post-meta">
            <span class="author">{$post.author.name}</span>
            <time datetime="{$post.datetime}">{$post.datetime|wa_datetime}</time>
        </div>
        <div class="post-excerpt">{$post.text|strip_tags|truncate:200}</div>
    </article>
{/foreach}
```

## Smarty Template Variables

### Global Variables

```smarty
{* Framework URLs *}
{$wa_url}           - Base Webasyst URL
{$wa_app_url}       - Current application URL
{$wa_theme_url}     - Current theme URL
{$wa_theme_path}    - Current theme file path

{* Application information *}
{$wa_app}           - Current application ID
{$wa_app_name}      - Current application name

{* User information *}
{$user}             - Current user object
{$user.id}          - User ID
{$user.name}        - User display name
{$user.email}       - User email
{$user.photo}       - User avatar URL

{* Request information *}
{$request_uri}      - Current request URI
{$request_method}   - HTTP request method
{$request_time}     - Request timestamp
```

### Shop-Script Variables

```smarty
{* Product variables *}
{$product.id}              - Product ID
{$product.name}            - Product name
{$product.description}     - Product description
{$product.price}           - Product price
{$product.currency}        - Product currency
{$product.url}             - Product URL
{$product.image_url}       - Main product image
{$product.images}          - All product images
{$product.skus}            - Product variants/SKUs
{$product.features}        - Product features
{$product.categories}      - Product categories

{* Category variables *}
{$category.id}             - Category ID
{$category.name}           - Category name
{$category.description}    - Category description
{$category.url}            - Category URL
{$category.count}          - Products count
{$category.childs}         - Subcategories

{* Cart variables *}
{$cart.count}              - Items count in cart
{$cart.total}              - Cart total amount
{$cart.items}              - Cart items array
{$cart.shipping}           - Shipping information
{$cart.discount}           - Applied discounts

{* Order variables *}
{$order.id}                - Order ID
{$order.total}             - Order total
{$order.status}            - Order status
{$order.items}             - Order items
{$order.customer}          - Customer information
{$order.shipping}          - Shipping details
{$order.payment}           - Payment information
```

### Site App Variables

```smarty
{* Page variables *}
{$page.id}                 - Page ID
{$page.title}              - Page title
{$page.content}            - Page content
{$page.url}                - Page URL
{$page.meta_title}         - SEO title
{$page.meta_description}   - SEO description
{$page.parent_id}          - Parent page ID

{* Blog variables *}
{$post.id}                 - Post ID
{$post.title}              - Post title
{$post.text}               - Post content
{$post.url}                - Post URL
{$post.datetime}           - Post date/time
{$post.author}             - Post author
{$post.categories}         - Post categories
{$post.tags}               - Post tags
```

## Template Modifiers

### Webasyst-Specific Modifiers

#### `|wa_currency`
Formats currency values.

```smarty
{$product.price|wa_currency}
<!-- Output: $19.99 -->

{$product.price|wa_currency:$product.currency}
<!-- Output: €19.99 -->

{$product.price|wa_currency:'USD':true}
<!-- Output: $19.99 USD -->
```

#### `|wa_datetime`
Formats date and time values.

```smarty
{$post.datetime|wa_datetime}
<!-- Output: January 15, 2024 3:30 PM -->

{$post.datetime|wa_datetime:'humandiff'}
<!-- Output: 2 hours ago -->

{$post.datetime|wa_datetime:'date'}
<!-- Output: January 15, 2024 -->

{$post.datetime|wa_datetime:'time'}
<!-- Output: 3:30 PM -->
```

#### `|wa_url`
Generates proper URLs for applications.

```smarty
{$page.url|wa_url}
<!-- Output: /page-slug/ -->

{'shop/product/'|cat:$product.url|wa_url}
<!-- Output: /shop/product/product-slug/ -->
```

#### `|wa_size`
Formats file sizes.

```smarty
{$file.size|wa_size}
<!-- Output: 1.5 MB -->

{$file.size|wa_size:'kb'}
<!-- Output: 1536 KB -->
```

### Standard Smarty Modifiers

#### String Modifiers

```smarty
{* Escape HTML *}
{$user_input|escape}
{$user_input|escape:'html'}

{* Strip HTML tags *}
{$html_content|strip_tags}

{* Truncate text *}
{$long_text|truncate:100}
{$long_text|truncate:100:'...'}

{* Convert case *}
{$text|upper}
{$text|lower}
{$text|capitalize}

{* Replace text *}
{$text|replace:'old':'new'}

{* URL encoding *}
{$search_query|urlencode}
```

#### Number Modifiers

```smarty
{* Format numbers *}
{$number|number_format}
{$number|number_format:2}
{$number|number_format:2:',':' '}

{* Mathematical operations *}
{$price * 1.2|string_format:'%.2f'}
```

#### Date Modifiers

```smarty
{* Format dates *}
{$timestamp|date_format:'%Y-%m-%d'}
{$timestamp|date_format:'%B %d, %Y'}
{$timestamp|date_format:'%A, %B %d, %Y at %I:%M %p'}
```

## Localization Functions

### Basic Localization

#### `{_w('text')}`
Translates text using the current locale.

```smarty
{_w('Add to Cart')}
<!-- Output: Add to Cart (English) or Добавить в корзину (Russian) -->

{* With parameters *}
{_w('Welcome, %s', $user.name)}
{_w('%d items in cart', $cart.count)}

{* Pluralization *}
{_w('%d product', '%d products', $count, $count)}
```

#### `{_wp('domain', 'text')}`
Translates text from specific domain.

```smarty
{_wp('shop', 'Add to Cart')}
{_wp('site', 'Read More')}
{_wp('blog', 'Leave a Comment')}
```

### Advanced Localization

```smarty
{* Check current locale *}
{if $wa->locale() == 'ru_RU'}
    <div class="russian-content">
        {_w('Russian specific content')}
    </div>
{else}
    <div class="english-content">
        {_w('English content')}
    </div>
{/if}

{* Locale-specific formatting *}
{if $wa->locale()|substr:0:2 == 'en'}
    {$date|date_format:'%m/%d/%Y'}
{else}
    {$date|date_format:'%d.%m.%Y'}
{/if}
```

## User and Authentication Functions

### User Information

```smarty
{* Check authentication *}
{if $wa->user()->isAuth()}
    <div class="user-menu">
        <img src="{$wa->user()->getPhoto()}" alt="{$wa->user()->getName()}">
        <span>{_w('Hello, %s', $wa->user()->getName())}</span>
        <a href="{$wa_url}?action=logout">{_w('Logout')}</a>
    </div>
{else}
    <div class="guest-menu">
        <a href="{$wa_app_url}shop/login/">{_w('Login')}</a>
        <a href="{$wa_app_url}shop/signup/">{_w('Sign up')}</a>
    </div>
{/if}

{* User contact information *}
{if $wa->user()->isAuth()}
    {$user_id = $wa->user()->getId()}
    {$user_email = $wa->user()->get('email')}
    {$user_phone = $wa->user()->get('phone')}
{/if}
```

### Permission Checks

```smarty
{* Check user rights *}
{if $wa->user()->getRights('shop', 'products')}
    <a href="{$wa_app_url}shop/products/add/">{_w('Add Product')}</a>
{/if}

{* Admin access *}
{if $wa->user()->isAdmin()}
    <div class="admin-panel">
        <a href="{$wa_url}webasyst/">{_w('Admin Panel')}</a>
    </div>
{/if}
```

## URL and Route Functions

### URL Generation

```smarty
{* Application URLs *}
<a href="{$wa_app_url}shop/">{_w('Shop')}</a>
<a href="{$wa_app_url}site/">{_w('Home')}</a>
<a href="{$wa_app_url}blog/">{_w('Blog')}</a>

{* Dynamic URLs *}
<a href="{$wa_app_url}shop/product/{$product.url}/">{$product.name}</a>
<a href="{$wa_app_url}shop/category/{$category.url}/">{$category.name}</a>

{* URLs with parameters *}
<a href="{$wa_app_url}shop/search/?query={$search_query|urlencode}">
    {_w('Search results')}
</a>
```

### Route Information

```smarty
{* Current route *}
{$current_route = $wa->currentRoute()}

{* Route parameters *}
{$route_params = $wa->currentRoute('params')}

{* Check specific route *}
{if $wa->currentRoute('id') == 'product'}
    <div class="product-page-specific">Product page content</div>
{/if}
```

## Cache and Performance Functions

### Template Caching

```smarty
{* Cache template block *}
{cache id="product_list" expire=3600}
    {foreach $products as $product}
        <div class="product">{$product.name}</div>
    {/foreach}
{/cache}

{* Conditional caching *}
{if !$wa->user()->isAuth()}
    {cache id="public_content" expire=1800}
        <div class="public-content">...</div>
    {/cache}
{/if}
```

### Performance Optimization

```smarty
{* Lazy loading images *}
<img data-src="{$product.image_url}" alt="{$product.name|escape}" 
     class="lazy-load" loading="lazy">

{* Preload critical resources *}
<link rel="preload" href="{$wa_theme_url}css/critical.css" as="style">
<link rel="preload" href="{$wa_theme_url}fonts/main.woff2" as="font" type="font/woff2" crossorigin>

{* Resource hints *}
<link rel="dns-prefetch" href="//cdn.example.com">
<link rel="preconnect" href="https://fonts.googleapis.com" crossorigin>
```

## Event System and Hooks

### Frontend Hooks

```smarty
{* Hook into frontend events *}
{wa_hook('frontend_head')}
{wa_hook('frontend_nav')}
{wa_hook('frontend_footer')}

{* Application-specific hooks *}
{wa_hook('shop_product_page')}
{wa_hook('shop_cart_items')}
{wa_hook('site_page_content')}
```

### Custom Event Triggers

```smarty
{* Trigger custom events *}
{wa_event('theme_custom_event', ['product' => $product])}

{* Conditional event triggers *}
{if $product.featured}
    {wa_event('featured_product_display', ['product' => $product])}
{/if}
```

## Best Practices and Examples

### Complete Template Example

```smarty
{* Complete product page template example *}
<div class="product-page" itemscope itemtype="http://schema.org/Product">
    {* Breadcrumbs *}
    {if $breadcrumbs}
        <nav class="breadcrumbs" aria-label="{_w('Breadcrumb')}">
            {foreach $breadcrumbs as $crumb}
                {if !$crumb@last}
                    <a href="{$crumb.url}">{$crumb.title}</a>
                    <span class="separator">/</span>
                {else}
                    <span class="current">{$crumb.title}</span>
                {/if}
            {/foreach}
        </nav>
    {/if}
    
    <div class="product-content">
        <div class="product-images">
            <div class="main-image">
                <img src="{$product.image_url}" alt="{$product.name|escape}" 
                     itemprop="image" class="img-responsive">
            </div>
            
            {if $product.images}
                <div class="image-gallery">
                    {foreach $product.images as $image}
                        <img src="{$image.thumb_url}" alt="{$product.name|escape}" 
                             data-full="{$image.full_url}" class="gallery-thumb">
                    {/foreach}
                </div>
            {/if}
        </div>
        
        <div class="product-info">
            <h1 class="product-title" itemprop="name">{$product.name}</h1>
            
            <div class="product-price" itemprop="offers" itemscope itemtype="http://schema.org/Offer">
                <span class="price" itemprop="price" content="{$product.price}">
                    {$product.price|wa_currency:$product.currency}
                </span>
                <meta itemprop="priceCurrency" content="{$product.currency}">
                <meta itemprop="availability" content="http://schema.org/InStock">
            </div>
            
            {if $product.description}
                <div class="product-description" itemprop="description">
                    {$product.description}
                </div>
            {/if}
            
            {* Product features *}
            {if $product.features}
                <div class="product-features">
                    <h3>{_w('Specifications')}</h3>
                    <dl class="features-list">
                        {foreach $product.features as $feature}
                            <dt class="feature-name">{$feature.name}:</dt>
                            <dd class="feature-value">{$feature.value}</dd>
                        {/foreach}
                    </dl>
                </div>
            {/if}
            
            {* Add to cart form *}
            <form class="add-to-cart-form" action="{$wa_app_url}shop/cart/add/" method="post">
                {$wa->csrf()}
                <input type="hidden" name="product_id" value="{$product.id}">
                
                {* Product variants *}
                {if $product.skus}
                    <div class="product-variants">
                        {foreach $product.skus as $sku}
                            <label class="variant-option">
                                <input type="radio" name="sku_id" value="{$sku.id}" 
                                       data-price="{$sku.price}" {if $sku@first}checked{/if}>
                                <span class="variant-label">
                                    {foreach $sku.features as $feature}
                                        {$feature.value}
                                    {/foreach}
                                    - {$sku.price|wa_currency:$product.currency}
                                </span>
                            </label>
                        {/foreach}
                    </div>
                {/if}
                
                <div class="quantity-selector">
                    <label for="quantity">{_w('Quantity')}:</label>
                    <input type="number" id="quantity" name="quantity" 
                           value="1" min="1" max="{$product.count}" class="form-control">
                </div>
                
                <button type="submit" class="btn btn-primary btn-add-to-cart">
                    {_w('Add to Cart')}
                </button>
            </form>
            
            {* Social sharing *}
            <div class="product-sharing">
                <h4>{_w('Share this product:')}</h4>
                <a href="https://www.facebook.com/sharer/sharer.php?u={$wa->currentUrl()|urlencode}" 
                   target="_blank" class="share-facebook">
                    <i class="fa fa-facebook"></i> Facebook
                </a>
                <a href="https://twitter.com/intent/tweet?url={$wa->currentUrl()|urlencode}&text={$product.name|urlencode}" 
                   target="_blank" class="share-twitter">
                    <i class="fa fa-twitter"></i> Twitter
                </a>
            </div>
        </div>
    </div>
    
    {* Related products *}
    {if $related_products}
        <section class="related-products">
            <h3>{_w('Related Products')}</h3>
            <div class="products-grid">
                {foreach $related_products as $related_product}
                    <div class="product-card">
                        <a href="{$wa_app_url}shop/product/{$related_product.url}/">
                            <img src="{$related_product.image_url}" alt="{$related_product.name|escape}">
                            <h4>{$related_product.name}</h4>
                            <span class="price">{$related_product.price|wa_currency}</span>
                        </a>
                    </div>
                {/foreach}
            </div>
        </section>
    {/if}
    
    {* Structured data *}
    <script type="application/ld+json">
    {
        "@context": "http://schema.org",
        "@type": "Product",
        "name": "{$product.name|escape:'json'}",
        "description": "{$product.description|strip_tags|escape:'json'}",
        "image": "{$product.image_url}",
        "offers": {
            "@type": "Offer",
            "price": "{$product.price}",
            "priceCurrency": "{$product.currency}",
            "availability": "http://schema.org/InStock"
        }
    }
    </script>
</div>
```

### Error Handling

```smarty
{* Safe variable access *}
{if $product}
    <h1>{$product.name}</h1>
{else}
    <div class="error-message">
        {_w('Product not found')}
    </div>
{/if}

{* Default values *}
<img src="{$product.image_url|default:'/wa-content/img/no-image.png'}" 
     alt="{$product.name|default:'Product'|escape}">

{* Error boundaries *}
{try}
    {$complex_calculation = $price * $tax_rate + $shipping}
    <span class="total">{$complex_calculation|wa_currency}</span>
{catch}
    <span class="error">{_w('Calculation error')}</span>
{/try}
```

### Performance Optimization

```smarty
{* Conditional loading *}
{if $show_reviews}
    {include file="product-reviews.html"}
{/if}

{* Lazy loading for non-critical content *}
<div class="lazy-section" data-load-url="{$wa_app_url}shop/product/{$product.id}/reviews/">
    <div class="loading-placeholder">{_w('Loading reviews...')}</div>
</div>

{* Resource optimization *}
<link rel="preload" href="{$wa_theme_url}css/critical.css" as="style">
<link rel="stylesheet" href="{$wa_theme_url}css/main.css" media="print" onload="this.media='all'">
```

This comprehensive API reference provides developers with all the essential functions, variables, and best practices needed for professional Webasyst theme development across all applications in the ecosystem.