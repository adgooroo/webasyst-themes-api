# Webasyst Theme Template Structure and Organization

## Table of Contents
1. [Template Structure Overview](#template-structure-overview)
2. [Directory Organization](#directory-organization)
3. [File Naming Conventions](#file-naming-conventions)
4. [Template Hierarchy](#template-hierarchy)
5. [Asset Management](#asset-management)
6. [Theme Manifest Configuration](#theme-manifest-configuration)
7. [Multi-Application Template Organization](#multi-application-template-organization)

## Template Structure Overview

Webasyst theme templates follow a hierarchical structure that allows for maximum flexibility while maintaining consistency across applications. Understanding this structure is crucial for creating maintainable and extensible themes.

### Core Template Types

1. **Layout Templates**: Define the overall page structure
2. **Page Templates**: Handle specific page content
3. **Block Templates**: Reusable components and widgets
4. **Include Templates**: Shared template fragments
5. **Application-Specific Templates**: Custom templates for specific apps

### Template File Extensions

- `.html` - Smarty template files
- `.css` - Stylesheets
- `.js` - JavaScript files
- `.xml` - Configuration and manifest files

## Directory Organization

### Standard Theme Directory Structure

```
theme-name/
├── theme.xml                    # Theme manifest
├── layout.html                  # Main layout template
├── index.html                   # Homepage template
├── css/                         # Stylesheets
│   ├── style.css               # Main theme styles
│   ├── layout.css              # Layout-specific styles
│   ├── responsive.css          # Mobile responsive styles
│   └── print.css               # Print styles
├── js/                          # JavaScript files
│   ├── theme.js                # Main theme functionality
│   ├── navigation.js           # Navigation interactions
│   └── forms.js                # Form enhancements
├── img/                         # Theme images
│   ├── backgrounds/            # Background images
│   ├── icons/                  # Icon files
│   ├── logos/                  # Logo variations
│   └── ui/                     # UI elements
├── includes/                    # Shared template includes
│   ├── header.html             # Site header
│   ├── footer.html             # Site footer
│   ├── navigation.html         # Main navigation
│   ├── sidebar.html            # Sidebar content
│   └── meta.html               # Meta tags template
├── blocks/                      # Reusable content blocks
│   ├── auth/                   # Authentication blocks
│   ├── user/                   # User-related blocks
│   ├── forms/                  # Form templates
│   └── widgets/                # Widget templates
├── pages/                       # Custom page templates
│   ├── contact.html            # Contact page
│   ├── about.html              # About page
│   └── error.html              # Error pages
└── app-specific/                # Application-specific templates
    ├── shop/                   # Shop-Script templates
    ├── crm/                    # CRM templates
    ├── helpdesk/               # Helpdesk templates
    ├── mailer/                 # Mailer templates
    └── hub/                    # Hub templates
```

### Advanced Directory Structure for Complex Themes

```
theme-name/
├── theme.xml
├── layout.html
├── assets/                      # Organized asset management
│   ├── css/
│   │   ├── base/               # Base styles
│   │   │   ├── reset.css
│   │   │   ├── typography.css
│   │   │   └── variables.css
│   │   ├── components/         # Component styles
│   │   │   ├── buttons.css
│   │   │   ├── forms.css
│   │   │   ├── navigation.css
│   │   │   └── cards.css
│   │   ├── layout/             # Layout styles
│   │   │   ├── header.css
│   │   │   ├── footer.css
│   │   │   ├── sidebar.css
│   │   │   └── grid.css
│   │   └── pages/              # Page-specific styles
│   │       ├── homepage.css
│   │       ├── product.css
│   │       └── contact.css
│   ├── js/
│   │   ├── core/               # Core functionality
│   │   │   ├── theme.js
│   │   │   ├── utils.js
│   │   │   └── api.js
│   │   ├── components/         # Component scripts
│   │   │   ├── carousel.js
│   │   │   ├── modal.js
│   │   │   ├── tabs.js
│   │   │   └── forms.js
│   │   └── vendor/             # Third-party libraries
│   │       ├── jquery.min.js
│   │       └── bootstrap.min.js
│   └── img/
│       ├── common/             # Common images
│       ├── icons/              # SVG icons
│       ├── placeholders/       # Placeholder images
│       └── backgrounds/        # Background patterns
├── templates/                   # Organized templates
│   ├── layouts/                # Layout variations
│   │   ├── default.html
│   │   ├── wide.html
│   │   ├── narrow.html
│   │   └── fullwidth.html
│   ├── partials/               # Template partials
│   │   ├── head.html
│   │   ├── header.html
│   │   ├── navigation.html
│   │   ├── sidebar.html
│   │   ├── footer.html
│   │   └── scripts.html
│   ├── components/             # Reusable components
│   │   ├── breadcrumbs.html
│   │   ├── pagination.html
│   │   ├── search-form.html
│   │   ├── social-links.html
│   │   └── newsletter.html
│   └── pages/                  # Page templates
│       ├── homepage.html
│       ├── category.html
│       ├── article.html
│       └── contact.html
└── config/                      # Configuration files
    ├── settings.xml
    ├── menus.xml
    └── widgets.xml
```

## File Naming Conventions

### Template Files

Follow these naming conventions for consistency:

1. **Lowercase with hyphens**: `contact-form.html`
2. **Descriptive names**: `product-detail.html` not `prod.html`
3. **Hierarchical naming**: `user-profile-edit.html`

### Asset Files

1. **CSS Files**: 
   - `style.css` (main styles)
   - `layout.css` (layout-specific)
   - `component-name.css` (component styles)

2. **JavaScript Files**:
   - `theme.js` (main functionality)
   - `component-name.js` (component scripts)
   - `vendor-library.js` (third-party)

3. **Image Files**:
   - `logo.png`, `logo-small.png`
   - `bg-pattern.jpg`
   - `icon-search.svg`

### Template Examples

**Main Layout Template (layout.html)**
```smarty
<!DOCTYPE html>
<html lang="{$wa->locale()}" class="{if $wa->isMobile()}mobile{else}desktop{/if}">
<head>
    {include file="partials/head.html"}
</head>
<body class="theme-{$wa_theme} app-{$wa->app()}">
    {include file="partials/header.html"}
    
    <main id="main-content" class="main-content">
        {if $breadcrumbs}
            {include file="components/breadcrumbs.html"}
        {/if}
        
        <div class="content-wrapper">
            {if $sidebar}
                <aside class="sidebar">
                    {include file="partials/sidebar.html"}
                </aside>
            {/if}
            
            <div class="main-content-area">
                {$content}
            </div>
        </div>
    </main>
    
    {include file="partials/footer.html"}
    {include file="partials/scripts.html"}
</body>
</html>
```

**Header Template (partials/header.html)**
```smarty
<header class="site-header" role="banner">
    <div class="header-container">
        <div class="header-top">
            {if $wa->user()}
                <div class="user-info">
                    <span>Welcome, {$wa->user()->getName()|escape}</span>
                    <a href="{$wa_app_url}?action=logout">Logout</a>
                </div>
            {else}
                <div class="auth-links">
                    <a href="{$wa_app_url}login/">Login</a>
                    <a href="{$wa_app_url}signup/">Sign Up</a>
                </div>
            {/if}
        </div>
        
        <div class="header-main">
            <div class="site-branding">
                <a href="{$wa_url}" class="site-logo">
                    <img src="{$wa_theme_url}img/logo.png" alt="{$wa->accountName()|escape}">
                </a>
            </div>
            
            <nav class="main-navigation" role="navigation">
                {include file="components/navigation.html"}
            </nav>
            
            <div class="header-actions">
                {include file="components/search-form.html"}
                {if $wa->app() == 'shop'}
                    {include file="shop/cart-widget.html"}
                {/if}
            </div>
        </div>
    </div>
</header>
```

**Navigation Component (components/navigation.html)**
```smarty
<ul class="nav-menu" role="menubar">
    {foreach $wa->apps() as $app}
        {if $app.available}
            <li class="nav-item {if $wa->app() == $app.id}current{/if}" role="none">
                <a href="{$app.url}" 
                   class="nav-link" 
                   role="menuitem"
                   {if $app.id == $wa->app()}aria-current="page"{/if}>
                    {if $app.icon}
                        <i class="app-icon {$app.icon}"></i>
                    {/if}
                    {$app.name|escape}
                </a>
                
                {if $app.subnav}
                    <ul class="sub-menu" role="menu">
                        {foreach $app.subnav as $subitem}
                            <li role="none">
                                <a href="{$subitem.url}" role="menuitem">
                                    {$subitem.name|escape}
                                </a>
                            </li>
                        {/foreach}
                    </ul>
                {/if}
            </li>
        {/if}
    {/foreach}
</ul>
```

## Template Hierarchy

### Override Priority

Templates are loaded in this priority order:

1. **Custom theme templates** (`wa-data/public/[domain]/themes/[theme]/`)
2. **Application default templates** (`wa-apps/[app]/themes/default/`)
3. **System default templates** (`wa-system/themes/default/`)

### Template Inheritance

**Child Theme Structure**
```smarty
{* Extend parent template *}
{extends file="parent-theme:layout.html"}

{* Override specific blocks *}
{block name="header"}
    <header class="custom-header">
        {* Custom header content *}
    </header>
{/block}

{* Append to existing blocks *}
{block name="styles" append}
    <link rel="stylesheet" href="{$wa_theme_url}css/custom.css">
{/block}
```

### Application-Specific Templates

**Shop-Script Product Template**
```smarty
{* templates/shop/product.html *}
<div class="product-page">
    <div class="product-gallery">
        {include file="shop/components/product-gallery.html"}
    </div>
    
    <div class="product-info">
        <h1 class="product-title">{$product.name|escape}</h1>
        
        <div class="product-price">
            {if $product.compare_price > $product.price}
                <span class="original-price">{shop_currency}{$product.compare_price}</span>
            {/if}
            <span class="current-price">{shop_currency}{$product.price}</span>
        </div>
        
        <div class="product-description">
            {$product.description}
        </div>
        
        {include file="shop/components/add-to-cart.html"}
        
        {if $product.features}
            {include file="shop/components/product-features.html"}
        {/if}
    </div>
</div>
```

## Asset Management

### CSS Organization

**Main Stylesheet (css/style.css)**
```css
/* Import base styles */
@import url('base/reset.css');
@import url('base/typography.css');
@import url('base/variables.css');

/* Import layout styles */
@import url('layout/header.css');
@import url('layout/footer.css');
@import url('layout/grid.css');

/* Import component styles */
@import url('components/buttons.css');
@import url('components/forms.css');
@import url('components/navigation.css');

/* Import page-specific styles */
@import url('pages/homepage.css');
@import url('pages/product.css');

/* Theme-specific overrides */
.theme-custom {
    /* Custom theme styles */
}
```

### JavaScript Organization

**Main JavaScript (js/theme.js)**
```javascript
/**
 * Main Theme JavaScript
 * Handles core theme functionality
 */

(function($) {
    'use strict';
    
    var WebasystTheme = {
        
        init: function() {
            this.setupNavigation();
            this.setupMobileMenu();
            this.setupForms();
            this.setupLazyLoading();
            this.bindEvents();
        },
        
        setupNavigation: function() {
            $('.nav-menu .nav-item').hover(
                function() {
                    $(this).addClass('hover');
                },
                function() {
                    $(this).removeClass('hover');
                }
            );
        },
        
        setupMobileMenu: function() {
            $('.mobile-menu-toggle').on('click', function(e) {
                e.preventDefault();
                $('body').toggleClass('mobile-menu-open');
            });
        },
        
        setupForms: function() {
            // Enhanced form functionality
            $('form.ajax-form').on('submit', function(e) {
                e.preventDefault();
                WebasystTheme.submitForm($(this));
            });
        },
        
        submitForm: function($form) {
            var formData = $form.serialize();
            var action = $form.attr('action');
            
            $.ajax({
                url: action,
                type: 'POST',
                data: formData,
                dataType: 'json',
                success: function(response) {
                    if (response.status === 'ok') {
                        WebasystTheme.showMessage(response.message, 'success');
                    } else {
                        WebasystTheme.showMessage(response.message, 'error');
                    }
                }
            });
        },
        
        showMessage: function(message, type) {
            var $message = $('<div class="alert alert-' + type + '">' + message + '</div>');
            $('.messages-container').html($message);
            
            setTimeout(function() {
                $message.fadeOut();
            }, 5000);
        },
        
        bindEvents: function() {
            $(window).on('scroll', this.handleScroll);
            $(window).on('resize', this.handleResize);
        },
        
        handleScroll: function() {
            var scrollTop = $(window).scrollTop();
            
            // Fixed header on scroll
            if (scrollTop > 100) {
                $('body').addClass('scrolled');
            } else {
                $('body').removeClass('scrolled');
            }
        },
        
        handleResize: function() {
            // Handle responsive behavior
            if ($(window).width() > 768) {
                $('body').removeClass('mobile-menu-open');
            }
        }
    };
    
    // Initialize theme
    $(document).ready(function() {
        WebasystTheme.init();
    });
    
    // Expose to global scope if needed
    window.WebasystTheme = WebasystTheme;
    
})(jQuery);
```

## Theme Manifest Configuration

### Complete theme.xml Example

```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="professional" system="0">
    <name locale="en_US">Professional Business Theme</name>
    <name locale="ru_RU">Профессиональная Бизнес Тема</name>
    <description locale="en_US">A comprehensive business theme for Webasyst applications</description>
    <description locale="ru_RU">Комплексная бизнес-тема для приложений Webasyst</description>
    
    <author>Your Company</author>
    <vendor>Your Company Ltd.</vendor>
    <version>2.1.0</version>
    <build>2024010301</build>
    
    <requirements>
        <app id="site" version="2.0"/>
        <app id="shop" version="8.0" optional="1"/>
        <app id="crm" version="1.0" optional="1"/>
        <app id="helpdesk" version="1.0" optional="1"/>
        <app id="mailer" version="1.0" optional="1"/>
        <app id="hub" version="1.0" optional="1"/>
    </requirements>
    
    <compatibility>
        <wa version="2.0"/>
        <php version="7.4"/>
    </compatibility>
    
    <files>
        <!-- Core styles -->
        <file path="css/style.css"/>
        <file path="css/responsive.css"/>
        
        <!-- Component styles -->
        <file path="css/components/buttons.css"/>
        <file path="css/components/forms.css"/>
        <file path="css/components/navigation.css"/>
        
        <!-- JavaScript files -->
        <file path="js/theme.js"/>
        <file path="js/components/carousel.js"/>
        <file path="js/components/modal.js"/>
        
        <!-- Template files -->
        <file path="layout.html"/>
        <file path="index.html"/>
        <file path="includes/header.html"/>
        <file path="includes/footer.html"/>
    </files>
    
    <settings>
        <setting id="color_scheme" type="select" default="blue">
            <name locale="en_US">Color Scheme</name>
            <option value="blue">Blue</option>
            <option value="green">Green</option>
            <option value="red">Red</option>
        </setting>
        
        <setting id="layout_width" type="select" default="wide">
            <name locale="en_US">Layout Width</name>
            <option value="narrow">Narrow</option>
            <option value="wide">Wide</option>
            <option value="fullwidth">Full Width</option>
        </setting>
        
        <setting id="show_breadcrumbs" type="checkbox" default="1">
            <name locale="en_US">Show Breadcrumbs</name>
        </setting>
    </settings>
    
    <preview>preview.jpg</preview>
    <icon>icon.png</icon>
</theme>
```

## Multi-Application Template Organization

### Application-Specific Directory Structure

```
theme-name/
├── app-templates/
│   ├── site/                   # Site application templates
│   │   ├── page.html
│   │   ├── category.html
│   │   └── includes/
│   ├── shop/                   # Shop-Script templates
│   │   ├── product.html
│   │   ├── category.html
│   │   ├── cart.html
│   │   ├── checkout.html
│   │   └── includes/
│   │       ├── product-card.html
│   │       ├── cart-widget.html
│   │       └── checkout-steps.html
│   ├── crm/                    # CRM templates
│   │   ├── contact.html
│   │   ├── deal.html
│   │   └── includes/
│   ├── helpdesk/               # Helpdesk templates
│   │   ├── ticket.html
│   │   ├── ticket-list.html
│   │   └── includes/
│   ├── mailer/                 # Mailer templates
│   │   ├── campaign.html
│   │   ├── template-editor.html
│   │   └── includes/
│   └── hub/                    # Hub templates
│       ├── article.html
│       ├── category.html
│       └── includes/
```

### Cross-Application Shared Components

```smarty
{* Shared user authentication component *}
{* File: includes/auth-status.html *}

<div class="auth-status">
    {if $wa->user()}
        <div class="user-authenticated">
            <div class="user-avatar">
                {if $wa->user()->getPhoto()}
                    <img src="{$wa->user()->getPhoto()}" alt="{$wa->user()->getName()|escape}">
                {else}
                    <div class="avatar-placeholder">
                        {$wa->user()->getName()|substr:0:1|upper}
                    </div>
                {/if}
            </div>
            
            <div class="user-info">
                <span class="user-name">{$wa->user()->getName()|escape}</span>
                <div class="user-actions">
                    <a href="{$wa_app_url}profile/">Profile</a>
                    <a href="{$wa_app_url}?action=logout">Logout</a>
                </div>
            </div>
        </div>
    {else}
        <div class="auth-links">
            <a href="{$wa_app_url}login/" class="btn btn-outline">Login</a>
            <a href="{$wa_app_url}signup/" class="btn btn-primary">Sign Up</a>
        </div>
    {/if}
</div>
```

This comprehensive template structure provides the foundation for creating maintainable, scalable themes that work consistently across all Webasyst applications while allowing for application-specific customizations.