# PHP and Smarty Integration in Webasyst Themes

## Table of Contents
1. [Integration Overview](#integration-overview)
2. [Business Logic vs Presentation](#business-logic-vs-presentation)
3. [PHP Controller Patterns](#php-controller-patterns)
4. [Smarty Template Patterns](#smarty-template-patterns)
5. [Data Assignment and Variables](#data-assignment-and-variables)
6. [Template Functions and Modifiers](#template-functions-and-modifiers)
7. [Event System and Hooks](#event-system-and-hooks)
8. [Advanced Integration Techniques](#advanced-integration-techniques)

## Integration Overview

Webasyst follows the Model-View-Controller (MVC) pattern where PHP handles business logic and data processing while Smarty templates handle presentation. This separation ensures maintainable, scalable themes that can be easily customized without affecting application functionality.

### Architecture Flow

```
User Request → Router → Controller (PHP) → Model (PHP) → View (Smarty) → Response
```

### Key Principles

1. **Separation of Concerns**: Business logic stays in PHP, presentation in Smarty
2. **Data Flow**: Controllers prepare data, templates display it
3. **Security**: User input sanitization in PHP, output escaping in Smarty
4. **Performance**: PHP caching for data, Smarty caching for templates

## Business Logic vs Presentation

### PHP Responsibilities (Business Logic)

- Data validation and processing
- Database operations
- User authentication and authorization
- API integrations
- File uploads and processing
- Session management
- Security implementations

### Smarty Responsibilities (Presentation)

- HTML structure and layout
- Data display and formatting
- User interface interactions
- Template inheritance and includes
- Output formatting and escaping
- Conditional display logic

### Example: Product Display

**PHP Controller (shopFrontendProductAction.class.php)**
```php
<?php

class shopFrontendProductAction extends waViewAction
{
    public function execute()
    {
        // Get product ID from request
        $product_id = waRequest::get('id', 0, waRequest::TYPE_INT);
        
        if (!$product_id) {
            throw new waException('Product not found', 404);
        }
        
        // Load product data
        $product_model = new shopProductModel();
        $product = $product_model->getById($product_id);
        
        if (!$product || !$product['status']) {
            throw new waException('Product not found', 404);
        }
        
        // Load additional product data
        $product_service = new shopProductService();
        $product_data = $product_service->getProductData($product_id, [
            'images' => true,
            'features' => true,
            'reviews' => true,
            'related' => true,
            'categories' => true,
            'tags' => true
        ]);
        
        // Process pricing
        $this->processProductPricing($product_data);
        
        // Handle stock information
        $this->processStockInfo($product_data);
        
        // Load reviews
        $reviews = $this->getProductReviews($product_id);
        
        // Assign data to template
        $this->view->assign([
            'product' => $product_data,
            'reviews' => $reviews,
            'breadcrumbs' => $this->getBreadcrumbs($product_data),
            'meta_title' => $product_data['meta_title'] ?: $product_data['name'],
            'meta_description' => $product_data['meta_description'] ?: $product_data['summary'],
            'canonical_url' => wa()->getRouteUrl('shop/frontend/product', ['id' => $product_id])
        ]);
        
        // Set page metadata
        $this->setPageMeta($product_data);
    }
    
    private function processProductPricing($product)
    {
        $currency = wa('shop')->getConfig()->getCurrency();
        $product['formatted_price'] = waCurrency::format('%{h}', $product['price'], $currency);
        
        if ($product['compare_price'] > $product['price']) {
            $product['formatted_compare_price'] = waCurrency::format('%{h}', $product['compare_price'], $currency);
            $product['discount_percent'] = round((1 - $product['price'] / $product['compare_price']) * 100);
        }
        
        return $product;
    }
    
    private function processStockInfo($product)
    {
        $stock_model = new shopProductStocksModel();
        $stocks = $stock_model->getByProduct($product['id']);
        
        $product['in_stock'] = array_sum(array_column($stocks, 'count')) > 0;
        $product['stock_count'] = array_sum(array_column($stocks, 'count'));
        
        return $product;
    }
    
    private function getProductReviews($product_id, $limit = 10)
    {
        $review_model = new shopProductReviewsModel();
        return $review_model->getByProduct($product_id, [
            'limit' => $limit,
            'approved' => true,
            'order' => 'datetime DESC'
        ]);
    }
    
    private function getBreadcrumbs($product)
    {
        $breadcrumbs = [];
        
        // Add category breadcrumbs
        if (!empty($product['categories'])) {
            $category = current($product['categories']);
            $category_model = new shopCategoryModel();
            $category_path = $category_model->getPath($category['id']);
            
            foreach ($category_path as $cat) {
                $breadcrumbs[] = [
                    'name' => $cat['name'],
                    'url' => wa()->getRouteUrl('shop/frontend/category', ['category_url' => $cat['url']])
                ];
            }
        }
        
        // Add product name
        $breadcrumbs[] = [
            'name' => $product['name'],
            'url' => ''
        ];
        
        return $breadcrumbs;
    }
    
    private function setPageMeta($product)
    {
        // Set page title
        wa()->getResponse()->setTitle($product['meta_title'] ?: $product['name']);
        
        // Set meta description
        if ($product['meta_description']) {
            wa()->getResponse()->setMeta('description', $product['meta_description']);
        }
        
        // Set Open Graph tags
        wa()->getResponse()->setMeta('og:title', $product['name']);
        wa()->getResponse()->setMeta('og:description', $product['summary']);
        wa()->getResponse()->setMeta('og:type', 'product');
        
        if (!empty($product['images'])) {
            $image = current($product['images']);
            wa()->getResponse()->setMeta('og:image', $image['url_big']);
        }
    }
}
```

**Smarty Template (product.html)**
```smarty
{* Product page template - handles presentation only *}

<article class="product-page" itemscope itemtype="http://schema.org/Product">
    {* Breadcrumbs *}
    {if $breadcrumbs}
        <nav class="breadcrumbs" aria-label="Breadcrumb">
            <ol>
                <li><a href="{$wa_url}">Home</a></li>
                {foreach $breadcrumbs as $crumb}
                    <li {if !$crumb.url}aria-current="page"{/if}>
                        {if $crumb.url}
                            <a href="{$crumb.url}">{$crumb.name|escape}</a>
                        {else}
                            {$crumb.name|escape}
                        {/if}
                    </li>
                {/foreach}
            </ol>
        </nav>
    {/if}

    <div class="product-content">
        {* Product Gallery *}
        <div class="product-gallery">
            {if $product.images}
                <div class="gallery-main">
                    <img src="{$product.images.0.url_big}" 
                         alt="{$product.name|escape}" 
                         itemprop="image"
                         class="main-image">
                </div>
                
                {if count($product.images) > 1}
                    <div class="gallery-thumbs">
                        {foreach $product.images as $image}
                            <button type="button" class="thumb-btn" data-image="{$image.url_big}">
                                <img src="{$image.url_thumb}" alt="{$product.name|escape}">
                            </button>
                        {/foreach}
                    </div>
                {/if}
            {else}
                <div class="no-image">
                    <img src="{$wa_theme_url}img/no-product-image.png" alt="No image">
                </div>
            {/if}
        </div>

        {* Product Information *}
        <div class="product-info">
            <header class="product-header">
                <h1 class="product-title" itemprop="name">{$product.name|escape}</h1>
                
                {if $product.sku}
                    <div class="product-sku">
                        SKU: <span itemprop="sku">{$product.sku|escape}</span>
                    </div>
                {/if}
            </header>

            {* Price Information *}
            <div class="product-pricing" itemprop="offers" itemscope itemtype="http://schema.org/Offer">
                {if $product.formatted_compare_price}
                    <span class="compare-price">{$product.formatted_compare_price}</span>
                    <span class="discount-badge">-{$product.discount_percent}%</span>
                {/if}
                
                <span class="current-price" itemprop="price" content="{$product.price}">
                    {$product.formatted_price}
                </span>
                
                <meta itemprop="priceCurrency" content="{$wa->shop()->getCurrency()}">
                <meta itemprop="availability" content="http://schema.org/{if $product.in_stock}InStock{else}OutOfStock{/if}">
            </div>

            {* Stock Status *}
            <div class="stock-status">
                {if $product.in_stock}
                    <span class="in-stock">✓ In Stock ({$product.stock_count} available)</span>
                {else}
                    <span class="out-of-stock">⚠ Out of Stock</span>
                {/if}
            </div>

            {* Product Description *}
            {if $product.description}
                <div class="product-description" itemprop="description">
                    {$product.description}
                </div>
            {/if}

            {* Product Features *}
            {if $product.features}
                <div class="product-features">
                    <h3>Specifications</h3>
                    <dl class="features-list">
                        {foreach $product.features as $feature}
                            <dt>{$feature.name|escape}:</dt>
                            <dd>{$feature.value|escape}</dd>
                        {/foreach}
                    </dl>
                </div>
            {/if}

            {* Add to Cart Form *}
            {if $product.in_stock}
                <form class="add-to-cart-form" action="{$wa_app_url}cart/add/" method="post">
                    <input type="hidden" name="product_id" value="{$product.id}">
                    
                    <div class="quantity-selector">
                        <label for="quantity">Quantity:</label>
                        <input type="number" 
                               id="quantity" 
                               name="quantity" 
                               value="1" 
                               min="1" 
                               max="{$product.stock_count}">
                    </div>
                    
                    <button type="submit" class="btn btn-primary add-to-cart-btn">
                        Add to Cart
                    </button>
                </form>
            {/if}
        </div>
    </div>

    {* Related Products *}
    {if $product.related}
        <section class="related-products">
            <h2>Related Products</h2>
            <div class="products-grid">
                {foreach $product.related as $related}
                    {include file="shop/includes/product-card.html" product=$related}
                {/foreach}
            </div>
        </section>
    {/if}

    {* Product Reviews *}
    {if $reviews}
        <section class="product-reviews">
            <h2>Customer Reviews</h2>
            <div class="reviews-list">
                {foreach $reviews as $review}
                    <article class="review-item">
                        <header class="review-header">
                            <div class="reviewer-name">{$review.name|escape}</div>
                            <div class="review-rating">
                                {include file="shop/includes/star-rating.html" rating=$review.rating}
                            </div>
                            <time class="review-date" datetime="{$review.datetime}">
                                {$review.datetime|wa_datetime}
                            </time>
                        </header>
                        
                        <div class="review-content">
                            {$review.text|escape|nl2br}
                        </div>
                    </article>
                {/foreach}
            </div>
        </section>
    {/if}
</article>
```

## PHP Controller Patterns

### Basic Controller Structure

```php
<?php

class myappFrontendMyAction extends waViewAction
{
    public function execute()
    {
        // 1. Validate input
        $this->validateInput();
        
        // 2. Process business logic
        $data = $this->processData();
        
        // 3. Assign to template
        $this->assignToTemplate($data);
        
        // 4. Set page metadata
        $this->setPageMetadata($data);
    }
    
    private function validateInput()
    {
        // Input validation logic
    }
    
    private function processData()
    {
        // Business logic
        return $data;
    }
    
    private function assignToTemplate($data)
    {
        $this->view->assign($data);
    }
    
    private function setPageMetadata($data)
    {
        // Set title, meta tags, etc.
    }
}
```

### Advanced Controller with Error Handling

```php
<?php

class shopFrontendCategoryAction extends waViewAction
{
    public function execute()
    {
        try {
            // Get category URL parameter
            $category_url = waRequest::param('category_url');
            
            if (empty($category_url)) {
                throw new waException('Category not specified', 404);
            }
            
            // Load category
            $category = $this->loadCategory($category_url);
            
            // Load products with pagination
            $products_data = $this->loadProducts($category);
            
            // Load filters
            $filters = $this->loadFilters($category);
            
            // Assign all data
            $this->view->assign([
                'category' => $category,
                'products' => $products_data['products'],
                'pagination' => $products_data['pagination'],
                'filters' => $filters,
                'breadcrumbs' => $this->getBreadcrumbs($category),
                'sort_options' => $this->getSortOptions()
            ]);
            
            // Set SEO metadata
            $this->setSEOData($category);
            
        } catch (waException $e) {
            if ($e->getCode() == 404) {
                $this->redirect404();
            } else {
                throw $e;
            }
        }
    }
    
    private function loadCategory($url)
    {
        $category_model = new shopCategoryModel();
        $category = $category_model->getByUrl($url);
        
        if (!$category || !$category['status']) {
            throw new waException('Category not found', 404);
        }
        
        return $category;
    }
    
    private function loadProducts($category)
    {
        $limit = 20;
        $page = waRequest::get('page', 1, waRequest::TYPE_INT);
        $sort = waRequest::get('sort', 'name');
        $filters = waRequest::get('filters', [], waRequest::TYPE_ARRAY);
        
        $product_model = new shopProductModel();
        
        // Build search criteria
        $criteria = [
            'category_id' => $category['id'],
            'status' => 1
        ];
        
        // Apply filters
        if (!empty($filters)) {
            $criteria = array_merge($criteria, $this->processFilters($filters));
        }
        
        // Get total count
        $total = $product_model->countByCategory($category['id'], $criteria);
        
        // Calculate pagination
        $pagination = [
            'current_page' => $page,
            'total_pages' => ceil($total / $limit),
            'total_items' => $total,
            'items_per_page' => $limit
        ];
        
        // Load products
        $products = $product_model->getByCategory($category['id'], [
            'criteria' => $criteria,
            'limit' => $limit,
            'offset' => ($page - 1) * $limit,
            'sort' => $sort
        ]);
        
        return [
            'products' => $products,
            'pagination' => $pagination
        ];
    }
    
    private function processFilters($filters)
    {
        $criteria = [];
        
        // Price range filter
        if (!empty($filters['price'])) {
            list($min, $max) = explode('-', $filters['price']);
            $criteria['price'] = ['min' => $min, 'max' => $max];
        }
        
        // Brand filter
        if (!empty($filters['brand'])) {
            $criteria['brand_id'] = $filters['brand'];
        }
        
        // Feature filters
        if (!empty($filters['features'])) {
            $criteria['features'] = $filters['features'];
        }
        
        return $criteria;
    }
}
```

## Smarty Template Patterns

### Template Variables and Assignment

```smarty
{* Basic variable output *}
<h1>{$page_title|escape}</h1>

{* Array access *}
<div class="user-name">{$user.name|escape}</div>
<div class="user-email">{$user['email']|escape}</div>

{* Object method calls *}
<div class="user-full-name">{$user->getFullName()|escape}</div>

{* Conditional output *}
{if $user}
    Welcome, {$user.name|escape}!
{else}
    Please log in.
{/if}

{* Loop through arrays *}
{foreach $products as $product}
    <div class="product">
        <h3>{$product.name|escape}</h3>
        <p>{$product.price}</p>
    </div>
{foreachelse}
    <p>No products found.</p>
{/foreach}

{* Nested loops *}
{foreach $categories as $category}
    <div class="category">
        <h2>{$category.name|escape}</h2>
        {if $category.products}
            <ul class="products">
                {foreach $category.products as $product}
                    <li>{$product.name|escape}</li>
                {/foreach}
            </ul>
        {/if}
    </div>
{/foreach}
```

### Advanced Template Structures

```smarty
{* Template inheritance *}
{extends file="layout.html"}

{* Define blocks *}
{block name="page_title"}Product Catalog{/block}

{block name="page_content"}
    <div class="catalog-page">
        {* Include sub-templates *}
        {include file="catalog/filters.html"}
        
        <div class="products-section">
            {include file="catalog/product-grid.html" products=$products}
        </div>
        
        {* Include with parameters *}
        {include file="common/pagination.html" 
                 current_page=$pagination.current_page 
                 total_pages=$pagination.total_pages}
    </div>
{/block}

{block name="page_scripts"}
    <script src="{$wa_theme_url}js/catalog.js"></script>
{/block}
```

### Form Handling in Templates

```smarty
{* Contact form with validation *}
<form class="contact-form" action="{$wa_app_url}contact/send/" method="post">
    {* CSRF protection *}
    {$wa->csrf()}
    
    <div class="form-group {if $errors.name}error{/if}">
        <label for="contact-name">Name *</label>
        <input type="text" 
               id="contact-name" 
               name="name" 
               value="{$form_data.name|escape}" 
               required>
        {if $errors.name}
            <div class="error-message">{$errors.name|escape}</div>
        {/if}
    </div>
    
    <div class="form-group {if $errors.email}error{/if}">
        <label for="contact-email">Email *</label>
        <input type="email" 
               id="contact-email" 
               name="email" 
               value="{$form_data.email|escape}" 
               required>
        {if $errors.email}
            <div class="error-message">{$errors.email|escape}</div>
        {/if}
    </div>
    
    <div class="form-group {if $errors.message}error{/if}">
        <label for="contact-message">Message *</label>
        <textarea id="contact-message" 
                  name="message" 
                  required>{$form_data.message|escape}</textarea>
        {if $errors.message}
            <div class="error-message">{$errors.message|escape}</div>
        {/if}
    </div>
    
    <button type="submit" class="btn btn-primary">Send Message</button>
</form>
```

## Data Assignment and Variables

### Controller Data Assignment

```php
// Simple variable assignment
$this->view->assign('page_title', 'My Page Title');

// Array assignment
$this->view->assign([
    'products' => $products,
    'categories' => $categories,
    'user' => $user,
    'settings' => $settings
]);

// Object assignment
$this->view->assign('product', new shopProduct($product_id));

// Complex data structures
$this->view->assign('catalog_data', [
    'products' => [
        'items' => $products,
        'total' => $total_count,
        'pagination' => $pagination_data
    ],
    'filters' => [
        'active' => $active_filters,
        'available' => $available_filters
    ],
    'sort_options' => $sort_options
]);
```

### Template Variable Usage

```smarty
{* Access complex data structures *}
{$catalog_data.products.total} products found

{foreach $catalog_data.products.items as $product}
    <div class="product-card">
        <h3>{$product.name|escape}</h3>
        <p class="price">{$product.formatted_price}</p>
    </div>
{/foreach}

{* Check if variables exist *}
{if isset($user) && $user}
    User is logged in
{/if}

{* Default values *}
{$product.description|default:"No description available"}

{* Variable modifiers chaining *}
{$product.name|escape|truncate:50:"..."}
```

## Template Functions and Modifiers

### Built-in Smarty Modifiers

```smarty
{* String manipulation *}
{$text|escape}                    {* HTML escape *}
{$text|truncate:100:"..."}       {* Truncate text *}
{$text|upper}                    {* Uppercase *}
{$text|lower}                    {* Lowercase *}
{$text|nl2br}                    {* Convert newlines to <br> *}
{$text|strip_tags}               {* Remove HTML tags *}

{* Date formatting *}
{$timestamp|date_format:"%Y-%m-%d"}
{$timestamp|wa_datetime}         {* Webasyst date format *}
{$timestamp|wa_date}             {* Webasyst date only *}

{* Number formatting *}
{$price|number_format:2}         {* Format number *}
{$price|wa_currency}             {* Currency formatting *}

{* Array manipulation *}
{$array|count}                   {* Array count *}
{$string|split:","}              {* Split string to array *}
```

### Custom Template Functions

```php
// Register custom function in PHP
wa()->getView()->getHelper()->registerFunction('format_filesize', 'wa_format_filesize');

function wa_format_filesize($bytes)
{
    $units = ['B', 'KB', 'MB', 'GB', 'TB'];
    
    for ($i = 0; $bytes > 1024 && $i < count($units) - 1; $i++) {
        $bytes /= 1024;
    }
    
    return round($bytes, 2) . ' ' . $units[$i];
}
```

```smarty
{* Use custom function in template *}
File size: {$file.size|format_filesize}
```

### Webasyst-Specific Functions

```smarty
{* Authentication *}
{if $wa->user()}
    Welcome, {$wa->user()->getName()|escape}!
{/if}

{* Application functions *}
{$wa->app()}                     {* Current app ID *}
{$wa->apps()}                    {* Available apps *}
{$wa->getUrl()}                  {* Current URL *}

{* Currency formatting *}
{shop_currency}{$product.price}

{* Localization *}
{_w('Localization string')}      {* Translate string *}
{_wp('Product', 'Products', $count)} {* Plural forms *}

{* Asset URLs *}
{$wa_url}                        {* Base URL *}
{$wa_app_url}                    {* App URL *}
{$wa_theme_url}                  {* Theme URL *}

{* Page metadata *}
{$wa->title()}                   {* Page title *}
{$wa->meta()}                    {* Meta tags *}
{$wa->header()}                  {* Header content *}
{$wa->js()}                      {* JavaScript includes *}
```

## Event System and Hooks

### PHP Event Handling

```php
// Listen for events in controller
wa()->event('shop.product_view', $product);

// Create custom event handler
class myThemeShopHandler
{
    public function productView($params)
    {
        $product = $params['product'];
        
        // Log product view
        $this->logProductView($product);
        
        // Update view counter
        $this->updateViewCounter($product['id']);
        
        return null;
    }
    
    private function logProductView($product)
    {
        $log_model = new myThemeProductLogModel();
        $log_model->insert([
            'product_id' => $product['id'],
            'user_id' => wa()->getUser()->getId(),
            'ip_address' => waRequest::getIp(),
            'datetime' => date('Y-m-d H:i:s')
        ]);
    }
}

// Register event handler
wa()->event('shop.product_view', [new myThemeShopHandler(), 'productView']);
```

### Template Event Integration

```smarty
{* Trigger events from templates *}
{wa()->event('theme.product_display', ['product' => $product])}

{* Custom template hooks *}
<div class="product-actions">
    {* Standard add to cart button *}
    <button class="add-to-cart">Add to Cart</button>
    
    {* Allow other plugins to add buttons *}
    {wa()->event('theme.product_actions', ['product' => $product])}
</div>
```

## Advanced Integration Techniques

### AJAX Integration

**PHP AJAX Handler**
```php
<?php

class shopFrontendCartAddAction extends waJsonAction
{
    public function execute()
    {
        try {
            $product_id = waRequest::post('product_id', 0, waRequest::TYPE_INT);
            $quantity = waRequest::post('quantity', 1, waRequest::TYPE_INT);
            
            if (!$product_id) {
                throw new waException('Product ID is required');
            }
            
            // Add to cart
            $cart = new shopCart();
            $cart->addProduct($product_id, $quantity);
            
            // Return success response
            $this->response = [
                'status' => 'ok',
                'message' => 'Product added to cart',
                'cart_total' => $cart->getTotal(),
                'cart_count' => $cart->getItemsCount()
            ];
            
        } catch (Exception $e) {
            $this->setError($e->getMessage());
        }
    }
}
```

**JavaScript AJAX Call**
```javascript
// Handle add to cart form submission
$('.add-to-cart-form').on('submit', function(e) {
    e.preventDefault();
    
    var $form = $(this);
    var formData = $form.serialize();
    
    $.ajax({
        url: $form.attr('action'),
        type: 'POST',
        data: formData,
        dataType: 'json',
        beforeSend: function() {
            $form.find('button').prop('disabled', true);
        },
        success: function(response) {
            if (response.status === 'ok') {
                // Update cart widget
                updateCartWidget(response.cart_total, response.cart_count);
                
                // Show success message
                showMessage(response.message, 'success');
            } else {
                showMessage(response.errors.join(', '), 'error');
            }
        },
        error: function() {
            showMessage('An error occurred. Please try again.', 'error');
        },
        complete: function() {
            $form.find('button').prop('disabled', false);
        }
    });
});
```

### Template Caching

```smarty
{* Cache expensive template sections *}
{cache key="product_recommendations_{$product.id}" lifetime=3600}
    {* Load and display related products *}
    {foreach $related_products as $related}
        {include file="product-card.html" product=$related}
    {/foreach}
{/cache}

{* Cache with dynamic keys *}
{cache key="category_products_{$category.id}_{$page}_{$sort}" lifetime=1800}
    {* Category product listing *}
{/cache}
```

### Performance Optimization

**Lazy Loading Implementation**
```smarty
{* Lazy load product images *}
<img class="lazy-load" 
     data-src="{$product.image_url}" 
     src="{$wa_theme_url}img/placeholder.png"
     alt="{$product.name|escape}">
```

```javascript
// Lazy loading implementation
function initLazyLoading() {
    const images = document.querySelectorAll('.lazy-load');
    
    const imageObserver = new IntersectionObserver((entries, observer) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                const img = entry.target;
                img.src = img.dataset.src;
                img.classList.remove('lazy-load');
                imageObserver.unobserve(img);
            }
        });
    });
    
    images.forEach(img => imageObserver.observe(img));
}

// Initialize on DOM ready
document.addEventListener('DOMContentLoaded', initLazyLoading);
```

This comprehensive integration guide provides the foundation for creating sophisticated Webasyst themes that properly separate business logic and presentation while maintaining high performance and user experience standards.