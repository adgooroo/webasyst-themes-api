# Core Blocks and Components for Webasyst Themes

## Table of Contents
1. [Core Components Overview](#core-components-overview)
2. [Authentication and Login Components](#authentication-and-login-components)
3. [Navigation and Menu Components](#navigation-and-menu-components)
4. [User Profile and Dashboard Components](#user-profile-and-dashboard-components)
5. [Access Level and Permissions Display](#access-level-and-permissions-display)
6. [Form Components](#form-components)
7. [Layout and UI Components](#layout-and-ui-components)
8. [Application-Specific Components](#application-specific-components)

## Core Components Overview

Webasyst themes are built using modular components that can be reused across different applications and pages. Understanding these core building blocks is essential for creating consistent, maintainable themes.

### Component Categories

1. **Authentication Components**: Login, registration, password recovery
2. **Navigation Components**: Main menus, breadcrumbs, application switching
3. **User Components**: Profile display, dashboard widgets, account management
4. **Permission Components**: Role-based content display, access controls
5. **Form Components**: Input fields, validation, submission handling
6. **Layout Components**: Headers, footers, sidebars, grids
7. **Content Components**: Articles, products, tickets, campaigns

### Component Structure

Each component typically consists of:
- **Template file** (`.html`): Smarty template for markup
- **CSS styles**: Component-specific styling
- **JavaScript**: Interactive functionality
- **PHP logic**: Backend data processing

## Authentication and Login Components

### Login Form Component

**Template: `components/auth/login-form.html`**
```smarty
<div class="auth-component login-form">
    <div class="auth-header">
        <h2 class="auth-title">{_w('Sign In')}</h2>
        <p class="auth-subtitle">{_w('Access your account')}</p>
    </div>

    {if $errors}
        <div class="alert alert-error" role="alert">
            <ul class="error-list">
                {foreach $errors as $error}
                    <li>{$error|escape}</li>
                {/foreach}
            </ul>
        </div>
    {/if}

    <form class="auth-form" action="{$wa_app_url}login/" method="post" novalidate>
        {$wa->csrf()}
        
        <div class="form-group">
            <label for="login-email" class="form-label">
                {_w('Email or Username')} *
            </label>
            <input type="text" 
                   id="login-email" 
                   name="login" 
                   class="form-control {if $errors.login}error{/if}"
                   value="{$request.login|escape}"
                   required 
                   autocomplete="username"
                   placeholder="user@example.com">
            {if $errors.login}
                <div class="field-error">{$errors.login|escape}</div>
            {/if}
        </div>

        <div class="form-group">
            <label for="login-password" class="form-label">
                {_w('Password')} *
            </label>
            <div class="password-field">
                <input type="password" 
                       id="login-password" 
                       name="password" 
                       class="form-control {if $errors.password}error{/if}"
                       required 
                       autocomplete="current-password"
                       placeholder="••••••••">
                <button type="button" class="password-toggle" aria-label="{_w('Show password')}">
                    <i class="icon-eye"></i>
                </button>
            </div>
            {if $errors.password}
                <div class="field-error">{$errors.password|escape}</div>
            {/if}
        </div>

        <div class="form-options">
            <label class="checkbox-label">
                <input type="checkbox" 
                       name="remember" 
                       value="1" 
                       {if $request.remember}checked{/if}>
                <span class="checkbox-mark"></span>
                {_w('Remember me')}
            </label>
            
            <a href="{$wa_app_url}forgotpassword/" class="forgot-link">
                {_w('Forgot password?')}
            </a>
        </div>

        <button type="submit" class="btn btn-primary btn-auth">
            <span class="btn-text">{_w('Sign In')}</span>
            <span class="btn-loader" style="display: none;">
                <i class="icon-spinner"></i>
            </span>
        </button>

        {if $oauth_providers}
            <div class="oauth-section">
                <div class="oauth-divider">
                    <span>{_w('or')}</span>
                </div>
                
                <div class="oauth-providers">
                    {foreach $oauth_providers as $provider}
                        <a href="{$provider.auth_url}" 
                           class="btn btn-oauth btn-{$provider.id}">
                            <i class="icon-{$provider.id}"></i>
                            {_w('Sign in with')} {$provider.name}
                        </a>
                    {/foreach}
                </div>
            </div>
        {/if}

        <div class="auth-footer">
            <p>{_w("Don't have an account?")} 
               <a href="{$wa_app_url}signup/">{_w('Sign up')}</a>
            </p>
        </div>
    </form>
</div>
```

### Registration Form Component

**Template: `components/auth/register-form.html`**
```smarty
<div class="auth-component register-form">
    <div class="auth-header">
        <h2 class="auth-title">{_w('Create Account')}</h2>
        <p class="auth-subtitle">{_w('Join our community')}</p>
    </div>

    <form class="auth-form" action="{$wa_app_url}signup/" method="post" novalidate>
        {$wa->csrf()}

        <div class="form-row">
            <div class="form-group">
                <label for="signup-firstname" class="form-label">
                    {_w('First Name')} *
                </label>
                <input type="text" 
                       id="signup-firstname" 
                       name="firstname" 
                       class="form-control {if $errors.firstname}error{/if}"
                       value="{$request.firstname|escape}"
                       required 
                       autocomplete="given-name">
                {if $errors.firstname}
                    <div class="field-error">{$errors.firstname|escape}</div>
                {/if}
            </div>

            <div class="form-group">
                <label for="signup-lastname" class="form-label">
                    {_w('Last Name')} *
                </label>
                <input type="text" 
                       id="signup-lastname" 
                       name="lastname" 
                       class="form-control {if $errors.lastname}error{/if}"
                       value="{$request.lastname|escape}"
                       required 
                       autocomplete="family-name">
                {if $errors.lastname}
                    <div class="field-error">{$errors.lastname|escape}</div>
                {/if}
            </div>
        </div>

        <div class="form-group">
            <label for="signup-email" class="form-label">
                {_w('Email Address')} *
            </label>
            <input type="email" 
                   id="signup-email" 
                   name="email" 
                   class="form-control {if $errors.email}error{/if}"
                   value="{$request.email|escape}"
                   required 
                   autocomplete="email">
            {if $errors.email}
                <div class="field-error">{$errors.email|escape}</div>
            {/if}
        </div>

        <div class="form-group">
            <label for="signup-password" class="form-label">
                {_w('Password')} *
            </label>
            <div class="password-field">
                <input type="password" 
                       id="signup-password" 
                       name="password" 
                       class="form-control {if $errors.password}error{/if}"
                       required 
                       autocomplete="new-password"
                       minlength="6">
                <div class="password-strength">
                    <div class="strength-bar"></div>
                    <span class="strength-text"></span>
                </div>
            </div>
            {if $errors.password}
                <div class="field-error">{$errors.password|escape}</div>
            {/if}
        </div>

        <div class="form-group">
            <label for="signup-password-confirm" class="form-label">
                {_w('Confirm Password')} *
            </label>
            <input type="password" 
                   id="signup-password-confirm" 
                   name="password_confirm" 
                   class="form-control {if $errors.password_confirm}error{/if}"
                   required 
                   autocomplete="new-password">
            {if $errors.password_confirm}
                <div class="field-error">{$errors.password_confirm|escape}</div>
            {/if}
        </div>

        <div class="form-group">
            <label class="checkbox-label">
                <input type="checkbox" 
                       name="terms" 
                       value="1" 
                       required>
                <span class="checkbox-mark"></span>
                {_w('I agree to the')} 
                <a href="/terms/" target="_blank">{_w('Terms of Service')}</a> 
                {_w('and')} 
                <a href="/privacy/" target="_blank">{_w('Privacy Policy')}</a>
            </label>
        </div>

        <button type="submit" class="btn btn-primary btn-auth">
            {_w('Create Account')}
        </button>

        <div class="auth-footer">
            <p>{_w('Already have an account?')} 
               <a href="{$wa_app_url}login/">{_w('Sign in')}</a>
            </p>
        </div>
    </form>
</div>
```

### User Authentication Status Component

**Template: `components/auth/user-status.html`**
```smarty
<div class="user-status-component">
    {if $wa->user()}
        {assign var="user" value=$wa->user()}
        <div class="user-authenticated">
            <div class="user-avatar">
                {if $user->getPhoto()}
                    <img src="{$user->getPhoto()}" 
                         alt="{$user->getName()|escape}"
                         class="avatar-image">
                {else}
                    <div class="avatar-placeholder">
                        {$user->getName()|substr:0:1|upper}
                    </div>
                {/if}
                
                {if $user->isOnline()}
                    <span class="status-indicator online" title="{_w('Online')}"></span>
                {/if}
            </div>

            <div class="user-info">
                <div class="user-name">{$user->getName()|escape}</div>
                <div class="user-role">
                    {if $user->isAdmin()}
                        <span class="role-badge admin">{_w('Administrator')}</span>
                    {elseif $user->isModerator()}
                        <span class="role-badge moderator">{_w('Moderator')}</span>
                    {else}
                        <span class="role-badge user">{_w('User')}</span>
                    {/if}
                </div>
            </div>

            <div class="user-dropdown">
                <button class="dropdown-toggle" type="button" aria-haspopup="true">
                    <i class="icon-chevron-down"></i>
                </button>
                
                <div class="dropdown-menu">
                    <a href="{$wa_app_url}profile/" class="dropdown-item">
                        <i class="icon-user"></i> {_w('Profile')}
                    </a>
                    <a href="{$wa_app_url}settings/" class="dropdown-item">
                        <i class="icon-settings"></i> {_w('Settings')}
                    </a>
                    
                    {if $user->isAdmin()}
                        <div class="dropdown-divider"></div>
                        <a href="{$wa_url}webasyst/" class="dropdown-item">
                            <i class="icon-admin"></i> {_w('Admin Panel')}
                        </a>
                    {/if}
                    
                    <div class="dropdown-divider"></div>
                    <a href="{$wa_app_url}logout/" class="dropdown-item logout">
                        <i class="icon-logout"></i> {_w('Sign Out')}
                    </a>
                </div>
            </div>
        </div>
    {else}
        <div class="user-guest">
            <div class="guest-actions">
                <a href="{$wa_app_url}login/" class="btn btn-outline">
                    {_w('Sign In')}
                </a>
                <a href="{$wa_app_url}signup/" class="btn btn-primary">
                    {_w('Sign Up')}
                </a>
            </div>
        </div>
    {/if}
</div>
```

## Navigation and Menu Components

### Main Navigation Component

**Template: `components/navigation/main-nav.html`**
```smarty
<nav class="main-navigation" role="navigation" aria-label="{_w('Main navigation')}">
    <div class="nav-container">
        {* Mobile menu toggle *}
        <button class="mobile-menu-toggle" 
                type="button" 
                aria-label="{_w('Toggle navigation menu')}"
                aria-expanded="false">
            <span class="hamburger">
                <span class="line"></span>
                <span class="line"></span>
                <span class="line"></span>
            </span>
        </button>

        {* Navigation menu *}
        <div class="nav-menu-wrapper">
            <ul class="nav-menu" role="menubar">
                {* Home link *}
                <li class="nav-item home-item" role="none">
                    <a href="{$wa_url}" 
                       class="nav-link {if $wa->getUrl() == $wa_url}current{/if}"
                       role="menuitem">
                        <i class="icon-home"></i>
                        <span class="nav-text">{_w('Home')}</span>
                    </a>
                </li>

                {* Application links *}
                {foreach $wa->apps() as $app}
                    {if $app.available}
                        <li class="nav-item app-item app-{$app.id} {if $wa->app() == $app.id}current{/if}" 
                            role="none">
                            <a href="{$app.url}" 
                               class="nav-link" 
                               role="menuitem"
                               {if $app.id == $wa->app()}aria-current="page"{/if}>
                                {if $app.icon}
                                    <i class="app-icon {$app.icon}"></i>
                                {/if}
                                <span class="nav-text">{$app.name|escape}</span>
                                
                                {* Notification badges *}
                                {if $app.notifications && $app.notifications > 0}
                                    <span class="notification-badge" 
                                          title="{$app.notifications} {_w('notifications')}">
                                        {$app.notifications}
                                    </span>
                                {/if}
                            </a>

                            {* Sub-navigation for complex apps *}
                            {if $app.subnav && $wa->app() == $app.id}
                                <ul class="sub-menu" role="menu">
                                    {foreach $app.subnav as $subitem}
                                        <li class="sub-item" role="none">
                                            <a href="{$subitem.url}" 
                                               class="sub-link {if $subitem.current}current{/if}"
                                               role="menuitem">
                                                {if $subitem.icon}
                                                    <i class="sub-icon {$subitem.icon}"></i>
                                                {/if}
                                                {$subitem.name|escape}
                                            </a>
                                        </li>
                                    {/foreach}
                                </ul>
                            {/if}
                        </li>
                    {/if}
                {/foreach}

                {* Additional custom menu items *}
                {if $custom_menu_items}
                    <li class="nav-divider" role="separator"></li>
                    {foreach $custom_menu_items as $item}
                        <li class="nav-item custom-item" role="none">
                            <a href="{$item.url}" 
                               class="nav-link {if $item.current}current{/if}"
                               role="menuitem"
                               {if $item.target}target="{$item.target}"{/if}>
                                {if $item.icon}
                                    <i class="custom-icon {$item.icon}"></i>
                                {/if}
                                <span class="nav-text">{$item.name|escape}</span>
                            </a>
                        </li>
                    {/foreach}
                {/if}
            </ul>
        </div>

        {* Search integration *}
        <div class="nav-search">
            {include file="components/forms/search-form.html"}
        </div>

        {* User status *}
        <div class="nav-user">
            {include file="components/auth/user-status.html"}
        </div>
    </div>
</nav>
```

### Breadcrumbs Component

**Template: `components/navigation/breadcrumbs.html`**
```smarty
{if $breadcrumbs && count($breadcrumbs) > 1}
    <nav class="breadcrumbs" aria-label="{_w('Breadcrumb navigation')}">
        <ol class="breadcrumb-list" itemscope itemtype="http://schema.org/BreadcrumbList">
            {foreach $breadcrumbs as $index => $crumb}
                <li class="breadcrumb-item {if $index == count($breadcrumbs) - 1}current{/if}" 
                    itemprop="itemListElement" 
                    itemscope 
                    itemtype="http://schema.org/ListItem">
                    
                    {if $crumb.url && $index < count($breadcrumbs) - 1}
                        <a href="{$crumb.url}" 
                           class="breadcrumb-link"
                           itemprop="item"
                           aria-current="{if $index == count($breadcrumbs) - 1}page{else}false{/if}">
                            <span itemprop="name">{$crumb.name|escape}</span>
                        </a>
                    {else}
                        <span class="breadcrumb-text" itemprop="name">
                            {$crumb.name|escape}
                        </span>
                    {/if}
                    
                    <meta itemprop="position" content="{$index + 1}">
                    
                    {if $index < count($breadcrumbs) - 1}
                        <span class="breadcrumb-separator" aria-hidden="true">
                            <i class="icon-chevron-right"></i>
                        </span>
                    {/if}
                </li>
            {/foreach}
        </ol>
    </nav>
{/if}
```

### Application Switcher Component

**Template: `components/navigation/app-switcher.html`**
```smarty
<div class="app-switcher-component">
    <button class="app-switcher-toggle" 
            type="button"
            aria-label="{_w('Switch application')}"
            aria-haspopup="true"
            aria-expanded="false">
        <div class="current-app">
            {assign var="current_app" value=$wa->getApp()}
            {if $current_app.icon}
                <i class="current-app-icon {$current_app.icon}"></i>
            {/if}
            <span class="current-app-name">{$current_app.name|escape}</span>
        </div>
        <i class="switcher-arrow icon-chevron-down"></i>
    </button>

    <div class="app-switcher-menu" role="menu">
        <div class="apps-grid">
            {foreach $wa->apps() as $app}
                {if $app.available}
                    <a href="{$app.url}" 
                       class="app-card {if $wa->app() == $app.id}current{/if}"
                       role="menuitem">
                        <div class="app-icon-wrapper">
                            {if $app.icon}
                                <i class="app-icon {$app.icon}"></i>
                            {else}
                                <div class="app-icon-placeholder">
                                    {$app.name|substr:0:2|upper}
                                </div>
                            {/if}
                            
                            {if $app.notifications && $app.notifications > 0}
                                <span class="app-badge">{$app.notifications}</span>
                            {/if}
                        </div>
                        
                        <div class="app-info">
                            <div class="app-name">{$app.name|escape}</div>
                            {if $app.description}
                                <div class="app-description">
                                    {$app.description|escape|truncate:50}
                                </div>
                            {/if}
                        </div>
                    </a>
                {/if}
            {/foreach}
        </div>

        {if $wa->user() && $wa->user()->isAdmin()}
            <div class="switcher-footer">
                <a href="{$wa_url}webasyst/installer/" class="install-apps-link">
                    <i class="icon-plus"></i>
                    {_w('Install Apps')}
                </a>
            </div>
        {/if}
    </div>
</div>
```

## User Profile and Dashboard Components

### User Profile Card Component

**Template: `components/user/profile-card.html`**
```smarty
{if $profile_user}
    <div class="profile-card-component">
        <div class="profile-header">
            <div class="profile-avatar">
                {if $profile_user->getPhoto()}
                    <img src="{$profile_user->getPhoto()}" 
                         alt="{$profile_user->getName()|escape}"
                         class="avatar-large">
                {else}
                    <div class="avatar-placeholder-large">
                        {$profile_user->getName()|substr:0:2|upper}
                    </div>
                {/if}
                
                {if $can_edit_profile}
                    <button class="avatar-edit-btn" type="button" title="{_w('Change avatar')}">
                        <i class="icon-camera"></i>
                    </button>
                {/if}
            </div>

            <div class="profile-info">
                <h2 class="profile-name">{$profile_user->getName()|escape}</h2>
                
                {if $profile_user->get('title')}
                    <div class="profile-title">{$profile_user->get('title')|escape}</div>
                {/if}

                <div class="profile-meta">
                    <div class="profile-status">
                        {if $profile_user->isOnline()}
                            <span class="status-indicator online"></span>
                            {_w('Online')}
                        {else}
                            <span class="status-indicator offline"></span>
                            {_w('Last seen')} {$profile_user->getLastActivity()|wa_datetime}
                        {/if}
                    </div>

                    {if $profile_user->get('create_datetime')}
                        <div class="profile-joined">
                            {_w('Joined')} {$profile_user->get('create_datetime')|date_format:"%B %Y"}
                        </div>
                    {/if}
                </div>
            </div>
        </div>

        <div class="profile-body">
            {if $profile_user->get('about')}
                <div class="profile-about">
                    <h3>{_w('About')}</h3>
                    <p>{$profile_user->get('about')|escape|nl2br}</p>
                </div>
            {/if}

            <div class="profile-contact">
                <h3>{_w('Contact Information')}</h3>
                <dl class="contact-list">
                    <dt>{_w('Email')}</dt>
                    <dd>
                        {if $show_email || $can_edit_profile}
                            <a href="mailto:{$profile_user->getEmail()}">
                                {$profile_user->getEmail()|escape}
                            </a>
                        {else}
                            <span class="text-muted">{_w('Hidden')}</span>
                        {/if}
                    </dd>

                    {if $profile_user->get('phone')}
                        <dt>{_w('Phone')}</dt>
                        <dd>
                            <a href="tel:{$profile_user->get('phone')}">
                                {$profile_user->get('phone')|escape}
                            </a>
                        </dd>
                    {/if}

                    {if $profile_user->get('company')}
                        <dt>{_w('Company')}</dt>
                        <dd>{$profile_user->get('company')|escape}</dd>
                    {/if}

                    {if $profile_user->get('location')}
                        <dt>{_w('Location')}</dt>
                        <dd>{$profile_user->get('location')|escape}</dd>
                    {/if}
                </dl>
            </div>

            {if $profile_user->getSocialNetworks()}
                <div class="profile-social">
                    <h3>{_w('Social Networks')}</h3>
                    <div class="social-links">
                        {foreach $profile_user->getSocialNetworks() as $network => $url}
                            <a href="{$url}" 
                               class="social-link social-{$network}"
                               target="_blank"
                               rel="noopener noreferrer"
                               title="{$network|capitalize}">
                                <i class="icon-{$network}"></i>
                            </a>
                        {/foreach}
                    </div>
                </div>
            {/if}
        </div>

        {if $can_edit_profile}
            <div class="profile-actions">
                <a href="{$wa_app_url}profile/edit/" class="btn btn-primary">
                    <i class="icon-edit"></i>
                    {_w('Edit Profile')}
                </a>
            </div>
        {/if}
    </div>
{/if}
```

### Dashboard Widget Component

**Template: `components/user/dashboard-widget.html`**
```smarty
<div class="dashboard-widget {$widget.class|default:''}">
    <div class="widget-header">
        <h3 class="widget-title">
            {if $widget.icon}
                <i class="widget-icon {$widget.icon}"></i>
            {/if}
            {$widget.title|escape}
        </h3>

        {if $widget.actions}
            <div class="widget-actions">
                {foreach $widget.actions as $action}
                    <a href="{$action.url}" 
                       class="widget-action {$action.class|default:''}"
                       {if $action.target}target="{$action.target}"{/if}
                       title="{$action.title|escape}">
                        {if $action.icon}
                            <i class="action-icon {$action.icon}"></i>
                        {/if}
                        {if $action.text}
                            <span class="action-text">{$action.text|escape}</span>
                        {/if}
                    </a>
                {/foreach}
            </div>
        {/if}

        {if $widget.collapsible}
            <button class="widget-toggle" 
                    type="button"
                    aria-label="{_w('Toggle widget')}"
                    data-widget="{$widget.id}">
                <i class="icon-chevron-up"></i>
            </button>
        {/if}
    </div>

    <div class="widget-body" {if $widget.collapsed}style="display: none;"{/if}>
        {if $widget.loading}
            <div class="widget-loading">
                <div class="loading-spinner">
                    <i class="icon-spinner icon-spin"></i>
                </div>
                <p class="loading-text">{_w('Loading...')}</p>
            </div>
        {else}
            {if $widget.content}
                {$widget.content}
            {elseif $widget.template}
                {include file=$widget.template data=$widget.data}
            {else}
                <div class="widget-empty">
                    <i class="empty-icon icon-info"></i>
                    <p class="empty-text">{_w('No content available')}</p>
                </div>
            {/if}
        {/if}
    </div>

    {if $widget.footer}
        <div class="widget-footer">
            {$widget.footer}
        </div>
    {/if}
</div>
```

## Access Level and Permissions Display

### Permission-Based Content Component

**Template: `components/access/permission-content.html`**
```smarty
{* Component for displaying content based on user permissions *}

{function name="check_permission" permission="" app="" user=null}
    {if !$user}
        {assign var="user" value=$wa->user()}
    {/if}
    
    {if !$user}
        {assign var="has_permission" value=false}
    {elseif $user->isAdmin()}
        {assign var="has_permission" value=true}
    {elseif $app}
        {assign var="has_permission" value=$user->getRights($app, $permission)}
    {else}
        {assign var="has_permission" value=$user->getRights('webasyst', $permission)}
    {/if}
{/function}

<div class="permission-content-component">
    {* Admin-only content *}
    {call name="check_permission" permission="admin"}
    {if $has_permission}
        <div class="admin-content">
            <div class="admin-header">
                <i class="icon-admin-crown"></i>
                <h3>{_w('Administrator Controls')}</h3>
            </div>
            
            <div class="admin-actions">
                <a href="{$wa_url}webasyst/" class="btn btn-admin">
                    <i class="icon-settings"></i>
                    {_w('System Settings')}
                </a>
                
                <a href="{$wa_url}webasyst/users/" class="btn btn-admin">
                    <i class="icon-users"></i>
                    {_w('User Management')}
                </a>
                
                <a href="{$wa_url}webasyst/installer/" class="btn btn-admin">
                    <i class="icon-apps"></i>
                    {_w('App Installer')}
                </a>
            </div>
        </div>
    {/if}

    {* Moderator content *}
    {call name="check_permission" permission="moderate"}
    {if $has_permission}
        <div class="moderator-content">
            <div class="moderator-header">
                <i class="icon-shield"></i>
                <h3>{_w('Moderation Tools')}</h3>
            </div>
            
            <div class="moderation-stats">
                {if $pending_items}
                    <div class="stat-item urgent">
                        <span class="stat-number">{$pending_items}</span>
                        <span class="stat-label">{_w('Pending Review')}</span>
                    </div>
                {/if}
                
                {if $reported_items}
                    <div class="stat-item warning">
                        <span class="stat-number">{$reported_items}</span>
                        <span class="stat-label">{_w('Reported Items')}</span>
                    </div>
                {/if}
            </div>
        </div>
    {/if}

    {* Application-specific permissions *}
    {if $wa->app() == 'shop'}
        {call name="check_permission" permission="products" app="shop"}
        {if $has_permission}
            <div class="app-permission-content">
                <h4>{_w('Product Management')}</h4>
                <div class="permission-actions">
                    <a href="{$wa_app_url}products/add/" class="btn btn-success">
                        <i class="icon-plus"></i>
                        {_w('Add Product')}
                    </a>
                    
                    <a href="{$wa_app_url}products/import/" class="btn btn-outline">
                        <i class="icon-upload"></i>
                        {_w('Import Products')}
                    </a>
                </div>
            </div>
        {/if}

        {call name="check_permission" permission="orders" app="shop"}
        {if $has_permission}
            <div class="app-permission-content">
                <h4>{_w('Order Management')}</h4>
                {if $new_orders}
                    <div class="alert alert-info">
                        <i class="icon-notification"></i>
                        {_wp('%d new order', '%d new orders', $new_orders, $new_orders)}
                    </div>
                {/if}
            </div>
        {/if}
    {/if}

    {* Role-based content display *}
    {if $wa->user()}
        <div class="role-based-content">
            {assign var="user_roles" value=$wa->user()->getRoles()}
            
            {foreach $user_roles as $role}
                <div class="role-content role-{$role.id}">
                    {if $role.id == 'admin'}
                        <div class="role-badge admin">
                            <i class="icon-admin"></i>
                            {_w('Administrator')}
                        </div>
                    {elseif $role.id == 'manager'}
                        <div class="role-badge manager">
                            <i class="icon-briefcase"></i>
                            {_w('Manager')}
                        </div>
                    {elseif $role.id == 'editor'}
                        <div class="role-badge editor">
                            <i class="icon-edit"></i>
                            {_w('Editor')}
                        </div>
                    {else}
                        <div class="role-badge user">
                            <i class="icon-user"></i>
                            {_w('User')}
                        </div>
                    {/if}
                </div>
            {/foreach}
        </div>
    {/if}

    {* Access level indicator *}
    <div class="access-level-indicator">
        {if $wa->user()}
            {assign var="access_level" value=$wa->user()->getAccessLevel()}
            
            <div class="access-badge level-{$access_level}">
                {if $access_level >= 3}
                    <i class="icon-key-admin"></i>
                    <span>{_w('Full Access')}</span>
                {elseif $access_level >= 2}
                    <i class="icon-key-manager"></i>
                    <span>{_w('Manager Access')}</span>
                {elseif $access_level >= 1}
                    <i class="icon-key-user"></i>
                    <span>{_w('User Access')}</span>
                {else}
                    <i class="icon-key-guest"></i>
                    <span>{_w('Limited Access')}</span>
                {/if}
            </div>
        {else}
            <div class="access-badge guest">
                <i class="icon-key-guest"></i>
                <span>{_w('Guest Access')}</span>
            </div>
        {/if}
    </div>
</div>
```

### Permission Check Helper Component

**Template: `components/access/permission-helper.html`**
```smarty
{* Reusable permission checking functions *}

{function name="has_app_access" app_id="" user=null}
    {if !$user}
        {assign var="user" value=$wa->user()}
    {/if}
    
    {if !$user}
        {assign var="result" value=false}
    {elseif $user->isAdmin()}
        {assign var="result" value=true}
    {else}
        {assign var="apps" value=$user->getApps()}
        {assign var="result" value=isset($apps[$app_id])}
    {/if}
{/function}

{function name="can_edit_content" content_id="" content_type="" user=null}
    {if !$user}
        {assign var="user" value=$wa->user()}
    {/if}
    
    {if !$user}
        {assign var="can_edit" value=false}
    {elseif $user->isAdmin()}
        {assign var="can_edit" value=true}
    {else}
        {* Check if user owns the content or has edit permissions *}
        {if $content_owner_id == $user->getId()}
            {assign var="can_edit" value=true}
        {else}
            {assign var="can_edit" value=$user->getRights($wa->app(), 'edit_' . $content_type)}
        {/if}
    {/if}
{/function}

{function name="show_if_permitted" permission="" app="" fallback=""}
    {if $wa->user() && ($wa->user()->isAdmin() || $wa->user()->getRights($app|default:$wa->app(), $permission))}
        {assign var="show_content" value=true}
    {else}
        {assign var="show_content" value=false}
        {if $fallback}
            <div class="permission-fallback">
                {$fallback}
            </div>
        {/if}
    {/if}
{/function}

{* Usage examples *}

{* Check if user can access shop app *}
{call name="has_app_access" app_id="shop"}
{if $result}
    <a href="{$wa_url}shop/" class="app-link">Shop</a>
{/if}

{* Check if user can edit specific content *}
{call name="can_edit_content" content_id=$article.id content_type="article" content_owner_id=$article.contact_id}
{if $can_edit}
    <a href="{$wa_app_url}article/edit/{$article.id}/" class="btn btn-edit">
        {_w('Edit Article')}
    </a>
{/if}

{* Conditional content display *}
{call name="show_if_permitted" permission="admin" fallback="<p>You don't have permission to view this content.</p>"}
{if $show_content}
    <div class="admin-only-content">
        {* Admin content here *}
    </div>
{/if}
```

This comprehensive guide to core blocks and components provides the foundation for building consistent, permission-aware, and user-friendly Webasyst themes. These components can be combined and customized to create rich user interfaces that adapt to different user roles and access levels while maintaining security and usability.