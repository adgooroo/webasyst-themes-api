# Hypermarket Theme Analysis & Override Mechanisms

## Table of Contents
1. [Hypermarket Theme Overview](#hypermarket-theme-overview)
2. [Multi-Application Integration](#multi-application-integration)
3. [Theme Structure Analysis](#theme-structure-analysis)
4. [Override and Extension Mechanisms](#override-and-extension-mechanisms)
5. [Parent-Child Theme Relationships](#parent-child-theme-relationships)
6. [Mobile and Responsive Implementation](#mobile-and-responsive-implementation)
7. [Best Practices from Hypermarket](#best-practices-from-hypermarket)
8. [Update-Safe Development Strategies](#update-safe-development-strategies)

## Hypermarket Theme Overview

The Hypermarket theme is a comprehensive, professional theme for Webasyst that demonstrates advanced multi-application integration, responsive design, and modern eCommerce functionality. It serves as an excellent reference for theme development best practices.

### Key Features of Hypermarket

- **Multi-App Support**: Seamless integration between Site, Shop-Script, and other applications
- **Responsive Design**: Mobile-first approach with tablet and desktop optimizations
- **Modern UI/UX**: Contemporary design patterns and user interface elements
- **Performance Optimized**: Efficient code structure and asset management
- **SEO Friendly**: Built-in search engine optimization features
- **Accessibility**: WCAG compliance and screen reader support

### Hypermarket File Structure

```
hypermarket/
├── theme.xml                    # Theme manifest and metadata
├── layout.html                  # Universal layout template
├── index.html                   # Homepage template
├── css/                         # Stylesheets
│   ├── layout.css              # Layout and grid system
│   ├── components.css          # UI components
│   ├── responsive.css          # Media queries
│   └── shop.css                # Shop-specific styles
├── js/                          # JavaScript files
│   ├── theme.js                # Main theme functionality
│   ├── shop.js                 # Shopping functionality
│   ├── mobile.js               # Mobile-specific features
│   └── analytics.js            # Tracking and analytics
├── img/                         # Theme images and icons
│   ├── layout/                 # Layout images
│   ├── icons/                  # Icon sets
│   └── banners/                # Marketing banners
├── includes/                    # Reusable template components
│   ├── header.html             # Site header
│   ├── footer.html             # Site footer
│   ├── navigation.html         # Main navigation
│   ├── sidebar.html            # Sidebar components
│   └── product-card.html       # Product display card
├── shop/                        # Shop-Script specific templates
│   ├── category.html           # Category listing
│   ├── product.html            # Product details
│   ├── cart.html               # Shopping cart
│   └── checkout/               # Checkout process
└── mobile/                      # Mobile-specific templates
    ├── layout.html             # Mobile layout
    ├── navigation.html         # Mobile navigation
    └── shop/                   # Mobile shop templates
```

## Multi-Application Integration

### Universal Layout Template

**Template: `layout.html`**
```smarty
{* Universal layout supporting multiple Webasyst applications *}
<!DOCTYPE html>
<html lang="{$wa->locale()}" {if $wa->locale() == 'ar'}dir="rtl"{/if}>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    
    {* Dynamic title based on current application *}
    <title>{$wa->title()}</title>
    
    {* SEO Meta Tags *}
    <meta name="description" content="{$wa->meta('description')|escape}">
    <meta name="keywords" content="{$wa->meta('keywords')|escape}">
    
    {* Open Graph *}
    <meta property="og:title" content="{$wa->title()|escape}">
    <meta property="og:description" content="{$wa->meta('description')|escape}">
    <meta property="og:image" content="{$wa_theme_url}img/og-image.jpg">
    <meta property="og:url" content="{$wa->currentUrl()}">
    
    {* Canonical URL *}
    <link rel="canonical" href="{$wa->currentUrl()}">
    
    {* Base Stylesheets *}
    <link href="{$wa_theme_url}css/layout.css?v={$wa->version()}" rel="stylesheet">
    <link href="{$wa_theme_url}css/components.css?v={$wa->version()}" rel="stylesheet">
    <link href="{$wa_theme_url}css/responsive.css?v={$wa->version()}" rel="stylesheet">
    
    {* Application-Specific Styles *}
    {if $wa->app() == 'shop'}
        <link href="{$wa_theme_url}css/shop.css?v={$wa->version()}" rel="stylesheet">
    {/if}
    
    {if $wa->app() == 'site'}
        <link href="{$wa_theme_url}css/site.css?v={$wa->version()}" rel="stylesheet">
    {/if}
    
    {* Custom CSS from theme settings *}
    {if $theme_settings.custom_css}
        <style type="text/css">{$theme_settings.custom_css}</style>
    {/if}
    
    {* Head additions from applications *}
    {$wa->header()}
    
    {* Analytics and tracking codes *}
    {if $theme_settings.google_analytics}
        <script async src="https://www.googletagmanager.com/gtag/js?id={$theme_settings.google_analytics}"></script>
        <script>
            window.dataLayer = window.dataLayer || [];
            function gtag(){dataLayer.push(arguments);}
            gtag('js', new Date());
            gtag('config', '{$theme_settings.google_analytics}');
        </script>
    {/if}
</head>

<body class="app-{$wa->app()} {if $is_mobile}mobile{/if} {if $wa->isMobile()}touch{/if}">
    {* Skip navigation for accessibility *}
    <a href="#main-content" class="skip-nav">{_w('Skip to main content')}</a>
    
    {* Site Header *}
    <header class="site-header" role="banner">
        {include file="$wa_theme_path/includes/header.html"}
    </header>
    
    {* Main Navigation *}
    <nav class="main-navigation" role="navigation">
        {include file="$wa_theme_path/includes/navigation.html"}
    </nav>
    
    {* Breadcrumbs *}
    {if $breadcrumbs}
        <nav class="breadcrumbs" aria-label="{_w('Breadcrumb')}">
            <div class="container">
                <ol class="breadcrumb-list">
                    {foreach $breadcrumbs as $crumb}
                        <li class="breadcrumb-item {if $crumb@last}active{/if}">
                            {if !$crumb@last}
                                <a href="{$crumb.url}">{$crumb.title}</a>
                            {else}
                                <span aria-current="page">{$crumb.title}</span>
                            {/if}
                        </li>
                    {/foreach}
                </ol>
            </div>
        </nav>
    {/if}
    
    {* Main Content Area *}
    <main id="main-content" class="main-content" role="main">
        <div class="container">
            {* Application-specific content *}
            {$content}
        </div>
    </main>
    
    {* Site Footer *}
    <footer class="site-footer" role="contentinfo">
        {include file="$wa_theme_path/includes/footer.html"}
    </footer>
    
    {* Base JavaScript *}
    <script src="{$wa_theme_url}js/theme.js?v={$wa->version()}"></script>
    
    {* Application-Specific JavaScript *}
    {if $wa->app() == 'shop'}
        <script src="{$wa_theme_url}js/shop.js?v={$wa->version()}"></script>
    {/if}
    
    {* Mobile-specific JavaScript *}
    {if $wa->isMobile()}
        <script src="{$wa_theme_url}js/mobile.js?v={$wa->version()}"></script>
    {/if}
    
    {* Footer JavaScript from applications *}
    {$wa->footer()}
    
    {* Custom JavaScript from theme settings *}
    {if $theme_settings.custom_js}
        <script>{$theme_settings.custom_js}</script>
    {/if}
</body>
</html>
```

### Dynamic Navigation System

**Template: `includes/navigation.html`**
```smarty
{* Multi-application navigation with dynamic menu generation *}
<div class="navigation-container">
    <div class="container">
        <div class="nav-wrapper">
            {* Logo and Brand *}
            <div class="nav-brand">
                <a href="{$wa_url}" class="brand-link">
                    {if $theme_settings.logo}
                        <img src="{$theme_settings.logo}" alt="{$wa->accountName()}" class="brand-logo">
                    {else}
                        <span class="brand-text">{$wa->accountName()}</span>
                    {/if}
                </a>
            </div>
            
            {* Mobile Menu Toggle *}
            <button class="mobile-menu-toggle" aria-label="{_w('Toggle navigation menu')}" aria-expanded="false">
                <span class="hamburger-line"></span>
                <span class="hamburger-line"></span>
                <span class="hamburger-line"></span>
            </button>
            
            {* Main Navigation Menu *}
            <div class="nav-menu" id="mainNavigation">
                <ul class="nav-list" role="menubar">
                    {* Home Link *}
                    <li class="nav-item">
                        <a href="{$wa_url}" class="nav-link {if $wa->app() == 'site' && !$wa->currentRoute('id')}active{/if}">
                            {_w('Home')}
                        </a>
                    </li>
                    
                    {* Shop Categories (if Shop-Script is installed) *}
                    {if $wa->appExists('shop')}
                        {foreach $shop_categories as $category}
                            <li class="nav-item {if $category.childs}has-dropdown{/if}">
                                <a href="{$wa_app_url}shop/category/{$category.url}" 
                                   class="nav-link {if $current_category && $current_category.id == $category.id}active{/if}"
                                   {if $category.childs}aria-haspopup="true" aria-expanded="false"{/if}>
                                    {$category.name}
                                    {if $category.childs}<i class="dropdown-arrow"></i>{/if}
                                </a>
                                
                                {* Sub-categories dropdown *}
                                {if $category.childs}
                                    <div class="dropdown-menu" role="menu">
                                        <div class="dropdown-content">
                                            <div class="dropdown-column">
                                                {foreach $category.childs as $subcategory}
                                                    <a href="{$wa_app_url}shop/category/{$subcategory.url}" 
                                                       class="dropdown-link" role="menuitem">
                                                        {$subcategory.name}
                                                    </a>
                                                {/foreach}
                                            </div>
                                            
                                            {* Featured products from this category *}
                                            {if $category.featured_products}
                                                <div class="dropdown-column featured-products">
                                                    <h4 class="dropdown-title">{_w('Featured Products')}</h4>
                                                    {foreach $category.featured_products as $product}
                                                        <div class="featured-product">
                                                            <a href="{$wa_app_url}shop/product/{$product.url}" class="product-link">
                                                                <img src="{$product.image_url}" alt="{$product.name}" class="product-image">
                                                                <span class="product-name">{$product.name}</span>
                                                                <span class="product-price">{$product.price}</span>
                                                            </a>
                                                        </div>
                                                    {/foreach}
                                                </div>
                                            {/if}
                                        </div>
                                    </div>
                                {/if}
                            </li>
                        {/foreach}
                    {/if}
                    
                    {* Site Pages *}
                    {foreach $site_pages as $page}
                        <li class="nav-item">
                            <a href="{$page.url}" class="nav-link {if $current_page && $current_page.id == $page.id}active{/if}">
                                {$page.title}
                            </a>
                        </li>
                    {/foreach}
                    
                    {* Blog Link (if Blog app is available) *}
                    {if $wa->appExists('blog')}
                        <li class="nav-item">
                            <a href="{$wa_app_url}blog/" class="nav-link {if $wa->app() == 'blog'}active{/if}">
                                {_w('Blog')}
                            </a>
                        </li>
                    {/if}
                    
                    {* Contact Page *}
                    <li class="nav-item">
                        <a href="{$wa_url}contact/" class="nav-link">
                            {_w('Contact')}
                        </a>
                    </li>
                </ul>
            </div>
            
            {* User Account and Cart *}
            <div class="nav-actions">
                {* User Account *}
                <div class="nav-account">
                    {if $wa->user()->isAuth()}
                        <div class="account-dropdown">
                            <button class="account-toggle" aria-label="{_w('Account menu')}" aria-expanded="false">
                                <i class="icon-user"></i>
                                <span class="account-name">{$wa->user()->getName()}</span>
                                <i class="dropdown-arrow"></i>
                            </button>
                            
                            <div class="account-menu">
                                <a href="{$wa_app_url}shop/account/" class="account-link">
                                    <i class="icon-profile"></i>
                                    {_w('My Account')}
                                </a>
                                <a href="{$wa_app_url}shop/orders/" class="account-link">
                                    <i class="icon-orders"></i>
                                    {_w('Order History')}
                                </a>
                                <a href="{$wa_app_url}shop/wishlist/" class="account-link">
                                    <i class="icon-heart"></i>
                                    {_w('Wishlist')}
                                </a>
                                <div class="account-divider"></div>
                                <a href="{$wa_url}?action=logout" class="account-link logout">
                                    <i class="icon-logout"></i>
                                    {_w('Sign Out')}
                                </a>
                            </div>
                        </div>
                    {else}
                        <a href="{$wa_app_url}shop/login/" class="nav-login">
                            <i class="icon-user"></i>
                            <span>{_w('Sign In')}</span>
                        </a>
                    {/if}
                </div>
                
                {* Shopping Cart *}
                {if $wa->appExists('shop')}
                    <div class="nav-cart">
                        <a href="{$wa_app_url}shop/cart/" class="cart-link" id="cartLink">
                            <i class="icon-cart"></i>
                            <span class="cart-text">{_w('Cart')}</span>
                            <span class="cart-count" id="cartCount">{$cart.count|default:0}</span>
                        </a>
                        
                        {* Mini cart dropdown *}
                        <div class="cart-dropdown" id="cartDropdown">
                            <div class="cart-dropdown-content">
                                {* Mini cart content loaded via AJAX *}
                                <div id="miniCartContent">
                                    {include file="$wa_theme_path/includes/mini-cart.html"}
                                </div>
                            </div>
                        </div>
                    </div>
                {/if}
                
                {* Search Toggle *}
                <button class="search-toggle" aria-label="{_w('Open search')}" id="searchToggle">
                    <i class="icon-search"></i>
                </button>
            </div>
        </div>
        
        {* Search Bar *}
        <div class="search-bar" id="searchBar">
            <form action="{$wa_app_url}shop/search/" method="get" class="search-form">
                <div class="search-input-group">
                    <input type="text" name="query" placeholder="{_w('Search products...')}" 
                           class="search-input" autocomplete="off" id="searchInput">
                    <button type="submit" class="search-submit">
                        <i class="icon-search"></i>
                        <span class="sr-only">{_w('Search')}</span>
                    </button>
                </div>
                
                {* Search suggestions dropdown *}
                <div class="search-suggestions" id="searchSuggestions">
                    {* Populated via AJAX *}
                </div>
            </form>
        </div>
    </div>
</div>
```

## Theme Structure Analysis

### Theme Manifest (theme.xml)

```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="hypermarket" app="*" type="template" vendor="webasyst">
    <name locale="en_US">Hypermarket</name>
    <name locale="ru_RU">Гипермаркет</name>
    
    <description locale="en_US">
        Modern responsive eCommerce theme with multi-application support
    </description>
    <description locale="ru_RU">
        Современная адаптивная тема для интернет-магазина с поддержкой множества приложений
    </description>
    
    <version>2.1.0</version>
    <vendor>Webasyst</vendor>
    <author>Webasyst Team</author>
    <author_email>support@webasyst.com</author_email>
    
    <requirements>
        <app version=">=2.0">shop</app>
        <app version=">=2.0" optional="true">site</app>
        <app version=">=1.0" optional="true">blog</app>
    </requirements>
    
    <!-- Theme settings for customization -->
    <settings>
        <setting var="logo" type="image">
            <name locale="en_US">Logo</name>
            <name locale="ru_RU">Логотип</name>
            <description locale="en_US">Upload your logo image</description>
        </setting>
        
        <setting var="color_scheme" type="select" values="blue,green,orange,red">
            <name locale="en_US">Color scheme</name>
            <name locale="ru_RU">Цветовая схема</name>
            <value>blue</value>
        </setting>
        
        <setting var="layout_width" type="select" values="fixed,fluid">
            <name locale="en_US">Layout width</name>
            <name locale="ru_RU">Ширина макета</name>
            <value>fixed</value>
        </setting>
        
        <setting var="show_breadcrumbs" type="checkbox">
            <name locale="en_US">Show breadcrumbs</name>
            <name locale="ru_RU">Показывать хлебные крошки</name>
            <value>1</value>
        </setting>
        
        <setting var="products_per_page" type="number">
            <name locale="en_US">Products per page</name>
            <name locale="ru_RU">Товаров на странице</name>
            <value>12</value>
        </setting>
        
        <setting var="custom_css" type="textarea">
            <name locale="en_US">Custom CSS</name>
            <name locale="ru_RU">Дополнительные CSS</name>
        </setting>
        
        <setting var="custom_js" type="textarea">
            <name locale="en_US">Custom JavaScript</name>
            <name locale="ru_RU">Дополнительный JavaScript</name>
        </setting>
        
        <setting var="google_analytics" type="text">
            <name locale="en_US">Google Analytics ID</name>
            <name locale="ru_RU">ID Google Analytics</name>
        </setting>
        
        <setting var="facebook_pixel" type="text">
            <name locale="en_US">Facebook Pixel ID</name>
            <name locale="ru_RU">ID Facebook Pixel</name>
        </setting>
    </settings>
    
    <!-- Screenshots for theme preview -->
    <screenshots>
        <screenshot>img/screenshots/homepage.jpg</screenshot>
        <screenshot>img/screenshots/category.jpg</screenshot>
        <screenshot>img/screenshots/product.jpg</screenshot>
        <screenshot>img/screenshots/mobile.jpg</screenshot>
    </screenshots>
</theme>
```

### CSS Architecture

**File: `css/layout.css`**
```css
/* Hypermarket Theme - Layout Styles */

/* CSS Variables for theme customization */
:root {
    --primary-color: #2c5aa0;
    --secondary-color: #f8f9fa;
    --accent-color: #ff6b35;
    --text-color: #333333;
    --text-muted: #6c757d;
    --border-color: #dee2e6;
    --success-color: #28a745;
    --warning-color: #ffc107;
    --error-color: #dc3545;
    
    /* Typography */
    --font-family-base: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    --font-family-headings: 'Inter', sans-serif;
    --font-size-base: 16px;
    --line-height-base: 1.5;
    
    /* Spacing */
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    --spacing-xl: 3rem;
    
    /* Layout */
    --container-max-width: 1200px;
    --header-height: 80px;
    --mobile-header-height: 60px;
    
    /* Breakpoints */
    --breakpoint-sm: 576px;
    --breakpoint-md: 768px;
    --breakpoint-lg: 992px;
    --breakpoint-xl: 1200px;
}

/* Color scheme variations */
.theme-blue {
    --primary-color: #2c5aa0;
    --accent-color: #ff6b35;
}

.theme-green {
    --primary-color: #28a745;
    --accent-color: #ff6b35;
}

.theme-orange {
    --primary-color: #fd7e14;
    --accent-color: #495057;
}

.theme-red {
    --primary-color: #dc3545;
    --accent-color: #28a745;
}

/* Base styles */
* {
    box-sizing: border-box;
}

html {
    font-size: var(--font-size-base);
    line-height: var(--line-height-base);
}

body {
    font-family: var(--font-family-base);
    color: var(--text-color);
    background-color: #ffffff;
    margin: 0;
    padding: 0;
}

/* Container system */
.container {
    width: 100%;
    max-width: var(--container-max-width);
    margin: 0 auto;
    padding: 0 var(--spacing-md);
}

.container-fluid {
    width: 100%;
    padding: 0 var(--spacing-md);
}

/* Fluid layout option */
body.layout-fluid .container {
    max-width: none;
    padding: 0 var(--spacing-lg);
}

/* Grid system */
.row {
    display: flex;
    flex-wrap: wrap;
    margin: 0 calc(-1 * var(--spacing-md) / 2);
}

.col,
.col-1, .col-2, .col-3, .col-4, .col-5, .col-6,
.col-7, .col-8, .col-9, .col-10, .col-11, .col-12 {
    position: relative;
    width: 100%;
    padding: 0 calc(var(--spacing-md) / 2);
}

.col { flex: 1 0 0%; }
.col-1 { flex: 0 0 8.333333%; max-width: 8.333333%; }
.col-2 { flex: 0 0 16.666667%; max-width: 16.666667%; }
.col-3 { flex: 0 0 25%; max-width: 25%; }
.col-4 { flex: 0 0 33.333333%; max-width: 33.333333%; }
.col-5 { flex: 0 0 41.666667%; max-width: 41.666667%; }
.col-6 { flex: 0 0 50%; max-width: 50%; }
.col-7 { flex: 0 0 58.333333%; max-width: 58.333333%; }
.col-8 { flex: 0 0 66.666667%; max-width: 66.666667%; }
.col-9 { flex: 0 0 75%; max-width: 75%; }
.col-10 { flex: 0 0 83.333333%; max-width: 83.333333%; }
.col-11 { flex: 0 0 91.666667%; max-width: 91.666667%; }
.col-12 { flex: 0 0 100%; max-width: 100%; }

/* Site header */
.site-header {
    background: #ffffff;
    border-bottom: 1px solid var(--border-color);
    position: sticky;
    top: 0;
    z-index: 1000;
    height: var(--header-height);
}

/* Main content */
.main-content {
    min-height: calc(100vh - var(--header-height) - 200px);
    padding: var(--spacing-lg) 0;
}

/* Site footer */
.site-footer {
    background: var(--secondary-color);
    border-top: 1px solid var(--border-color);
    padding: var(--spacing-xl) 0 var(--spacing-lg);
    margin-top: var(--spacing-xl);
}

/* Accessibility */
.skip-nav {
    position: absolute;
    top: -40px;
    left: 6px;
    background: var(--primary-color);
    color: white;
    padding: 8px;
    text-decoration: none;
    border-radius: 0 0 4px 4px;
    z-index: 1001;
}

.skip-nav:focus {
    top: 0;
}

.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
}

/* Print styles */
@media print {
    .site-header,
    .main-navigation,
    .site-footer,
    .nav-actions,
    .cart-dropdown,
    .search-bar {
        display: none !important;
    }
    
    .main-content {
        min-height: auto;
        padding: 0;
    }
    
    a[href]:after {
        content: " (" attr(href) ")";
    }
    
    img {
        max-width: 100% !important;
    }
}
```

## Override and Extension Mechanisms

### Template Override System

Webasyst provides multiple levels of template override to ensure update-safe customizations:

1. **System Level**: `/wa-apps/{app}/themes/{theme}/`
2. **User Level**: `/wa-data/public/{domain}/themes/{theme}/`
3. **Custom Level**: Custom theme inheritance

### Safe Override Practices

**Directory Structure for Overrides:**
```
wa-data/public/example.com/themes/hypermarket/
├── css/
│   └── custom.css              # Additional styles
├── js/
│   └── custom.js               # Additional JavaScript
├── includes/
│   └── header.html             # Override header component
├── shop/
│   ├── product.html            # Override product template
│   └── category.html           # Override category template
└── layout.html                 # Override main layout
```

### Theme Inheritance Example

**PHP Class: `lib/HypermarketTheme.class.php`**
```php
<?php

class HypermarketTheme extends waTheme
{
    protected $app = '*'; // Support all applications
    
    public function __construct($app = null)
    {
        parent::__construct($app);
        $this->setupThemeVariables();
        $this->registerEventHandlers();
    }
    
    /**
     * Setup theme-specific variables
     */
    protected function setupThemeVariables()
    {
        // Get theme settings
        $settings = $this->getSettings();
        
        // Assign theme variables to view
        $this->view->assign('theme_settings', $settings);
        $this->view->assign('color_scheme', $settings['color_scheme'] ?? 'blue');
        $this->view->assign('layout_width', $settings['layout_width'] ?? 'fixed');
        
        // Mobile detection
        $this->view->assign('is_mobile', wa()->isMobile());
        
        // Current application info
        $this->view->assign('current_app', array(
            'id' => wa()->app(),
            'name' => wa()->appName(),
            'url' => wa()->appUrl()
        ));
    }
    
    /**
     * Register event handlers for theme customization
     */
    protected function registerEventHandlers()
    {
        // Hook into head section
        wa()->event('frontend_head', array($this, 'frontendHead'));
        
        // Hook into body classes
        wa()->event('frontend_body_class', array($this, 'frontendBodyClass'));
        
        // Hook into navigation
        wa()->event('frontend_nav', array($this, 'frontendNav'));
    }
    
    /**
     * Add custom CSS and JavaScript to head
     */
    public function frontendHead()
    {
        $settings = $this->getSettings();
        $output = '';
        
        // Color scheme CSS
        if (!empty($settings['color_scheme']) && $settings['color_scheme'] !== 'blue') {
            $output .= '<link href="' . $this->getUrl() . 'css/schemes/' . 
                      $settings['color_scheme'] . '.css" rel="stylesheet">' . "\n";
        }
        
        // Custom CSS
        if (!empty($settings['custom_css'])) {
            $output .= '<style type="text/css">' . $settings['custom_css'] . '</style>' . "\n";
        }
        
        // Facebook Pixel
        if (!empty($settings['facebook_pixel'])) {
            $output .= $this->getFacebookPixelCode($settings['facebook_pixel']);
        }
        
        return $output;
    }
    
    /**
     * Add custom body classes
     */
    public function frontendBodyClass()
    {
        $settings = $this->getSettings();
        $classes = array();
        
        // Color scheme class
        $classes[] = 'theme-' . ($settings['color_scheme'] ?? 'blue');
        
        // Layout width class
        $classes[] = 'layout-' . ($settings['layout_width'] ?? 'fixed');
        
        // Application-specific class
        $classes[] = 'app-' . wa()->app();
        
        // Mobile class
        if (wa()->isMobile()) {
            $classes[] = 'mobile';
        }
        
        return implode(' ', $classes);
    }
    
    /**
     * Customize navigation menu
     */
    public function frontendNav()
    {
        $nav_items = array();
        
        // Get shop categories for navigation
        if (wa()->appExists('shop')) {
            $nav_items['shop_categories'] = $this->getShopCategories();
        }
        
        // Get site pages for navigation
        if (wa()->appExists('site')) {
            $nav_items['site_pages'] = $this->getSitePages();
        }
        
        return $nav_items;
    }
    
    /**
     * Get shop categories for navigation
     */
    protected function getShopCategories()
    {
        if (!wa()->appExists('shop')) {
            return array();
        }
        
        try {
            $category_model = new shopCategoryModel();
            $categories = $category_model->getTree(0, 2); // 2 levels deep
            
            // Add featured products to categories
            foreach ($categories as &$category) {
                if ($category['count'] > 0) {
                    $category['featured_products'] = $this->getFeaturedProducts($category['id'], 3);
                }
            }
            
            return $categories;
        } catch (Exception $e) {
            return array();
        }
    }
    
    /**
     * Get featured products for a category
     */
    protected function getFeaturedProducts($category_id, $limit = 3)
    {
        if (!wa()->appExists('shop')) {
            return array();
        }
        
        try {
            $product_model = new shopProductModel();
            $products = $product_model->select('id, name, url, price, currency')
                ->where('status = 1 AND category_id = ?', $category_id)
                ->order('sort ASC, id DESC')
                ->limit($limit)
                ->fetchAll();
            
            // Add image URLs
            foreach ($products as &$product) {
                $product['image_url'] = shopProductImagesModel::getImageUrl(
                    $product, shopProductImagesModel::SIZE_THUMB
                );
                $product['price'] = wa_currency($product['price'], $product['currency']);
            }
            
            return $products;
        } catch (Exception $e) {
            return array();
        }
    }
    
    /**
     * Get site pages for navigation
     */
    protected function getSitePages()
    {
        if (!wa()->appExists('site')) {
            return array();
        }
        
        try {
            $page_model = new sitePageModel();
            $pages = $page_model->select('id, name as title, url, parent_id')
                ->where('status = 1 AND parent_id = 0')
                ->order('sort ASC')
                ->fetchAll();
            
            return $pages;
        } catch (Exception $e) {
            return array();
        }
    }
    
    /**
     * Get Facebook Pixel tracking code
     */
    protected function getFacebookPixelCode($pixel_id)
    {
        return '
        <!-- Facebook Pixel Code -->
        <script>
        !function(f,b,e,v,n,t,s)
        {if(f.fbq)return;n=f.fbq=function(){n.callMethod?
        n.callMethod.apply(n,arguments):n.queue.push(arguments)};
        if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version=\'2.0\';
        n.queue=[];t=b.createElement(e);t.async=!0;
        t.src=v;s=b.getElementsByTagName(e)[0];
        s.parentNode.insertBefore(t,s)}(window, document,\'script\',
        \'https://connect.facebook.net/en_US/fbevents.js\');
        fbq(\'init\', \'' . $pixel_id . '\');
        fbq(\'track\', \'PageView\');
        </script>
        <noscript><img height="1" width="1" style="display:none"
        src="https://www.facebook.com/tr?id=' . $pixel_id . '&ev=PageView&noscript=1"
        /></noscript>
        <!-- End Facebook Pixel Code -->
        ';
    }
    
    /**
     * Get theme settings
     */
    public function getSettings()
    {
        return wa()->storage()->read('theme_settings') ?: array();
    }
    
    /**
     * Save theme settings
     */
    public function saveSettings($settings)
    {
        return wa()->storage()->write('theme_settings', $settings);
    }
}
```

## Parent-Child Theme Relationships

### Child Theme Creation

**Child Theme Manifest: `child-theme/theme.xml`**
```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="hypermarket-child" app="*" type="template" vendor="custom" parent="hypermarket">
    <name locale="en_US">Hypermarket Child</name>
    <name locale="ru_RU">Дочерняя тема Гипермаркет</name>
    
    <description locale="en_US">
        Custom child theme based on Hypermarket
    </description>
    
    <version>1.0.0</version>
    <vendor>Custom Developer</vendor>
    <author>Your Name</author>
    <author_email>your@email.com</author_email>
    
    <!-- Inherit parent theme requirements -->
    <parent_theme>hypermarket</parent_theme>
    
    <!-- Additional settings specific to child theme -->
    <settings>
        <setting var="custom_feature" type="checkbox">
            <name locale="en_US">Enable custom feature</name>
            <value>0</value>
        </setting>
    </settings>
</theme>
```

### Child Theme PHP Class

**File: `child-theme/lib/HypermarketChildTheme.class.php`**
```php
<?php

class HypermarketChildTheme extends HypermarketTheme
{
    public function __construct($app = null)
    {
        parent::__construct($app);
        $this->setupChildThemeFeatures();
    }
    
    /**
     * Setup child theme specific features
     */
    protected function setupChildThemeFeatures()
    {
        $settings = $this->getSettings();
        
        if (!empty($settings['custom_feature'])) {
            $this->enableCustomFeature();
        }
    }
    
    /**
     * Override parent theme navigation
     */
    public function frontendNav()
    {
        $nav_items = parent::frontendNav();
        
        // Add custom navigation items
        $nav_items['custom_pages'] = $this->getCustomPages();
        
        return $nav_items;
    }
    
    /**
     * Add custom feature functionality
     */
    protected function enableCustomFeature()
    {
        // Add custom event handlers
        wa()->event('frontend_custom_feature', array($this, 'customFeatureHandler'));
    }
    
    /**
     * Custom feature event handler
     */
    public function customFeatureHandler()
    {
        // Custom functionality implementation
        return '<div class="custom-feature">Custom content</div>';
    }
    
    /**
     * Get custom pages for child theme
     */
    protected function getCustomPages()
    {
        return array(
            array(
                'id' => 'custom-page',
                'title' => 'Custom Page',
                'url' => '/custom-page/'
            )
        );
    }
}
```

## Mobile and Responsive Implementation

### Mobile-Specific Templates

**Template: `mobile/layout.html`**
```smarty
{* Mobile-optimized layout template *}
<!DOCTYPE html>
<html lang="{$wa->locale()}" class="mobile">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
    
    <title>{$wa->title()}</title>
    
    {* Mobile-optimized CSS *}
    <link href="{$wa_theme_url}css/mobile.css?v={$wa->version()}" rel="stylesheet">
    
    {* Touch icons *}
    <link rel="apple-touch-icon" sizes="180x180" href="{$wa_theme_url}img/icons/apple-touch-icon.png">
    <link rel="icon" type="image/png" sizes="32x32" href="{$wa_theme_url}img/icons/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="{$wa_theme_url}img/icons/favicon-16x16.png">
    
    {* PWA manifest *}
    <link rel="manifest" href="{$wa_theme_url}manifest.json">
    <meta name="theme-color" content="{$theme_settings.primary_color|default:'#2c5aa0'}">
    
    {$wa->header()}
</head>

<body class="mobile-layout">
    {* Mobile header *}
    <header class="mobile-header">
        <div class="mobile-header-content">
            <button class="mobile-menu-btn" id="mobileMenuBtn">
                <span class="hamburger"></span>
            </button>
            
            <div class="mobile-logo">
                <a href="{$wa_url}">
                    {if $theme_settings.logo}
                        <img src="{$theme_settings.logo}" alt="{$wa->accountName()}">
                    {else}
                        {$wa->accountName()}
                    {/if}
                </a>
            </div>
            
            <div class="mobile-actions">
                <button class="mobile-search-btn" id="mobileSearchBtn">
                    <i class="icon-search"></i>
                </button>
                
                {if $wa->appExists('shop')}
                    <a href="{$wa_app_url}shop/cart/" class="mobile-cart-btn">
                        <i class="icon-cart"></i>
                        <span class="cart-count">{$cart.count|default:0}</span>
                    </a>
                {/if}
            </div>
        </div>
        
        {* Mobile search bar *}
        <div class="mobile-search" id="mobileSearch">
            <form action="{$wa_app_url}shop/search/" method="get">
                <input type="text" name="query" placeholder="{_w('Search...')}" class="mobile-search-input">
                <button type="submit" class="mobile-search-submit">
                    <i class="icon-search"></i>
                </button>
            </form>
        </div>
    </header>
    
    {* Mobile navigation menu *}
    <nav class="mobile-nav" id="mobileNav">
        {include file="$wa_theme_path/mobile/navigation.html"}
    </nav>
    
    {* Main content *}
    <main class="mobile-content">
        {$content}
    </main>
    
    {* Mobile footer *}
    <footer class="mobile-footer">
        {include file="$wa_theme_path/mobile/footer.html"}
    </footer>
    
    {* Mobile-specific JavaScript *}
    <script src="{$wa_theme_url}js/mobile.js?v={$wa->version()}"></script>
    
    {$wa->footer()}
</body>
</html>
```

### Responsive CSS Implementation

**File: `css/responsive.css`**
```css
/* Hypermarket Theme - Responsive Styles */

/* Mobile First Approach */

/* Extra small devices (phones, less than 576px) */
@media (max-width: 575.98px) {
    .container {
        padding: 0 var(--spacing-sm);
    }
    
    .site-header {
        height: var(--mobile-header-height);
    }
    
    .main-content {
        min-height: calc(100vh - var(--mobile-header-height) - 150px);
        padding: var(--spacing-md) 0;
    }
    
    /* Hide desktop navigation */
    .nav-menu {
        display: none;
    }
    
    /* Show mobile menu toggle */
    .mobile-menu-toggle {
        display: block;
    }
    
    /* Stack columns on mobile */
    .row {
        margin: 0;
    }
    
    .col,
    [class*="col-"] {
        flex: 0 0 100%;
        max-width: 100%;
        padding: 0;
        margin-bottom: var(--spacing-md);
    }
    
    /* Mobile navigation */
    .nav-menu.mobile-open {
        position: fixed;
        top: var(--mobile-header-height);
        left: 0;
        right: 0;
        bottom: 0;
        background: rgba(0, 0, 0, 0.9);
        z-index: 9999;
        display: block;
        overflow-y: auto;
    }
    
    .nav-list {
        flex-direction: column;
        padding: var(--spacing-lg);
    }
    
    .nav-item {
        width: 100%;
        border-bottom: 1px solid rgba(255, 255, 255, 0.1);
    }
    
    .nav-link {
        display: block;
        padding: var(--spacing-md) 0;
        color: white;
        font-size: 1.1rem;
    }
    
    /* Mobile dropdown menus */
    .dropdown-menu {
        position: static;
        display: block;
        background: rgba(255, 255, 255, 0.1);
        border: none;
        box-shadow: none;
        margin-top: 0;
    }
    
    .dropdown-link {
        padding: var(--spacing-sm) var(--spacing-md);
        color: rgba(255, 255, 255, 0.8);
    }
    
    /* Mobile search */
    .search-bar {
        position: fixed;
        top: var(--mobile-header-height);
        left: 0;
        right: 0;
        background: white;
        border-bottom: 1px solid var(--border-color);
        z-index: 999;
        transform: translateY(-100%);
        transition: transform 0.3s ease;
    }
    
    .search-bar.active {
        transform: translateY(0);
    }
    
    /* Mobile cart dropdown */
    .cart-dropdown {
        position: fixed;
        top: var(--mobile-header-height);
        left: 0;
        right: 0;
        background: white;
        max-height: 50vh;
        overflow-y: auto;
        z-index: 998;
    }
}

/* Small devices (landscape phones, 576px and up) */
@media (min-width: 576px) {
    .container {
        max-width: 540px;
    }
    
    /* Show 2 columns for some content */
    .col-sm-6 {
        flex: 0 0 50%;
        max-width: 50%;
    }
    
    .col-sm-4 {
        flex: 0 0 33.333333%;
        max-width: 33.333333%;
    }
    
    .col-sm-3 {
        flex: 0 0 25%;
        max-width: 25%;
    }
}

/* Medium devices (tablets, 768px and up) */
@media (min-width: 768px) {
    .container {
        max-width: 720px;
    }
    
    /* Show desktop navigation */
    .mobile-menu-toggle {
        display: none;
    }
    
    .nav-menu {
        display: flex;
    }
    
    /* Tablet-specific columns */
    .col-md-1 { flex: 0 0 8.333333%; max-width: 8.333333%; }
    .col-md-2 { flex: 0 0 16.666667%; max-width: 16.666667%; }
    .col-md-3 { flex: 0 0 25%; max-width: 25%; }
    .col-md-4 { flex: 0 0 33.333333%; max-width: 33.333333%; }
    .col-md-5 { flex: 0 0 41.666667%; max-width: 41.666667%; }
    .col-md-6 { flex: 0 0 50%; max-width: 50%; }
    .col-md-7 { flex: 0 0 58.333333%; max-width: 58.333333%; }
    .col-md-8 { flex: 0 0 66.666667%; max-width: 66.666667%; }
    .col-md-9 { flex: 0 0 75%; max-width: 75%; }
    .col-md-10 { flex: 0 0 83.333333%; max-width: 83.333333%; }
    .col-md-11 { flex: 0 0 91.666667%; max-width: 91.666667%; }
    .col-md-12 { flex: 0 0 100%; max-width: 100%; }
    
    /* Dropdown menus */
    .dropdown-menu {
        position: absolute;
        top: 100%;
        left: 0;
        background: white;
        border: 1px solid var(--border-color);
        border-radius: 4px;
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        min-width: 200px;
        z-index: 1000;
    }
    
    .dropdown-menu.mega-menu {
        min-width: 600px;
        left: 50%;
        transform: translateX(-50%);
    }
}

/* Large devices (desktops, 992px and up) */
@media (min-width: 992px) {
    .container {
        max-width: 960px;
    }
    
    /* Desktop-specific columns */
    .col-lg-1 { flex: 0 0 8.333333%; max-width: 8.333333%; }
    .col-lg-2 { flex: 0 0 16.666667%; max-width: 16.666667%; }
    .col-lg-3 { flex: 0 0 25%; max-width: 25%; }
    .col-lg-4 { flex: 0 0 33.333333%; max-width: 33.333333%; }
    .col-lg-5 { flex: 0 0 41.666667%; max-width: 41.666667%; }
    .col-lg-6 { flex: 0 0 50%; max-width: 50%; }
    .col-lg-7 { flex: 0 0 58.333333%; max-width: 58.333333%; }
    .col-lg-8 { flex: 0 0 66.666667%; max-width: 66.666667%; }
    .col-lg-9 { flex: 0 0 75%; max-width: 75%; }
    .col-lg-10 { flex: 0 0 83.333333%; max-width: 83.333333%; }
    .col-lg-11 { flex: 0 0 91.666667%; max-width: 91.666667%; }
    .col-lg-12 { flex: 0 0 100%; max-width: 100%; }
    
    /* Full mega menu support */
    .mega-menu .dropdown-content {
        display: flex;
        flex-wrap: wrap;
        padding: var(--spacing-lg);
    }
    
    .mega-menu .dropdown-column {
        flex: 1;
        min-width: 200px;
        padding: 0 var(--spacing-md);
    }
    
    .mega-menu .featured-products {
        border-left: 1px solid var(--border-color);
    }
}

/* Extra large devices (large desktops, 1200px and up) */
@media (min-width: 1200px) {
    .container {
        max-width: var(--container-max-width);
    }
    
    /* Extra large columns */
    .col-xl-1 { flex: 0 0 8.333333%; max-width: 8.333333%; }
    .col-xl-2 { flex: 0 0 16.666667%; max-width: 16.666667%; }
    .col-xl-3 { flex: 0 0 25%; max-width: 25%; }
    .col-xl-4 { flex: 0 0 33.333333%; max-width: 33.333333%; }
    .col-xl-5 { flex: 0 0 41.666667%; max-width: 41.666667%; }
    .col-xl-6 { flex: 0 0 50%; max-width: 50%; }
    .col-xl-7 { flex: 0 0 58.333333%; max-width: 58.333333%; }
    .col-xl-8 { flex: 0 0 66.666667%; max-width: 66.666667%; }
    .col-xl-9 { flex: 0 0 75%; max-width: 75%; }
    .col-xl-10 { flex: 0 0 83.333333%; max-width: 83.333333%; }
    .col-xl-11 { flex: 0 0 91.666667%; max-width: 91.666667%; }
    .col-xl-12 { flex: 0 0 100%; max-width: 100%; }
}

/* High DPI displays */
@media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi) {
    /* High resolution image replacements */
    .brand-logo {
        image-rendering: -webkit-optimize-contrast;
        image-rendering: crisp-edges;
    }
}

/* Landscape orientation specific styles */
@media (orientation: landscape) and (max-height: 500px) {
    .mobile-nav {
        max-height: 80vh;
        overflow-y: auto;
    }
    
    .search-suggestions {
        max-height: 40vh;
        overflow-y: auto;
    }
}

/* Reduced motion preferences */
@media (prefers-reduced-motion: reduce) {
    *,
    *::before,
    *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
    }
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
    :root {
        --text-color: #ffffff;
        --text-muted: #cccccc;
        --secondary-color: #2d2d2d;
        --border-color: #404040;
    }
    
    body {
        background-color: #1a1a1a;
        color: var(--text-color);
    }
    
    .site-header,
    .site-footer {
        background: #2d2d2d;
        border-color: var(--border-color);
    }
}
```

## Best Practices from Hypermarket

### Performance Optimization

1. **CSS and JavaScript Optimization**:
   - Minification and compression
   - Critical CSS inlining
   - Lazy loading for non-critical resources
   - CDN integration for static assets

2. **Image Optimization**:
   - WebP format support with fallbacks
   - Responsive images with srcset
   - Lazy loading implementation
   - Proper alt attributes for accessibility

3. **Caching Strategies**:
   - Browser caching headers
   - Template compilation caching
   - Database query optimization
   - CDN caching for static resources

### SEO Implementation

1. **Structured Data**:
   - JSON-LD structured data
   - Product markup for eCommerce
   - Breadcrumb navigation markup
   - Organization and business information

2. **Meta Tag Management**:
   - Dynamic title generation
   - Meta descriptions from content
   - Open Graph tags for social sharing
   - Canonical URLs to prevent duplication

3. **Performance Metrics**:
   - Core Web Vitals optimization
   - First Contentful Paint (FCP)
   - Largest Contentful Paint (LCP)
   - Cumulative Layout Shift (CLS)

## Update-Safe Development Strategies

### Version Control Integration

**Git Structure Example:**
```
theme-development/
├── src/                        # Source files
│   ├── scss/                   # SCSS source files
│   ├── js/                     # JavaScript source files
│   └── templates/              # Template source files
├── dist/                       # Compiled theme files
│   ├── css/
│   ├── js/
│   └── templates/
├── tools/                      # Build tools
│   ├── webpack.config.js
│   ├── package.json
│   └── gulpfile.js
└── README.md
```

### Build Process Automation

**Package.json Configuration:**
```json
{
  "name": "hypermarket-child-theme",
  "version": "1.0.0",
  "scripts": {
    "dev": "webpack --mode development --watch",
    "build": "webpack --mode production",
    "deploy": "npm run build && ./deploy.sh"
  },
  "devDependencies": {
    "webpack": "^5.0.0",
    "webpack-cli": "^4.0.0",
    "sass-loader": "^12.0.0",
    "css-loader": "^6.0.0",
    "mini-css-extract-plugin": "^2.0.0",
    "terser-webpack-plugin": "^5.0.0"
  }
}
```

### Deployment Strategy

**Deployment Script Example:**
```bash
#!/bin/bash
# deploy.sh - Safe theme deployment script

# Build production assets
npm run build

# Create backup of current theme
cp -r /wa-data/public/domain.com/themes/hypermarket-child \
      /backups/themes/hypermarket-child-$(date +%Y%m%d_%H%M%S)

# Deploy new theme files
rsync -av --exclude='.git' --exclude='node_modules' \
      dist/ /wa-data/public/domain.com/themes/hypermarket-child/

# Clear Webasyst cache
php wa.php cache:clear

echo "Theme deployed successfully!"
```

This comprehensive analysis of the Hypermarket theme provides deep insights into professional theme development practices, multi-application integration patterns, and update-safe customization strategies that can be applied to any Webasyst theme development project.