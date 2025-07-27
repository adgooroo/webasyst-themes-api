# Site App Theme Development Guide

## Table of Contents
1. [Site App Overview](#site-app-overview)
2. [Homepage and Landing Page Templates](#homepage-and-landing-page-templates)
3. [Page Layout System](#page-layout-system)
4. [Content Management Templates](#content-management-templates)
5. [Search Functionality](#search-functionality)
6. [Contact Forms and Lead Generation](#contact-forms-and-lead-generation)
7. [SEO and Performance Optimization](#seo-and-performance-optimization)
8. [Multi-language Support](#multi-language-support)

## Site App Overview

The Site app is Webasyst's content management system for creating corporate websites, blogs, and landing pages. Site app themes focus on content presentation, SEO optimization, and lead generation functionality.

### Key Site App Features

- **Content Management**: Pages, blogs, news, and custom content types
- **Homepage Builder**: Flexible homepage with customizable sections
- **SEO Tools**: Meta tags, structured data, and search optimization
- **Contact Forms**: Lead capture and customer communication
- **Multi-language**: Internationalization and localization support
- **Blog System**: Article publishing and content organization

### Site Theme Structure

```
site-theme/
├── theme.xml                    # Theme manifest
├── layout.html                  # Base layout
├── index.html                   # Homepage template
├── page.html                    # Static page template
├── blog/                        # Blog templates
│   ├── post.html               # Blog post detail
│   ├── list.html               # Blog post listing
│   └── category.html           # Blog category
├── includes/                    # Reusable components
│   ├── hero-section.html       # Homepage hero
│   ├── features-grid.html      # Feature showcase
│   ├── testimonials.html       # Customer testimonials
│   ├── contact-form.html       # Contact form
│   └── footer-cta.html         # Call-to-action footer
├── css/                         # Stylesheets
│   ├── homepage.css            # Homepage-specific styles
│   ├── blog.css                # Blog styling
│   └── forms.css               # Form styling
└── js/                          # JavaScript
    ├── homepage.js             # Homepage interactions
    ├── forms.js                # Form functionality
    └── blog.js                 # Blog interactions
```

## Homepage and Landing Page Templates

### Homepage Template Structure

**Template: `index.html`**
```smarty
{* Homepage layout with hero section and modular content blocks *}
{include file="$wa_theme_path/includes/hero-section.html"}

<main class="homepage-content">
    {* Hero Section with Call-to-Action *}
    <section class="hero-section" style="background-image: url('{$wa_theme_url}img/hero-bg.jpg')">
        <div class="container">
            <div class="hero-content">
                <h1 class="hero-title">{$wa->title()}</h1>
                <p class="hero-subtitle">{$wa->meta('description')}</p>
                <div class="hero-actions">
                    <a href="#contact" class="btn btn-primary btn-lg">{_w('Get Started')}</a>
                    <a href="#features" class="btn btn-outline btn-lg">{_w('Learn More')}</a>
                </div>
            </div>
        </div>
    </section>

    {* Features Grid Section *}
    <section class="features-section" id="features">
        <div class="container">
            <div class="section-header">
                <h2 class="section-title">{_w('Our Features')}</h2>
                <p class="section-subtitle">{_w('Everything you need to succeed')}</p>
            </div>
            
            <div class="features-grid">
                {foreach $features as $feature}
                    <div class="feature-card">
                        <div class="feature-icon">
                            <i class="{$feature.icon}"></i>
                        </div>
                        <h3 class="feature-title">{$feature.title}</h3>
                        <p class="feature-description">{$feature.description}</p>
                    </div>
                {/foreach}
            </div>
        </div>
    </section>

    {* Services Section *}
    <section class="services-section">
        <div class="container">
            <div class="row">
                <div class="col-md-6">
                    <div class="services-content">
                        <h2>{_w('Our Services')}</h2>
                        <p>{_w('Professional solutions tailored to your needs')}</p>
                        
                        <div class="services-list">
                            {foreach $services as $service}
                                <div class="service-item">
                                    <h4>{$service.title}</h4>
                                    <p>{$service.description}</p>
                                </div>
                            {/foreach}
                        </div>
                    </div>
                </div>
                <div class="col-md-6">
                    <div class="services-image">
                        <img src="{$wa_theme_url}img/services.jpg" alt="{_w('Our Services')}" class="img-responsive">
                    </div>
                </div>
            </div>
        </div>
    </section>

    {* Testimonials Section *}
    {include file="$wa_theme_path/includes/testimonials.html"}

    {* Contact Form Section *}
    <section class="contact-section" id="contact">
        <div class="container">
            <div class="row">
                <div class="col-md-8 col-md-offset-2">
                    <div class="section-header text-center">
                        <h2 class="section-title">{_w('Get In Touch')}</h2>
                        <p class="section-subtitle">{_w('Ready to start your project?')}</p>
                    </div>
                    
                    {include file="$wa_theme_path/includes/contact-form.html"}
                </div>
            </div>
        </div>
    </section>
</main>
```

### Hero Section Component

**Template: `includes/hero-section.html`**
```smarty
<section class="hero-section {if $hero_overlay}hero-overlay{/if}" 
         {if $hero_background}style="background-image: url('{$hero_background}')"{/if}>
    <div class="container">
        <div class="hero-content">
            {if $hero_badge}
                <div class="hero-badge">
                    <span class="badge badge-primary">{$hero_badge}</span>
                </div>
            {/if}
            
            <h1 class="hero-title">{$hero_title|default:$wa->title()}</h1>
            
            {if $hero_subtitle}
                <p class="hero-subtitle">{$hero_subtitle}</p>
            {/if}
            
            {if $hero_features}
                <ul class="hero-features">
                    {foreach $hero_features as $feature}
                        <li class="hero-feature">
                            <i class="fa fa-check"></i>
                            {$feature}
                        </li>
                    {/foreach}
                </ul>
            {/if}
            
            <div class="hero-actions">
                {if $hero_primary_action}
                    <a href="{$hero_primary_action.url}" class="btn btn-primary btn-lg">
                        {$hero_primary_action.text}
                    </a>
                {/if}
                
                {if $hero_secondary_action}
                    <a href="{$hero_secondary_action.url}" class="btn btn-outline btn-lg">
                        {$hero_secondary_action.text}
                    </a>
                {/if}
            </div>
        </div>
        
        {if $hero_image}
            <div class="hero-image">
                <img src="{$hero_image}" alt="{$hero_title}" class="img-responsive">
            </div>
        {/if}
    </div>
</section>
```

## Page Layout System

### Base Page Template

**Template: `page.html`**
```smarty
{* Page template for static content pages *}
<article class="page-content">
    <header class="page-header">
        <div class="container">
            {if $page.breadcrumbs}
                <nav class="breadcrumbs">
                    {foreach $page.breadcrumbs as $crumb}
                        {if !$crumb@last}
                            <a href="{$crumb.url}">{$crumb.title}</a>
                            <span class="breadcrumb-separator">/</span>
                        {else}
                            <span class="breadcrumb-current">{$crumb.title}</span>
                        {/if}
                    {/foreach}
                </nav>
            {/if}
            
            <h1 class="page-title">{$page.title}</h1>
            
            {if $page.subtitle}
                <p class="page-subtitle">{$page.subtitle}</p>
            {/if}
            
            {if $page.featured_image}
                <div class="page-featured-image">
                    <img src="{$page.featured_image}" alt="{$page.title}" class="img-responsive">
                </div>
            {/if}
        </div>
    </header>

    <div class="page-body">
        <div class="container">
            <div class="row">
                <div class="col-md-{if $page.sidebar}8{else}12{/if}">
                    <div class="page-content-area">
                        {$page.content}
                        
                        {* Page-specific call-to-action *}
                        {if $page.cta}
                            <div class="page-cta">
                                <div class="cta-card">
                                    <h3>{$page.cta.title}</h3>
                                    <p>{$page.cta.description}</p>
                                    <a href="{$page.cta.url}" class="btn btn-primary">
                                        {$page.cta.button_text}
                                    </a>
                                </div>
                            </div>
                        {/if}
                    </div>
                </div>
                
                {if $page.sidebar}
                    <div class="col-md-4">
                        <aside class="page-sidebar">
                            {foreach $page.sidebar as $widget}
                                <div class="sidebar-widget widget-{$widget.type}">
                                    {if $widget.title}
                                        <h4 class="widget-title">{$widget.title}</h4>
                                    {/if}
                                    <div class="widget-content">
                                        {$widget.content}
                                    </div>
                                </div>
                            {/foreach}
                        </aside>
                    </div>
                {/if}
            </div>
        </div>
    </div>
</article>
```

## Content Management Templates

### Blog Post Template

**Template: `blog/post.html`**
```smarty
{* Individual blog post template *}
<article class="blog-post">
    <header class="post-header">
        <div class="container">
            <nav class="breadcrumbs">
                <a href="{$wa_url}">{_w('Home')}</a>
                <span class="breadcrumb-separator">/</span>
                <a href="{$blog_url}">{_w('Blog')}</a>
                <span class="breadcrumb-separator">/</span>
                <span class="breadcrumb-current">{$post.title}</span>
            </nav>
            
            <h1 class="post-title">{$post.title}</h1>
            
            <div class="post-meta">
                <div class="post-author">
                    <img src="{$post.author.avatar}" alt="{$post.author.name}" class="author-avatar">
                    <span class="author-name">{$post.author.name}</span>
                </div>
                
                <time class="post-date" datetime="{$post.date_iso}">
                    {$post.date|date_format:"%B %d, %Y"}
                </time>
                
                {if $post.reading_time}
                    <span class="reading-time">{$post.reading_time} {_w('min read')}</span>
                {/if}
                
                {if $post.categories}
                    <div class="post-categories">
                        {foreach $post.categories as $category}
                            <a href="{$category.url}" class="category-tag">
                                {$category.name}
                            </a>
                        {/foreach}
                    </div>
                {/if}
            </div>
            
            {if $post.featured_image}
                <div class="post-featured-image">
                    <img src="{$post.featured_image}" alt="{$post.title}" class="img-responsive">
                </div>
            {/if}
        </div>
    </header>

    <div class="post-content">
        <div class="container">
            <div class="row">
                <div class="col-md-8 col-md-offset-2">
                    <div class="post-body">
                        {$post.content}
                    </div>
                    
                    {if $post.tags}
                        <div class="post-tags">
                            <h4>{_w('Tags:')}</h4>
                            {foreach $post.tags as $tag}
                                <a href="{$tag.url}" class="tag">{$tag.name}</a>
                            {/foreach}
                        </div>
                    {/if}
                    
                    <div class="post-sharing">
                        <h4>{_w('Share this post:')}</h4>
                        <div class="social-sharing">
                            <a href="https://twitter.com/intent/tweet?url={$post.url|urlencode}&text={$post.title|urlencode}" 
                               class="share-twitter" target="_blank">
                                <i class="fa fa-twitter"></i> Twitter
                            </a>
                            <a href="https://www.facebook.com/sharer/sharer.php?u={$post.url|urlencode}" 
                               class="share-facebook" target="_blank">
                                <i class="fa fa-facebook"></i> Facebook
                            </a>
                            <a href="https://www.linkedin.com/sharing/share-offsite/?url={$post.url|urlencode}" 
                               class="share-linkedin" target="_blank">
                                <i class="fa fa-linkedin"></i> LinkedIn
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</article>

{* Related Posts Section *}
{if $related_posts}
    <section class="related-posts">
        <div class="container">
            <h3 class="section-title">{_w('Related Posts')}</h3>
            <div class="row">
                {foreach $related_posts as $related_post}
                    <div class="col-md-4">
                        {include file="$wa_theme_path/includes/post-card.html" post=$related_post}
                    </div>
                {/foreach}
            </div>
        </div>
    </section>
{/if}
```

### Blog Listing Template

**Template: `blog/list.html`**
```smarty
{* Blog posts listing page *}
<div class="blog-listing">
    <header class="blog-header">
        <div class="container">
            <h1 class="blog-title">{_w('Blog')}</h1>
            <p class="blog-subtitle">{_w('Latest insights and updates')}</p>
        </div>
    </header>

    <div class="blog-content">
        <div class="container">
            <div class="row">
                <div class="col-md-8">
                    <div class="posts-list">
                        {foreach $posts as $post}
                            <article class="post-preview">
                                {if $post.featured_image}
                                    <div class="post-preview-image">
                                        <a href="{$post.url}">
                                            <img src="{$post.featured_image}" alt="{$post.title}" class="img-responsive">
                                        </a>
                                    </div>
                                {/if}
                                
                                <div class="post-preview-content">
                                    <h2 class="post-preview-title">
                                        <a href="{$post.url}">{$post.title}</a>
                                    </h2>
                                    
                                    <div class="post-preview-meta">
                                        <span class="post-author">{$post.author.name}</span>
                                        <span class="post-date">{$post.date|date_format:"%B %d, %Y"}</span>
                                    </div>
                                    
                                    <p class="post-preview-excerpt">{$post.excerpt}</p>
                                    
                                    <a href="{$post.url}" class="read-more">
                                        {_w('Read More')} <i class="fa fa-arrow-right"></i>
                                    </a>
                                </div>
                            </article>
                        {/foreach}
                    </div>
                    
                    {* Pagination *}
                    {if $pagination.total_pages > 1}
                        <nav class="pagination-nav">
                            <ul class="pagination">
                                {if $pagination.prev_page}
                                    <li class="page-item">
                                        <a href="{$pagination.prev_url}" class="page-link">
                                            <i class="fa fa-chevron-left"></i> {_w('Previous')}
                                        </a>
                                    </li>
                                {/if}
                                
                                {foreach $pagination.pages as $page}
                                    <li class="page-item {if $page.current}active{/if}">
                                        <a href="{$page.url}" class="page-link">{$page.number}</a>
                                    </li>
                                {/foreach}
                                
                                {if $pagination.next_page}
                                    <li class="page-item">
                                        <a href="{$pagination.next_url}" class="page-link">
                                            {_w('Next')} <i class="fa fa-chevron-right"></i>
                                        </a>
                                    </li>
                                {/if}
                            </ul>
                        </nav>
                    {/if}
                </div>
                
                <div class="col-md-4">
                    <aside class="blog-sidebar">
                        {* Categories Widget *}
                        {if $categories}
                            <div class="sidebar-widget">
                                <h4 class="widget-title">{_w('Categories')}</h4>
                                <ul class="category-list">
                                    {foreach $categories as $category}
                                        <li class="category-item">
                                            <a href="{$category.url}">
                                                {$category.name}
                                                <span class="post-count">({$category.post_count})</span>
                                            </a>
                                        </li>
                                    {/foreach}
                                </ul>
                            </div>
                        {/if}
                        
                        {* Recent Posts Widget *}
                        {if $recent_posts}
                            <div class="sidebar-widget">
                                <h4 class="widget-title">{_w('Recent Posts')}</h4>
                                <div class="recent-posts">
                                    {foreach $recent_posts as $recent_post}
                                        <div class="recent-post">
                                            <h5 class="recent-post-title">
                                                <a href="{$recent_post.url}">{$recent_post.title}</a>
                                            </h5>
                                            <span class="recent-post-date">{$recent_post.date|date_format:"%B %d, %Y"}</span>
                                        </div>
                                    {/foreach}
                                </div>
                            </div>
                        {/if}
                    </aside>
                </div>
            </div>
        </div>
    </div>
</div>
```

## Search Functionality

### Search Results Template

**Template: `search.html`**
```smarty
{* Search results page *}
<div class="search-results">
    <header class="search-header">
        <div class="container">
            <h1 class="search-title">
                {if $search_query}
                    {_w('Search Results for')}: "{$search_query}"
                {else}
                    {_w('Search')}
                {/if}
            </h1>
            
            {if $results_count !== null}
                <p class="search-meta">
                    {if $results_count > 0}
                        {_w('%d results found', $results_count)}
                    {else}
                        {_w('No results found')}
                    {/if}
                </p>
            {/if}
        </div>
    </header>

    <div class="search-content">
        <div class="container">
            <div class="row">
                <div class="col-md-8">
                    {* Search Form *}
                    <div class="search-form-container">
                        <form action="{$search_url}" method="get" class="search-form">
                            <div class="search-input-group">
                                <input type="text" name="query" value="{$search_query|escape}" 
                                       placeholder="{_w('Enter your search terms...')}" 
                                       class="search-input" required>
                                <button type="submit" class="search-button">
                                    <i class="fa fa-search"></i>
                                    <span class="sr-only">{_w('Search')}</span>
                                </button>
                            </div>
                            
                            {* Advanced Search Options *}
                            <div class="search-filters">
                                <select name="type" class="search-filter">
                                    <option value="">{_w('All Content')}</option>
                                    <option value="pages" {if $search_type == 'pages'}selected{/if}>{_w('Pages')}</option>
                                    <option value="blog" {if $search_type == 'blog'}selected{/if}>{_w('Blog Posts')}</option>
                                    <option value="products" {if $search_type == 'products'}selected{/if}>{_w('Products')}</option>
                                </select>
                                
                                <select name="sort" class="search-filter">
                                    <option value="relevance" {if $search_sort == 'relevance'}selected{/if}>{_w('Relevance')}</option>
                                    <option value="date" {if $search_sort == 'date'}selected{/if}>{_w('Date')}</option>
                                    <option value="title" {if $search_sort == 'title'}selected{/if}>{_w('Title')}</option>
                                </select>
                            </div>
                        </form>
                    </div>
                    
                    {* Search Results *}
                    {if $search_results}
                        <div class="search-results-list">
                            {foreach $search_results as $result}
                                <article class="search-result">
                                    <header class="result-header">
                                        <h3 class="result-title">
                                            <a href="{$result.url}">{$result.title}</a>
                                        </h3>
                                        
                                        <div class="result-meta">
                                            <span class="result-type">{$result.type}</span>
                                            {if $result.date}
                                                <span class="result-date">{$result.date|date_format:"%B %d, %Y"}</span>
                                            {/if}
                                        </div>
                                    </header>
                                    
                                    <div class="result-content">
                                        <p class="result-excerpt">{$result.excerpt}</p>
                                    </div>
                                    
                                    <div class="result-actions">
                                        <a href="{$result.url}" class="result-link">
                                            {_w('Read More')} <i class="fa fa-arrow-right"></i>
                                        </a>
                                    </div>
                                </article>
                            {/foreach}
                        </div>
                        
                        {* Search Pagination *}
                        {if $search_pagination.total_pages > 1}
                            <nav class="search-pagination">
                                <ul class="pagination">
                                    {if $search_pagination.prev_page}
                                        <li class="page-item">
                                            <a href="{$search_pagination.prev_url}" class="page-link">
                                                <i class="fa fa-chevron-left"></i> {_w('Previous')}
                                            </a>
                                        </li>
                                    {/if}
                                    
                                    {foreach $search_pagination.pages as $page}
                                        <li class="page-item {if $page.current}active{/if}">
                                            <a href="{$page.url}" class="page-link">{$page.number}</a>
                                        </li>
                                    {/foreach}
                                    
                                    {if $search_pagination.next_page}
                                        <li class="page-item">
                                            <a href="{$search_pagination.next_url}" class="page-link">
                                                {_w('Next')} <i class="fa fa-chevron-right"></i>
                                            </a>
                                        </li>
                                    {/if}
                                </ul>
                            </nav>
                        {/if}
                    {elseif $search_query}
                        <div class="no-results">
                            <div class="no-results-content">
                                <h3>{_w('No results found')}</h3>
                                <p>{_w('Try adjusting your search terms or browse our popular content below.')}</p>
                                
                                {* Search Suggestions *}
                                {if $search_suggestions}
                                    <div class="search-suggestions">
                                        <h4>{_w('Did you mean:')}</h4>
                                        <ul class="suggestion-list">
                                            {foreach $search_suggestions as $suggestion}
                                                <li>
                                                    <a href="{$search_url}?query={$suggestion|urlencode}">{$suggestion}</a>
                                                </li>
                                            {/foreach}
                                        </ul>
                                    </div>
                                {/if}
                            </div>
                        </div>
                    {/if}
                </div>
                
                <div class="col-md-4">
                    <aside class="search-sidebar">
                        {* Popular Content *}
                        {if $popular_content}
                            <div class="sidebar-widget">
                                <h4 class="widget-title">{_w('Popular Content')}</h4>
                                <div class="popular-content">
                                    {foreach $popular_content as $popular_item}
                                        <div class="popular-item">
                                            <h5>
                                                <a href="{$popular_item.url}">{$popular_item.title}</a>
                                            </h5>
                                            <span class="popular-type">{$popular_item.type}</span>
                                        </div>
                                    {/foreach}
                                </div>
                            </div>
                        {/if}
                        
                        {* Recent Content *}
                        {if $recent_content}
                            <div class="sidebar-widget">
                                <h4 class="widget-title">{_w('Recent Content')}</h4>
                                <div class="recent-content">
                                    {foreach $recent_content as $recent_item}
                                        <div class="recent-item">
                                            <h5>
                                                <a href="{$recent_item.url}">{$recent_item.title}</a>
                                            </h5>
                                            <span class="recent-date">{$recent_item.date|date_format:"%B %d, %Y"}</span>
                                        </div>
                                    {/foreach}
                                </div>
                            </div>
                        {/if}
                    </aside>
                </div>
            </div>
        </div>
    </div>
</div>
```

## Contact Forms and Lead Generation

### Contact Form Component

**Template: `includes/contact-form.html`**
```smarty
{* Advanced contact form with lead capture *}
<div class="contact-form-container">
    <form action="{$contact_form_url}" method="post" class="contact-form" id="contactForm">
        {$wa->csrf()}
        
        <div class="form-row">
            <div class="form-group col-md-6">
                <label for="contact_name" class="form-label">{_w('Full Name')} *</label>
                <input type="text" id="contact_name" name="name" class="form-control" required
                       placeholder="{_w('Enter your full name')}">
            </div>
            
            <div class="form-group col-md-6">
                <label for="contact_email" class="form-label">{_w('Email Address')} *</label>
                <input type="email" id="contact_email" name="email" class="form-control" required
                       placeholder="{_w('Enter your email address')}">
            </div>
        </div>
        
        <div class="form-row">
            <div class="form-group col-md-6">
                <label for="contact_phone" class="form-label">{_w('Phone Number')}</label>
                <input type="tel" id="contact_phone" name="phone" class="form-control"
                       placeholder="{_w('Enter your phone number')}">
            </div>
            
            <div class="form-group col-md-6">
                <label for="contact_company" class="form-label">{_w('Company')}</label>
                <input type="text" id="contact_company" name="company" class="form-control"
                       placeholder="{_w('Enter your company name')}">
            </div>
        </div>
        
        <div class="form-group">
            <label for="contact_subject" class="form-label">{_w('Subject')} *</label>
            <select id="contact_subject" name="subject" class="form-control" required>
                <option value="">{_w('Select a subject')}</option>
                <option value="general">{_w('General Inquiry')}</option>
                <option value="sales">{_w('Sales Question')}</option>
                <option value="support">{_w('Technical Support')}</option>
                <option value="partnership">{_w('Partnership Opportunity')}</option>
                <option value="other">{_w('Other')}</option>
            </select>
        </div>
        
        <div class="form-group">
            <label for="contact_message" class="form-label">{_w('Message')} *</label>
            <textarea id="contact_message" name="message" class="form-control" rows="5" required
                      placeholder="{_w('Tell us about your project or question...')}"></textarea>
        </div>
        
        {* Budget Range (for lead qualification) *}
        <div class="form-group">
            <label for="contact_budget" class="form-label">{_w('Project Budget Range')}</label>
            <select id="contact_budget" name="budget" class="form-control">
                <option value="">{_w('Select budget range')}</option>
                <option value="under-5k">{_w('Under $5,000')}</option>
                <option value="5k-15k">{_w('$5,000 - $15,000')}</option>
                <option value="15k-50k">{_w('$15,000 - $50,000')}</option>
                <option value="50k-plus">{_w('$50,000+')}</option>
            </select>
        </div>
        
        {* Timeline *}
        <div class="form-group">
            <label for="contact_timeline" class="form-label">{_w('Project Timeline')}</label>
            <select id="contact_timeline" name="timeline" class="form-control">
                <option value="">{_w('Select timeline')}</option>
                <option value="asap">{_w('ASAP')}</option>
                <option value="1-month">{_w('Within 1 month')}</option>
                <option value="3-months">{_w('Within 3 months')}</option>
                <option value="6-months">{_w('Within 6 months')}</option>
                <option value="flexible">{_w('Flexible')}</option>
            </select>
        </div>
        
        {* Marketing Consent *}
        <div class="form-group">
            <div class="form-check">
                <input type="checkbox" id="contact_newsletter" name="newsletter" class="form-check-input" value="1">
                <label for="contact_newsletter" class="form-check-label">
                    {_w('I would like to receive updates and marketing communications')}
                </label>
            </div>
        </div>
        
        {* Privacy Consent *}
        <div class="form-group">
            <div class="form-check">
                <input type="checkbox" id="contact_privacy" name="privacy" class="form-check-input" value="1" required>
                <label for="contact_privacy" class="form-check-label">
                    {_w('I agree to the')} <a href="/privacy-policy" target="_blank">{_w('Privacy Policy')}</a> *
                </label>
            </div>
        </div>
        
        {* Captcha (if enabled) *}
        {if $captcha_enabled}
            <div class="form-group">
                <div class="captcha-container">
                    {$captcha_html}
                </div>
            </div>
        {/if}
        
        <div class="form-actions">
            <button type="submit" class="btn btn-primary btn-lg">
                <span class="btn-text">{_w('Send Message')}</span>
                <span class="btn-loading" style="display: none;">
                    <i class="fa fa-spinner fa-spin"></i> {_w('Sending...')}
                </span>
            </button>
        </div>
    </form>
    
    {* Success Message *}
    <div class="form-success" id="formSuccess" style="display: none;">
        <div class="success-content">
            <i class="fa fa-check-circle success-icon"></i>
            <h3>{_w('Thank you!')}</h3>
            <p>{_w('Your message has been sent successfully. We\'ll get back to you soon.')}</p>
        </div>
    </div>
</div>

{* Contact Form JavaScript *}
<script>
document.getElementById('contactForm').addEventListener('submit', function(e) {
    e.preventDefault();
    
    const form = this;
    const submitBtn = form.querySelector('button[type="submit"]');
    const btnText = submitBtn.querySelector('.btn-text');
    const btnLoading = submitBtn.querySelector('.btn-loading');
    const successDiv = document.getElementById('formSuccess');
    
    // Show loading state
    btnText.style.display = 'none';
    btnLoading.style.display = 'inline';
    submitBtn.disabled = true;
    
    // Submit form via AJAX
    fetch(form.action, {
        method: 'POST',
        body: new FormData(form)
    })
    .then(response => response.json())
    .then(data => {
        if (data.success) {
            form.style.display = 'none';
            successDiv.style.display = 'block';
            
            // Track conversion (Google Analytics, Facebook Pixel, etc.)
            if (typeof gtag !== 'undefined') {
                gtag('event', 'form_submit', {
                    'event_category': 'Contact',
                    'event_label': 'Contact Form'
                });
            }
        } else {
            alert(data.message || '{_w("An error occurred. Please try again.")}');
        }
    })
    .catch(error => {
        console.error('Error:', error);
        alert('{_w("An error occurred. Please try again.")}');
    })
    .finally(() => {
        // Reset button state
        btnText.style.display = 'inline';
        btnLoading.style.display = 'none';
        submitBtn.disabled = false;
    });
});
</script>
```

## SEO and Performance Optimization

### SEO Template Functions

**Template: `includes/seo-meta.html`**
```smarty
{* SEO meta tags and structured data *}
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">

{* Basic SEO Meta Tags *}
<title>{$wa->title()}</title>
<meta name="description" content="{$wa->meta('description')|escape}">
<meta name="keywords" content="{$wa->meta('keywords')|escape}">
<meta name="author" content="{$site_author|default:'Webasyst'}">

{* Open Graph Meta Tags *}
<meta property="og:title" content="{$wa->title()|escape}">
<meta property="og:description" content="{$wa->meta('description')|escape}">
<meta property="og:type" content="{$og_type|default:'website'}">
<meta property="og:url" content="{$wa->currentUrl()}">
<meta property="og:site_name" content="{$wa->accountName()}">

{if $featured_image}
    <meta property="og:image" content="{$featured_image}">
    <meta property="og:image:width" content="1200">
    <meta property="og:image:height" content="630">
{else}
    <meta property="og:image" content="{$wa_theme_url}img/og-default.jpg">
{/if}

{* Twitter Card Meta Tags *}
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="{$wa->title()|escape}">
<meta name="twitter:description" content="{$wa->meta('description')|escape}">
<meta name="twitter:image" content="{$featured_image|default:$wa_theme_url|cat:'img/og-default.jpg'}">

{if $twitter_site}
    <meta name="twitter:site" content="{$twitter_site}">
{/if}

{* Canonical URL *}
<link rel="canonical" href="{$wa->currentUrl()}">

{* Alternate Language Links *}
{if $languages}
    {foreach $languages as $lang}
        <link rel="alternate" hreflang="{$lang.code}" href="{$lang.url}">
    {/foreach}
{/if}

{* Schema.org Structured Data *}
<script type="application/ld+json">
{
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": "{$wa->accountName()|escape:'json'}",
    "url": "{$wa_url}",
    "logo": "{$wa_url}wa-content/img/logo.png",
    "description": "{$wa->meta('description')|escape:'json'}",
    "address": {
        "@type": "PostalAddress",
        "streetAddress": "{$company_address.street|escape:'json'}",
        "addressLocality": "{$company_address.city|escape:'json'}",
        "addressRegion": "{$company_address.state|escape:'json'}",
        "postalCode": "{$company_address.zip|escape:'json'}",
        "addressCountry": "{$company_address.country|escape:'json'}"
    },
    "contactPoint": {
        "@type": "ContactPoint",
        "telephone": "{$company_phone|escape:'json'}",
        "contactType": "Customer Service"
    },
    "sameAs": [
        {if $social_links.facebook}"{$social_links.facebook|escape:'json'}",{/if}
        {if $social_links.twitter}"{$social_links.twitter|escape:'json'}",{/if}
        {if $social_links.linkedin}"{$social_links.linkedin|escape:'json'}",{/if}
        {if $social_links.instagram}"{$social_links.instagram|escape:'json'}"({/if}
    ]
}
</script>

{* Page-specific structured data *}
{if $page_type == 'article'}
    <script type="application/ld+json">
    {
        "@context": "https://schema.org",
        "@type": "Article",
        "headline": "{$article.title|escape:'json'}",
        "description": "{$article.excerpt|escape:'json'}",
        "image": "{$article.featured_image|default:$wa_theme_url|cat:'img/og-default.jpg'}",
        "author": {
            "@type": "Person",
            "name": "{$article.author.name|escape:'json'}"
        },
        "publisher": {
            "@type": "Organization",
            "name": "{$wa->accountName()|escape:'json'}",
            "logo": {
                "@type": "ImageObject",
                "url": "{$wa_url}wa-content/img/logo.png"
            }
        },
        "datePublished": "{$article.date_iso}",
        "dateModified": "{$article.modified_iso|default:$article.date_iso}"
    }
    </script>
{/if}

{* Breadcrumb structured data *}
{if $breadcrumbs}
    <script type="application/ld+json">
    {
        "@context": "https://schema.org",
        "@type": "BreadcrumbList",
        "itemListElement": [
            {foreach $breadcrumbs as $crumb}
                {
                    "@type": "ListItem",
                    "position": {$crumb@iteration},
                    "name": "{$crumb.title|escape:'json'}",
                    "item": "{$crumb.url}"
                }{if !$crumb@last},{/if}
            {/foreach}
        ]
    }
    </script>
{/if}
```

## Multi-language Support

### Language Switcher Component

**Template: `includes/language-switcher.html`**
```smarty
{* Multi-language support component *}
{if $languages && count($languages) > 1}
    <div class="language-switcher">
        <div class="current-language">
            <button type="button" class="language-toggle" data-toggle="dropdown">
                <span class="language-flag">
                    <img src="{$wa_theme_url}img/flags/{$current_language.code}.png" 
                         alt="{$current_language.name}" class="flag-icon">
                </span>
                <span class="language-name">{$current_language.name}</span>
                <i class="fa fa-chevron-down"></i>
            </button>
        </div>
        
        <div class="language-dropdown">
            <ul class="language-list">
                {foreach $languages as $language}
                    <li class="language-item {if $language.code == $current_language.code}active{/if}">
                        <a href="{$language.url}" class="language-link" hreflang="{$language.code}">
                            <span class="language-flag">
                                <img src="{$wa_theme_url}img/flags/{$language.code}.png" 
                                     alt="{$language.name}" class="flag-icon">
                            </span>
                            <span class="language-name">{$language.name}</span>
                        </a>
                    </li>
                {/foreach}
            </ul>
        </div>
    </div>
{/if}
```

### Internationalization Helper Functions

**PHP Helper: `lib/SiteThemeHelper.class.php`**
```php
<?php

class SiteThemeHelper
{
    private $locale;
    private $translations = array();
    
    public function __construct($locale = 'en')
    {
        $this->locale = $locale;
        $this->loadTranslations();
    }
    
    /**
     * Get localized text
     */
    public function _w($text, $params = array())
    {
        $translation = isset($this->translations[$text]) 
            ? $this->translations[$text] 
            : $text;
            
        if (!empty($params)) {
            return vsprintf($translation, $params);
        }
        
        return $translation;
    }
    
    /**
     * Format date according to locale
     */
    public function formatDate($timestamp, $format = 'medium')
    {
        $formats = array(
            'en' => array(
                'short' => 'm/d/Y',
                'medium' => 'M j, Y',
                'long' => 'F j, Y',
                'full' => 'l, F j, Y'
            ),
            'ru' => array(
                'short' => 'd.m.Y',
                'medium' => 'd M Y',
                'long' => 'd F Y',
                'full' => 'l, d F Y'
            ),
            'de' => array(
                'short' => 'd.m.Y',
                'medium' => 'd. M Y',
                'long' => 'd. F Y',
                'full' => 'l, d. F Y'
            )
        );
        
        $locale_formats = isset($formats[$this->locale]) 
            ? $formats[$this->locale] 
            : $formats['en'];
            
        $date_format = isset($locale_formats[$format]) 
            ? $locale_formats[$format] 
            : $locale_formats['medium'];
            
        return date($date_format, $timestamp);
    }
    
    /**
     * Format currency according to locale
     */
    public function formatCurrency($amount, $currency = 'USD')
    {
        $currency_symbols = array(
            'USD' => '$',
            'EUR' => '€',
            'GBP' => '£',
            'RUB' => '₽',
            'JPY' => '¥'
        );
        
        $symbol = isset($currency_symbols[$currency]) 
            ? $currency_symbols[$currency] 
            : $currency;
            
        // Format based on locale
        switch ($this->locale) {
            case 'en':
                return $symbol . number_format($amount, 2);
            case 'de':
                return number_format($amount, 2, ',', '.') . ' ' . $symbol;
            case 'ru':
                return number_format($amount, 2, ',', ' ') . ' ' . $symbol;
            default:
                return $symbol . number_format($amount, 2);
        }
    }
    
    /**
     * Get localized URL
     */
    public function getLocalizedUrl($path = '', $language = null)
    {
        $language = $language ?: $this->locale;
        $base_url = wa()->getRouteUrl('site/frontend', array('locale' => $language));
        
        return $base_url . ltrim($path, '/');
    }
    
    /**
     * Load translations for current locale
     */
    private function loadTranslations()
    {
        $locale_file = wa()->getAppPath('locale/' . $this->locale . '.php', 'site');
        
        if (file_exists($locale_file)) {
            $this->translations = include($locale_file);
        }
    }
    
    /**
     * Get direction for current locale (LTR/RTL)
     */
    public function getDirection()
    {
        $rtl_languages = array('ar', 'he', 'fa', 'ur');
        return in_array($this->locale, $rtl_languages) ? 'rtl' : 'ltr';
    }
    
    /**
     * Get current locale
     */
    public function getLocale()
    {
        return $this->locale;
    }
}
```

This comprehensive Site app theme development guide covers all the essential aspects of creating professional website themes for Webasyst's Site application, including homepage customization, content management, search functionality, contact forms, SEO optimization, and internationalization support.