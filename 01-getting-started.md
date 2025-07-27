# Webasyst Theme Development Guide - Getting Started

## Table of Contents
1. [Introduction](#introduction)
2. [Development Environment Setup](#development-environment-setup)
3. [Understanding Webasyst Theme Architecture](#understanding-webasyst-theme-architecture)
4. [Basic Theme Structure](#basic-theme-structure)
5. [Creating Your First Theme](#creating-your-first-theme)

## Introduction

This comprehensive guide covers developing themes for the Webasyst ecosystem, including themes for multiple applications such as Site, Shop-Script, CRM, Helpdesk, Mailer, and Hub. Each application can have its own theme variations while maintaining consistency across the platform.

### What You'll Learn

- Complete theme development workflow from setup to deployment
- Integration patterns between PHP business logic and Smarty templates
- Application-specific theme requirements and best practices
- Responsive design and mobile optimization techniques
- Update-safe development practices

### Prerequisites

- Basic knowledge of HTML, CSS, JavaScript
- Understanding of PHP fundamentals
- Familiarity with Smarty template engine
- Web development experience

## Development Environment Setup

### Local Development Installation

1. **Download Webasyst Framework**
```bash
git clone https://github.com/webasyst/webasyst-framework.git webasyst-dev
cd webasyst-dev
```

2. **Set up Virtual Host**
```apache
<VirtualHost *:80>
    ServerName webasyst.local
    DocumentRoot /path/to/webasyst-dev
    
    <Directory "/path/to/webasyst-dev">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

3. **Configure Database**
```sql
CREATE DATABASE webasyst_dev CHARACTER SET utf8 COLLATE utf8_general_ci;
GRANT ALL PRIVILEGES ON webasyst_dev.* TO 'webasyst'@'localhost' IDENTIFIED BY 'password';
```

4. **Install Core Applications**
```bash
# Clone required applications
git clone https://github.com/webasyst/shop-script.git wa-apps/shop
git clone https://github.com/webasyst/helpdesk.git wa-apps/helpdesk
git clone https://github.com/webasyst/crm.git wa-apps/crm
git clone https://github.com/webasyst/mailer.git wa-apps/mailer
git clone https://github.com/webasyst/hub.git wa-apps/hub
```

### Directory Structure Overview

```
webasyst-dev/
├── wa-apps/                    # Application directories
│   ├── site/                  # Site application (CMS)
│   ├── shop/                  # Shop-Script (eCommerce)
│   ├── crm/                   # CRM application
│   ├── helpdesk/              # Helpdesk application
│   ├── mailer/                # Email marketing
│   └── hub/                   # Knowledge base
├── wa-data/                   # User data and customizations
│   └── public/                # Public files
│       └── [domain]/
│           └── themes/        # Custom theme files (update-safe)
├── wa-system/                 # Core framework
├── wa-config/                 # Configuration files
└── wa-cache/                  # Cache directory
```

## Understanding Webasyst Theme Architecture

### Theme Location Hierarchy

Webasyst uses a hierarchical theme system:

1. **Core Themes** (`wa-apps/[app]/themes/`): Original application themes
2. **Custom Themes** (`wa-data/public/[domain]/themes/`): User customizations (update-safe)

### Theme Components

Each theme consists of:

- **Templates** (`.html`): Smarty template files
- **Layouts** (`.html`): Page layout templates
- **Stylesheets** (`.css`): Design styles
- **JavaScript** (`.js`): Interactive functionality
- **Images**: Theme graphics and assets
- **Theme Manifest** (`theme.xml`): Theme configuration

### PHP and Smarty Integration

Webasyst themes combine PHP business logic with Smarty presentation:

```php
// PHP Controller (handles business logic)
class shopFrontendProductAction extends waViewAction
{
    public function execute()
    {
        $product_id = waRequest::get('id', 0, waRequest::TYPE_INT);
        $product = new shopProduct($product_id);
        
        $this->view->assign([
            'product' => $product,
            'categories' => $product->getCategories(),
            'features' => $product->getFeatures(),
        ]);
    }
}
```

```smarty
{* Smarty Template (handles presentation) *}
<div class="product-details">
    <h1>{$product.name|escape}</h1>
    <div class="price">
        {shop_currency}{$product.price}
    </div>
    
    {foreach $product.features as $feature}
        <div class="feature">
            <strong>{$feature.name}:</strong> {$feature.value}
        </div>
    {/foreach}
</div>
```

## Basic Theme Structure

### Minimal Theme Structure

```
theme-name/
├── theme.xml              # Theme manifest
├── index.html            # Homepage template
├── layout.html           # Main layout template
├── css/
│   └── style.css         # Main stylesheet
├── js/
│   └── theme.js          # Theme JavaScript
└── img/                  # Theme images
```

### Theme Manifest (theme.xml)

```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="mytheme" system="0">
    <name locale="en_US">My Custom Theme</name>
    <name locale="ru_RU">Моя Тема</name>
    <description locale="en_US">A custom theme for Webasyst</description>
    <author>Your Name</author>
    <vendor>Your Company</vendor>
    <version>1.0.0</version>
    <requirements>
        <app id="site" version="2.0"/>
        <app id="shop" version="8.0" optional="1"/>
    </requirements>
    <files>
        <file path="css/style.css"/>
        <file path="js/theme.js"/>
    </files>
</theme>
```

### Layout Template Structure

```smarty
<!DOCTYPE html>
<html lang="{$wa->locale()}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    
    {* Page title and meta *}
    <title>{$wa->title()}</title>
    {$wa->meta()}
    
    {* Theme stylesheets *}
    <link rel="stylesheet" href="{$wa_theme_url}css/style.css?v{$wa_theme_version}">
    
    {* Application-specific headers *}
    {$wa->header()}
</head>
<body>
    {* Main navigation *}
    <nav class="main-navigation">
        {include file="navigation.html"}
    </nav>
    
    {* Page content *}
    <main class="page-content">
        {$content}
    </main>
    
    {* Footer *}
    <footer class="site-footer">
        {include file="footer.html"}
    </footer>
    
    {* JavaScript files *}
    <script src="{$wa_theme_url}js/theme.js?v{$wa_theme_version}"></script>
    {$wa->js()}
</body>
</html>
```

## Creating Your First Theme

### Step 1: Create Theme Directory

```bash
mkdir wa-data/public/yourdomain.com/themes/mytheme
cd wa-data/public/yourdomain.com/themes/mytheme
```

### Step 2: Create Basic Files

**theme.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<theme id="mytheme" system="0">
    <name locale="en_US">My First Theme</name>
    <description locale="en_US">Learning Webasyst theme development</description>
    <author>Your Name</author>
    <version>1.0.0</version>
</theme>
```

**layout.html**
```smarty
<!DOCTYPE html>
<html>
<head>
    <title>{$wa->title()}</title>
    {$wa->meta()}
    <link rel="stylesheet" href="{$wa_theme_url}css/style.css">
    {$wa->header()}
</head>
<body>
    <header>
        <h1>My First Theme</h1>
        <nav>
            {foreach $wa->apps() as $app}
                <a href="{$app.url}">{$app.name}</a>
            {/foreach}
        </nav>
    </header>
    
    <main>
        {$content}
    </main>
    
    <footer>
        <p>&copy; {date('Y')} My Website</p>
    </footer>
    
    <script src="{$wa_theme_url}js/theme.js"></script>
    {$wa->js()}
</body>
</html>
```

**index.html**
```smarty
<div class="homepage">
    <h2>Welcome to My Website</h2>
    <p>This is the homepage content.</p>
    
    {* Display available applications *}
    <div class="apps-grid">
        {foreach $wa->apps() as $app}
            {if $app.available}
                <div class="app-card">
                    <h3>{$app.name}</h3>
                    <p>{$app.description}</p>
                    <a href="{$app.url}" class="btn">Open {$app.name}</a>
                </div>
            {/if}
        {/foreach}
    </div>
</div>
```

**css/style.css**
```css
/* Basic theme styles */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    line-height: 1.6;
}

header {
    background: #333;
    color: white;
    padding: 1rem;
}

nav a {
    color: white;
    text-decoration: none;
    margin-right: 1rem;
}

main {
    padding: 2rem;
    min-height: 60vh;
}

.apps-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1rem;
    margin-top: 2rem;
}

.app-card {
    border: 1px solid #ddd;
    padding: 1rem;
    border-radius: 4px;
}

.btn {
    display: inline-block;
    background: #007cba;
    color: white;
    padding: 0.5rem 1rem;
    text-decoration: none;
    border-radius: 4px;
}

footer {
    background: #f8f9fa;
    text-align: center;
    padding: 1rem;
}
```

**js/theme.js**
```javascript
document.addEventListener('DOMContentLoaded', function() {
    console.log('My First Theme loaded');
    
    // Add any interactive functionality here
    const appCards = document.querySelectorAll('.app-card');
    appCards.forEach(card => {
        card.addEventListener('mouseenter', function() {
            this.style.transform = 'scale(1.02)';
        });
        
        card.addEventListener('mouseleave', function() {
            this.style.transform = 'scale(1)';
        });
    });
});
```

### Step 3: Activate Theme

1. Log into Webasyst admin panel
2. Go to Design → Themes
3. Select your new theme
4. Click "Apply"

### Key Webasyst Template Variables

- `{$wa_url}` - Base Webasyst URL
- `{$wa_app_url}` - Current application URL
- `{$wa_theme_url}` - Current theme URL
- `{$wa_theme_version}` - Theme version for cache busting
- `{$wa->user()}` - Current user object
- `{$wa->apps()}` - Available applications array
- `{$wa->title()}` - Page title
- `{$wa->meta()}` - Page meta tags

### Testing Your Theme

1. **Frontend Testing**: Visit your site to see the theme applied
2. **Application Testing**: Test each installed application
3. **Responsive Testing**: Check mobile compatibility
4. **User Testing**: Test with different user roles

### Next Steps

After creating your basic theme:

1. Study application-specific requirements
2. Implement responsive design patterns
3. Add advanced JavaScript functionality
4. Optimize for performance
5. Test across different applications

This foundation provides the essential knowledge to start developing Webasyst themes. The following documentation sections will dive deeper into application-specific implementations, advanced features, and best practices.