# Shop-Script Theme Development Guide

## Table of Contents
1. [Shop-Script Overview](#shop-script-overview)
2. [eCommerce Theme Structure](#ecommerce-theme-structure)
3. [Product Display Components](#product-display-components)
4. [Shopping Cart and Checkout](#shopping-cart-and-checkout)
5. [Category and Navigation](#category-and-navigation)
6. [Payment Integration Templates](#payment-integration-templates)
7. [Customer Account Templates](#customer-account-templates)
8. [Mobile eCommerce Optimization](#mobile-ecommerce-optimization)

## Shop-Script Overview

Shop-Script is Webasyst's comprehensive eCommerce application that powers online stores. Developing themes for Shop-Script requires understanding eCommerce-specific functionality, user experience patterns, and conversion optimization techniques.

### Key eCommerce Features

- **Product Catalog**: Complex product management with variants, features, and categories
- **Shopping Cart**: Multi-step cart and checkout process
- **Payment Integration**: Multiple payment gateways and methods
- **Order Management**: Order tracking, status updates, and customer communication
- **Customer Accounts**: Registration, profiles, order history, and wishlist
- **Mobile Commerce**: Responsive design for mobile shopping

### Shop-Script Theme Structure

```
shop-theme/
├── theme.xml                    # Theme manifest
├── layout.html                  # Base layout
├── product.html                 # Product detail page
├── category.html                # Category listing
├── cart.html                    # Shopping cart
├── checkout/                    # Checkout process templates
│   ├── checkout.html           # Main checkout
│   ├── shipping.html           # Shipping options
│   ├── payment.html            # Payment methods
│   └── confirmation.html       # Order confirmation
├── customer/                    # Customer account templates
│   ├── login.html              # Customer login
│   ├── registration.html       # Customer registration
│   ├── account.html            # Account dashboard
│   ├── orders.html             # Order history
│   └── wishlist.html           # Customer wishlist
├── includes/                    # Reusable components
│   ├── product-card.html       # Product grid item
│   ├── cart-widget.html        # Mini cart
│   ├── breadcrumbs.html        # Navigation breadcrumbs
│   └── pagination.html         # Product pagination
└── css/
    ├── shop.css                # eCommerce-specific styles
    ├── products.css            # Product display styles
    ├── cart.css                # Shopping cart styles
    └── checkout.css            # Checkout process styles
```

## eCommerce Theme Structure

### Main Layout for Shop-Script

**Template: `layout.html`**
```smarty
<!DOCTYPE html>
<html lang="{$wa->locale()}" class="shop-layout">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    
    {* SEO and Open Graph tags *}
    <title>{$wa->title()}</title>
    {$wa->meta()}
    
    {* Schema.org structured data for eCommerce *}
    {if $product}
        <script type="application/ld+json">
        {
            "@context": "https://schema.org/",
            "@type": "Product",
            "name": "{$product.name|escape:'javascript'}",
            "image": [
                {if $product.images}
                    {foreach $product.images as $image}
                        "{$image.url_big}"{if !$image@last},{/if}
                    {/foreach}
                {/if}
            ],
            "description": "{$product.summary|escape:'javascript'}",
            "sku": "{$product.sku|escape:'javascript'}",
            "brand": {
                "@type": "Brand",
                "name": "{$product.brand|escape:'javascript'}"
            },
            "offers": {
                "@type": "Offer",
                "url": "{$wa->currentUrl()}",
                "priceCurrency": "{$shop_currency_info.code}",
                "price": "{$product.price}",
                "availability": "http://schema.org/{if $product.count > 0}InStock{else}OutOfStock{/if}",
                "seller": {
                    "@type": "Organization",
                    "name": "{$wa->accountName()}"
                }
            }
        }
        </script>
    {/if}
    
    {* Theme stylesheets *}
    <link rel="stylesheet" href="{$wa_theme_url}css/shop.css?v{$wa_theme_version}">
    {$wa->header()}
</head>
<body class="shop-app">
    {* Header with cart widget *}
    <header class="shop-header">
        <div class="header-container">
            <div class="site-branding">
                <a href="{$wa_url}" class="logo">
                    <img src="{$wa_theme_url}img/logo.png" alt="{$wa->accountName()|escape}">
                </a>
            </div>
            
            {* Main navigation with categories *}
            <nav class="main-navigation">
                {include file="includes/main-nav.html"}
            </nav>
            
            {* Search, account, and cart *}
            <div class="header-actions">
                {include file="includes/search-form.html"}
                {include file="includes/account-menu.html"}
                {include file="includes/cart-widget.html"}
            </div>
        </div>
    </header>

    {* Breadcrumbs for navigation *}
    {if $breadcrumbs}
        {include file="includes/breadcrumbs.html"}
    {/if}

    {* Main content area *}
    <main class="main-content shop-content">
        {$content}
    </main>

    {* Footer with additional links *}
    <footer class="shop-footer">
        <div class="footer-container">
            <div class="footer-section">
                <h4>{_w('Customer Service')}</h4>
                <ul>
                    <li><a href="{$wa_app_url}page/shipping-info/">{_w('Shipping Information')}</a></li>
                    <li><a href="{$wa_app_url}page/returns/">{_w('Returns & Exchanges')}</a></li>
                    <li><a href="{$wa_app_url}page/faq/">{_w('FAQ')}</a></li>
                    <li><a href="{$wa_app_url}page/contact/">{_w('Contact Us')}</a></li>
                </ul>
            </div>
            
            <div class="footer-section">
                <h4>{_w('My Account')}</h4>
                <ul>
                    {if $wa->user()}
                        <li><a href="{$wa_app_url}my/">{_w('My Account')}</a></li>
                        <li><a href="{$wa_app_url}my/orders/">{_w('Order History')}</a></li>
                        <li><a href="{$wa_app_url}my/profile/">{_w('Profile Settings')}</a></li>
                    {else}
                        <li><a href="{$wa_app_url}login/">{_w('Sign In')}</a></li>
                        <li><a href="{$wa_app_url}signup/">{_w('Create Account')}</a></li>
                    {/if}
                </ul>
            </div>
            
            <div class="footer-section">
                <h4>{_w('Quick Links')}</h4>
                <ul>
                    <li><a href="{$wa_app_url}category/new-arrivals/">{_w('New Arrivals')}</a></li>
                    <li><a href="{$wa_app_url}category/sale/">{_w('Sale Items')}</a></li>
                    <li><a href="{$wa_app_url}brands/">{_w('All Brands')}</a></li>
                    <li><a href="{$wa_app_url}gift-cards/">{_w('Gift Cards')}</a></li>
                </ul>
            </div>
        </div>
        
        <div class="footer-bottom">
            <p>&copy; {date('Y')} {$wa->accountName()|escape}. {_w('All rights reserved.')}</p>
        </div>
    </footer>

    {* JavaScript includes *}
    <script src="{$wa_theme_url}js/shop.js?v{$wa_theme_version}"></script>
    {$wa->js()}
</body>
</html>
```

## Product Display Components

### Product Detail Page

**Template: `product.html`**
```smarty
<div class="product-page" itemscope itemtype="http://schema.org/Product">
    <div class="product-container">
        {* Product gallery *}
        <div class="product-gallery">
            {if $product.images}
                <div class="gallery-main">
                    <img src="{$product.images.0.url_big}" 
                         alt="{$product.name|escape}" 
                         itemprop="image"
                         class="main-product-image"
                         id="main-product-image">
                    
                    {if $product.images|count > 1}
                        <button class="gallery-zoom" aria-label="{_w('Zoom image')}">
                            <i class="icon-zoom-in"></i>
                        </button>
                    {/if}
                </div>
                
                {if $product.images|count > 1}
                    <div class="gallery-thumbs">
                        {foreach $product.images as $image}
                            <button type="button" 
                                    class="thumb-btn {if $image@first}active{/if}" 
                                    data-image="{$image.url_big}"
                                    data-zoom="{$image.url_big}">
                                <img src="{$image.url_thumb}" alt="{$product.name|escape}">
                            </button>
                        {/foreach}
                    </div>
                {/if}
            {else}
                <div class="no-image">
                    <img src="{$wa_theme_url}img/no-product-image.png" alt="{_w('No image available')}">
                </div>
            {/if}

            {* Product badges *}
            <div class="product-badges">
                {if $product.badge}
                    <span class="badge badge-{$product.badge}">{$product.badge|escape}</span>
                {/if}
                {if $product.compare_price > $product.price}
                    <span class="badge badge-sale">
                        -{math equation="round((1 - price/compare_price) * 100)" price=$product.price compare_price=$product.compare_price}%
                    </span>
                {/if}
                {if $product.count <= $product.low_stock_threshold && $product.count > 0}
                    <span class="badge badge-low-stock">{_w('Low Stock')}</span>
                {/if}
            </div>
        </div>

        {* Product information *}
        <div class="product-info">
            <div class="product-header">
                <h1 class="product-title" itemprop="name">{$product.name|escape}</h1>
                
                {if $product.sku}
                    <div class="product-sku">
                        {_w('SKU')}: <span itemprop="sku">{$product.sku|escape}</span>
                    </div>
                {/if}

                {if $product.brand}
                    <div class="product-brand" itemprop="brand" itemscope itemtype="http://schema.org/Brand">
                        <span itemprop="name">{$product.brand|escape}</span>
                    </div>
                {/if}
            </div>

            {* Product pricing *}
            <div class="product-pricing" itemprop="offers" itemscope itemtype="http://schema.org/Offer">
                {if $product.compare_price > $product.price}
                    <span class="compare-price">{shop_currency}{$product.compare_price}</span>
                {/if}
                
                <span class="current-price" itemprop="price" content="{$product.price}">
                    {shop_currency}{$product.price}
                </span>
                
                <meta itemprop="priceCurrency" content="{$shop_currency_info.code}">
                <meta itemprop="availability" content="http://schema.org/{if $product.count > 0}InStock{else}OutOfStock{/if}">
            </div>

            {* Stock status *}
            <div class="stock-status">
                {if $product.count > 0}
                    <span class="in-stock">
                        <i class="icon-check"></i>
                        {if $product.count > 10}
                            {_w('In Stock')}
                        {else}
                            {_w('%d left in stock', $product.count)}
                        {/if}
                    </span>
                {else}
                    <span class="out-of-stock">
                        <i class="icon-x"></i>
                        {_w('Out of Stock')}
                    </span>
                {/if}
            </div>

            {* Product summary *}
            {if $product.summary}
                <div class="product-summary" itemprop="description">
                    {$product.summary|nl2br}
                </div>
            {/if}

            {* Product variants (size, color, etc.) *}
            {if $product.skus}
                <div class="product-variants">
                    {foreach $product.features as $feature_id => $feature}
                        {if $feature.values}
                            <div class="variant-group">
                                <label class="variant-label">{$feature.name|escape}:</label>
                                <div class="variant-options">
                                    {foreach $feature.values as $value}
                                        <label class="variant-option">
                                            <input type="radio" 
                                                   name="features[{$feature_id}]" 
                                                   value="{$value.id}"
                                                   {if $value.selected}checked{/if}
                                                   data-price-change="{$value.price_change}">
                                            <span class="variant-text">{$value.name|escape}</span>
                                            {if $value.price_change != 0}
                                                <span class="price-change">
                                                    {if $value.price_change > 0}+{/if}{shop_currency}{$value.price_change}
                                                </span>
                                            {/if}
                                        </label>
                                    {/foreach}
                                </div>
                            </div>
                        {/if}
                    {/foreach}
                </div>
            {/if}

            {* Add to cart form *}
            {if $product.count > 0}
                <form class="add-to-cart-form" 
                      action="{$wa_app_url}cart/add/" 
                      method="post" 
                      data-product-id="{$product.id}">
                    <input type="hidden" name="product_id" value="{$product.id}">
                    
                    <div class="quantity-wrapper">
                        <label for="product-quantity" class="quantity-label">{_w('Quantity')}:</label>
                        <div class="quantity-controls">
                            <button type="button" class="qty-decrease" aria-label="{_w('Decrease quantity')}">-</button>
                            <input type="number" 
                                   id="product-quantity"
                                   name="quantity" 
                                   value="1" 
                                   min="1" 
                                   max="{$product.count}"
                                   class="quantity-input">
                            <button type="button" class="qty-increase" aria-label="{_w('Increase quantity')}">+</button>
                        </div>
                    </div>
                    
                    <div class="cart-actions">
                        <button type="submit" class="btn btn-primary add-to-cart-btn">
                            <i class="icon-cart-plus"></i>
                            <span class="btn-text">{_w('Add to Cart')}</span>
                            <span class="btn-loader" style="display: none;">
                                <i class="icon-spinner icon-spin"></i>
                            </span>
                        </button>
                        
                        <button type="button" class="btn btn-outline wishlist-btn" data-product-id="{$product.id}">
                            <i class="icon-heart"></i>
                            <span>{_w('Add to Wishlist')}</span>
                        </button>
                    </div>
                </form>
            {else}
                <div class="out-of-stock-actions">
                    <button type="button" class="btn btn-outline notify-btn" data-product-id="{$product.id}">
                        <i class="icon-bell"></i>
                        {_w('Notify When Available')}
                    </button>
                </div>
            {/if}

            {* Product features/specifications *}
            {if $product.features}
                <div class="product-features">
                    <h3 class="features-title">{_w('Specifications')}</h3>
                    <dl class="features-list">
                        {foreach $product.features as $feature}
                            {if $feature.value}
                                <dt class="feature-name">{$feature.name|escape}:</dt>
                                <dd class="feature-value">{$feature.value|escape}</dd>
                            {/if}
                        {/foreach}
                    </dl>
                </div>
            {/if}

            {* Social sharing *}
            <div class="product-sharing">
                <span class="sharing-label">{_w('Share')}:</span>
                <div class="sharing-buttons">
                    <a href="https://www.facebook.com/sharer/sharer.php?u={$wa->currentUrl()|urlencode}" 
                       class="share-btn facebook" 
                       target="_blank" 
                       rel="noopener">
                        <i class="icon-facebook"></i>
                    </a>
                    <a href="https://twitter.com/intent/tweet?url={$wa->currentUrl()|urlencode}&text={$product.name|urlencode}" 
                       class="share-btn twitter" 
                       target="_blank" 
                       rel="noopener">
                        <i class="icon-twitter"></i>
                    </a>
                    <a href="https://pinterest.com/pin/create/button/?url={$wa->currentUrl()|urlencode}&media={$product.images.0.url_big|urlencode}&description={$product.name|urlencode}" 
                       class="share-btn pinterest" 
                       target="_blank" 
                       rel="noopener">
                        <i class="icon-pinterest"></i>
                    </a>
                </div>
            </div>
        </div>
    </div>

    {* Product tabs *}
    <div class="product-tabs">
        <div class="tab-navigation">
            <button class="tab-btn active" data-tab="description">{_w('Description')}</button>
            {if $product.features}
                <button class="tab-btn" data-tab="specifications">{_w('Specifications')}</button>
            {/if}
            <button class="tab-btn" data-tab="reviews">{_w('Reviews')} ({$product.review_count|default:0})</button>
            <button class="tab-btn" data-tab="shipping">{_w('Shipping & Returns')}</button>
        </div>

        <div class="tab-content">
            <div class="tab-panel active" id="tab-description">
                {if $product.description}
                    <div class="product-description">
                        {$product.description}
                    </div>
                {else}
                    <p>{_w('No description available.')}</p>
                {/if}
            </div>

            {if $product.features}
                <div class="tab-panel" id="tab-specifications">
                    <table class="specifications-table">
                        {foreach $product.features as $feature}
                            {if $feature.value}
                                <tr>
                                    <td class="spec-name">{$feature.name|escape}</td>
                                    <td class="spec-value">{$feature.value|escape}</td>
                                </tr>
                            {/if}
                        {/foreach}
                    </table>
                </div>
            {/if}

            <div class="tab-panel" id="tab-reviews">
                {include file="includes/product-reviews.html"}
            </div>

            <div class="tab-panel" id="tab-shipping">
                <div class="shipping-info">
                    <h4>{_w('Shipping Information')}</h4>
                    <p>{_w('Free shipping on orders over $50. Standard delivery takes 3-5 business days.')}</p>
                    
                    <h4>{_w('Returns & Exchanges')}</h4>
                    <p>{_w('30-day return policy. Items must be in original condition.')}</p>
                </div>
            </div>
        </div>
    </div>

    {* Related products *}
    {if $product.cross_selling}
        <section class="related-products">
            <h2 class="section-title">{_w('Related Products')}</h2>
            <div class="products-grid">
                {foreach $product.cross_selling as $related_product}
                    {include file="includes/product-card.html" product=$related_product}
                {/foreach}
            </div>
        </section>
    {/if}
</div>
```

### Product Card Component

**Template: `includes/product-card.html`**
```smarty
<div class="product-card" itemscope itemtype="http://schema.org/Product">
    <div class="product-card-inner">
        {* Product image *}
        <div class="product-image">
            <a href="{$product.frontend_url}" class="image-link">
                {if $product.image}
                    <img src="{$product.image.url_thumb}" 
                         alt="{$product.name|escape}"
                         itemprop="image"
                         class="product-img"
                         loading="lazy">
                {else}
                    <img src="{$wa_theme_url}img/no-product-image.png" 
                         alt="{_w('No image')}"
                         class="product-img no-image"
                         loading="lazy">
                {/if}
            </a>

            {* Product badges *}
            <div class="product-badges">
                {if $product.badge}
                    <span class="badge badge-{$product.badge}">{$product.badge|escape}</span>
                {/if}
                {if $product.compare_price > $product.price}
                    <span class="badge badge-sale">
                        -{math equation="round((1 - price/compare_price) * 100)" price=$product.price compare_price=$product.compare_price}%
                    </span>
                {/if}
                {if $product.count == 0}
                    <span class="badge badge-out-of-stock">{_w('Out of Stock')}</span>
                {/if}
            </div>

            {* Quick actions *}
            <div class="product-actions">
                <button type="button" 
                        class="action-btn wishlist-btn" 
                        data-product-id="{$product.id}"
                        title="{_w('Add to Wishlist')}">
                    <i class="icon-heart"></i>
                </button>
                
                <button type="button" 
                        class="action-btn compare-btn" 
                        data-product-id="{$product.id}"
                        title="{_w('Add to Compare')}">
                    <i class="icon-compare"></i>
                </button>
                
                <button type="button" 
                        class="action-btn quick-view-btn" 
                        data-product-id="{$product.id}"
                        title="{_w('Quick View')}">
                    <i class="icon-eye"></i>
                </button>
            </div>
        </div>

        {* Product info *}
        <div class="product-info">
            <h3 class="product-title" itemprop="name">
                <a href="{$product.frontend_url}" class="product-link">
                    {$product.name|escape|truncate:60}
                </a>
            </h3>

            {if $product.brand}
                <div class="product-brand">{$product.brand|escape}</div>
            {/if}

            {* Product rating *}
            {if $product.rating}
                <div class="product-rating">
                    <div class="stars stars-{$product.rating*20}">
                        {for $i = 1 to 5}
                            <i class="icon-star {if $i <= $product.rating}filled{/if}"></i>
                        {/for}
                    </div>
                    <span class="rating-count">({$product.review_count})</span>
                </div>
            {/if}

            {* Product price *}
            <div class="product-price" itemprop="offers" itemscope itemtype="http://schema.org/Offer">
                {if $product.compare_price > $product.price}
                    <span class="compare-price">{shop_currency}{$product.compare_price}</span>
                {/if}
                
                <span class="current-price" itemprop="price" content="{$product.price}">
                    {shop_currency}{$product.price}
                </span>
                
                <meta itemprop="priceCurrency" content="{$shop_currency_info.code}">
                <meta itemprop="availability" content="http://schema.org/{if $product.count > 0}InStock{else}OutOfStock{/if}">
            </div>

            {* Add to cart button *}
            <div class="product-cart-action">
                {if $product.count > 0}
                    <form class="mini-add-to-cart" 
                          action="{$wa_app_url}cart/add/" 
                          method="post" 
                          data-product-id="{$product.id}">
                        <input type="hidden" name="product_id" value="{$product.id}">
                        <input type="hidden" name="quantity" value="1">
                        
                        <button type="submit" class="btn btn-primary btn-sm add-to-cart">
                            <i class="icon-cart-plus"></i>
                            <span>{_w('Add to Cart')}</span>
                        </button>
                    </form>
                {else}
                    <button type="button" class="btn btn-outline btn-sm out-of-stock" disabled>
                        <i class="icon-x"></i>
                        <span>{_w('Out of Stock')}</span>
                    </button>
                {/if}
            </div>
        </div>
    </div>
</div>
```

## Shopping Cart and Checkout

### Shopping Cart Template

**Template: `cart.html`**
```smarty
<div class="cart-page">
    <div class="cart-container">
        <h1 class="page-title">{_w('Shopping Cart')}</h1>

        {if $cart.items}
            <div class="cart-content">
                <div class="cart-items">
                    <div class="cart-header">
                        <div class="header-product">{_w('Product')}</div>
                        <div class="header-price">{_w('Price')}</div>
                        <div class="header-quantity">{_w('Quantity')}</div>
                        <div class="header-total">{_w('Total')}</div>
                        <div class="header-actions"></div>
                    </div>

                    {foreach $cart.items as $item}
                        <div class="cart-item" data-item-id="{$item.id}">
                            <div class="item-product">
                                <div class="product-image">
                                    {if $item.product.image}
                                        <img src="{$item.product.image.url_thumb}" 
                                             alt="{$item.product.name|escape}">
                                    {else}
                                        <img src="{$wa_theme_url}img/no-product-image.png" 
                                             alt="{_w('No image')}">
                                    {/if}
                                </div>
                                
                                <div class="product-details">
                                    <h3 class="product-name">
                                        <a href="{$item.product.frontend_url}">
                                            {$item.product.name|escape}
                                        </a>
                                    </h3>
                                    
                                    {if $item.sku_name && $item.sku_name != $item.product.name}
                                        <div class="sku-name">{$item.sku_name|escape}</div>
                                    {/if}
                                    
                                    {if $item.product.sku}
                                        <div class="product-sku">
                                            {_w('SKU')}: {$item.product.sku|escape}
                                        </div>
                                    {/if}
                                </div>
                            </div>

                            <div class="item-price">
                                {if $item.price != $item.product.price}
                                    <span class="original-price">{shop_currency}{$item.product.price}</span>
                                {/if}
                                <span class="current-price">{shop_currency}{$item.price}</span>
                            </div>

                            <div class="item-quantity">
                                <form class="quantity-form" 
                                      action="{$wa_app_url}cart/save/" 
                                      method="post">
                                    <input type="hidden" name="item_id" value="{$item.id}">
                                    
                                    <div class="quantity-controls">
                                        <button type="button" 
                                                class="qty-decrease" 
                                                data-item-id="{$item.id}">-</button>
                                        <input type="number" 
                                               name="quantity" 
                                               value="{$item.quantity}" 
                                               min="1" 
                                               max="{$item.product.count}"
                                               class="quantity-input"
                                               data-item-id="{$item.id}">
                                        <button type="button" 
                                                class="qty-increase" 
                                                data-item-id="{$item.id}">+</button>
                                    </div>
                                </form>
                            </div>

                            <div class="item-total">
                                <span class="total-price">{shop_currency}{$item.total}</span>
                            </div>

                            <div class="item-actions">
                                <button type="button" 
                                        class="remove-item-btn" 
                                        data-item-id="{$item.id}"
                                        title="{_w('Remove item')}">
                                    <i class="icon-trash"></i>
                                </button>
                            </div>
                        </div>
                    {/foreach}
                </div>

                <div class="cart-summary">
                    <div class="summary-box">
                        <h3 class="summary-title">{_w('Order Summary')}</h3>
                        
                        <div class="summary-line">
                            <span class="label">{_w('Subtotal')}:</span>
                            <span class="value">{shop_currency}{$cart.subtotal}</span>
                        </div>
                        
                        {if $cart.discount}
                            <div class="summary-line discount">
                                <span class="label">{_w('Discount')}:</span>
                                <span class="value">-{shop_currency}{$cart.discount}</span>
                            </div>
                        {/if}
                        
                        <div class="summary-line shipping">
                            <span class="label">{_w('Shipping')}:</span>
                            <span class="value">
                                {if $cart.shipping}
                                    {shop_currency}{$cart.shipping}
                                {else}
                                    {_w('Calculated at checkout')}
                                {/if}
                            </span>
                        </div>
                        
                        {if $cart.tax}
                            <div class="summary-line tax">
                                <span class="label">{_w('Tax')}:</span>
                                <span class="value">{shop_currency}{$cart.tax}</span>
                            </div>
                        {/if}
                        
                        <div class="summary-line total">
                            <span class="label">{_w('Total')}:</span>
                            <span class="value">{shop_currency}{$cart.total}</span>
                        </div>

                        {* Promotional code *}
                        <div class="promo-code-section">
                            <form class="promo-form" action="{$wa_app_url}cart/coupon/" method="post">
                                <div class="promo-input-group">
                                    <input type="text" 
                                           name="coupon_code" 
                                           placeholder="{_w('Promotional code')}"
                                           class="promo-input">
                                    <button type="submit" class="btn btn-outline btn-sm">
                                        {_w('Apply')}
                                    </button>
                                </div>
                            </form>
                        </div>

                        <div class="checkout-actions">
                            <a href="{$wa_app_url}checkout/" class="btn btn-primary btn-lg checkout-btn">
                                <i class="icon-lock"></i>
                                <span>{_w('Proceed to Checkout')}</span>
                            </a>
                            
                            <a href="{$wa_app_url}" class="continue-shopping-link">
                                <i class="icon-arrow-left"></i>
                                {_w('Continue Shopping')}
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        {else}
            <div class="empty-cart">
                <div class="empty-cart-icon">
                    <i class="icon-cart-empty"></i>
                </div>
                <h2 class="empty-cart-title">{_w('Your cart is empty')}</h2>
                <p class="empty-cart-message">{_w('Add some products to get started!')}</p>
                
                <a href="{$wa_app_url}" class="btn btn-primary">
                    <i class="icon-arrow-left"></i>
                    {_w('Start Shopping')}
                </a>
            </div>
        {/if}
    </div>
</div>
```

### Mini Cart Widget

**Template: `includes/cart-widget.html`**
```smarty
<div class="cart-widget" id="cart-widget">
    <button class="cart-toggle" type="button" aria-label="{_w('Shopping cart')}" data-toggle="dropdown">
        <i class="icon-cart"></i>
        <span class="cart-count" id="cart-count">{$cart.count|default:0}</span>
        <span class="cart-total" id="cart-total">{shop_currency}{$cart.total|default:0}</span>
    </button>

    <div class="cart-dropdown" id="cart-dropdown">
        <div class="cart-dropdown-header">
            <h4 class="cart-title">{_w('Shopping Cart')}</h4>
        </div>

        <div class="cart-dropdown-body" id="cart-dropdown-body">
            {if $cart.items}
                <div class="mini-cart-items">
                    {foreach $cart.items as $item}
                        <div class="mini-cart-item" data-item-id="{$item.id}">
                            <div class="item-image">
                                {if $item.product.image}
                                    <img src="{$item.product.image.url_thumb}" 
                                         alt="{$item.product.name|escape}">
                                {else}
                                    <div class="no-image"></div>
                                {/if}
                            </div>
                            
                            <div class="item-details">
                                <h5 class="item-name">
                                    <a href="{$item.product.frontend_url}">
                                        {$item.product.name|escape|truncate:30}
                                    </a>
                                </h5>
                                <div class="item-price-qty">
                                    {$item.quantity} × {shop_currency}{$item.price}
                                </div>
                            </div>
                            
                            <button type="button" 
                                    class="remove-mini-item" 
                                    data-item-id="{$item.id}"
                                    title="{_w('Remove')}">
                                <i class="icon-x"></i>
                            </button>
                        </div>
                    {/foreach}
                </div>

                <div class="mini-cart-footer">
                    <div class="mini-cart-total">
                        <strong>{_w('Total')}: {shop_currency}{$cart.total}</strong>
                    </div>
                    
                    <div class="mini-cart-actions">
                        <a href="{$wa_app_url}cart/" class="btn btn-outline btn-sm">
                            {_w('View Cart')}
                        </a>
                        <a href="{$wa_app_url}checkout/" class="btn btn-primary btn-sm">
                            {_w('Checkout')}
                        </a>
                    </div>
                </div>
            {else}
                <div class="mini-cart-empty">
                    <i class="icon-cart-empty"></i>
                    <p>{_w('Your cart is empty')}</p>
                </div>
            {/if}
        </div>
    </div>
</div>
```

This comprehensive Shop-Script theme guide provides the foundation for creating professional eCommerce websites with optimized product displays, shopping cart functionality, and checkout processes. The templates include modern UX patterns, SEO optimization, structured data, and accessibility features essential for successful online stores.