# CRM, Helpdesk, Mailer & Hub Application Themes

## Table of Contents
1. [CRM Application Themes](#crm-application-themes)
2. [Helpdesk Application Themes](#helpdesk-application-themes)
3. [Mailer Application Themes](#mailer-application-themes)
4. [Hub Application Themes](#hub-application-themes)
5. [Cross-Application Theme Integration](#cross-application-theme-integration)
6. [Shared Components and Patterns](#shared-components-and-patterns)

## CRM Application Themes

The CRM application in Webasyst focuses on customer relationship management, featuring contact management, sales pipeline visualization, deal tracking, and activity management.

### CRM Theme Structure

```
crm-theme/
├── theme.xml                    # Theme manifest
├── layout.html                  # Base CRM layout
├── dashboard.html               # Main dashboard
├── contacts/                    # Contact management
│   ├── list.html               # Contacts listing
│   ├── view.html               # Contact details
│   ├── edit.html               # Contact editing
│   └── card.html               # Contact card component
├── deals/                       # Deal management
│   ├── pipeline.html           # Sales pipeline/Kanban
│   ├── list.html               # Deal listing
│   ├── view.html               # Deal details
│   └── form.html               # Deal form
├── activities/                  # Activity tracking
│   ├── feed.html               # Activity feed
│   ├── calendar.html           # Calendar view
│   └── task.html               # Task component
├── reports/                     # Analytics and reports
│   ├── dashboard.html          # Reports dashboard
│   ├── charts.html             # Chart components
│   └── export.html             # Export functionality
└── includes/                    # Shared components
    ├── kanban-board.html       # Kanban board
    ├── contact-widget.html     # Contact sidebar
    ├── activity-item.html      # Activity list item
    └── quick-add.html          # Quick add forms
```

### CRM Dashboard Template

**Template: `dashboard.html`**
```smarty
{* CRM Dashboard with KPIs and activity overview *}
<div class="crm-dashboard">
    <header class="dashboard-header">
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-8">
                    <h1 class="dashboard-title">{_w('CRM Dashboard')}</h1>
                    <p class="dashboard-subtitle">{_w('Welcome back, %s', $user.name)}</p>
                </div>
                <div class="col-md-4">
                    <div class="dashboard-actions">
                        <a href="{$wa_app_url}contact/add" class="btn btn-primary">
                            <i class="fa fa-plus"></i> {_w('Add Contact')}
                        </a>
                        <a href="{$wa_app_url}deal/add" class="btn btn-success">
                            <i class="fa fa-handshake-o"></i> {_w('New Deal')}
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <div class="dashboard-content">
        <div class="container-fluid">
            {* KPI Cards *}
            <div class="row dashboard-stats">
                <div class="col-md-3">
                    <div class="stat-card stat-primary">
                        <div class="stat-icon">
                            <i class="fa fa-users"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.total_contacts}</h3>
                            <p class="stat-label">{_w('Total Contacts')}</p>
                            <span class="stat-change positive">
                                +{$stats.new_contacts_this_month} {_w('this month')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-success">
                        <div class="stat-icon">
                            <i class="fa fa-trophy"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.closed_deals}</h3>
                            <p class="stat-label">{_w('Closed Deals')}</p>
                            <span class="stat-change positive">
                                {$stats.deal_close_rate}% {_w('close rate')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-warning">
                        <div class="stat-icon">
                            <i class="fa fa-dollar"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.revenue|money}</h3>
                            <p class="stat-label">{_w('Revenue This Month')}</p>
                            <span class="stat-change {if $stats.revenue_change > 0}positive{else}negative{/if}">
                                {if $stats.revenue_change > 0}+{/if}{$stats.revenue_change}%
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-info">
                        <div class="stat-icon">
                            <i class="fa fa-tasks"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.pending_tasks}</h3>
                            <p class="stat-label">{_w('Pending Tasks')}</p>
                            <span class="stat-change neutral">
                                {$stats.overdue_tasks} {_w('overdue')}
                            </span>
                        </div>
                    </div>
                </div>
            </div>

            <div class="row">
                {* Sales Pipeline *}
                <div class="col-md-8">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Sales Pipeline')}</h3>
                            <div class="widget-actions">
                                <a href="{$wa_app_url}deal/pipeline" class="btn btn-sm btn-default">
                                    {_w('View Full Pipeline')}
                                </a>
                            </div>
                        </div>
                        <div class="widget-content">
                            {include file="$wa_theme_path/includes/kanban-board.html" deals=$pipeline_deals}
                        </div>
                    </div>
                </div>

                {* Recent Activities *}
                <div class="col-md-4">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Recent Activities')}</h3>
                            <div class="widget-actions">
                                <a href="{$wa_app_url}activity/" class="btn btn-sm btn-default">
                                    {_w('View All')}
                                </a>
                            </div>
                        </div>
                        <div class="widget-content">
                            <div class="activity-feed">
                                {foreach $recent_activities as $activity}
                                    {include file="$wa_theme_path/includes/activity-item.html" activity=$activity}
                                {/foreach}
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="row">
                {* Revenue Chart *}
                <div class="col-md-6">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Revenue Trend')}</h3>
                        </div>
                        <div class="widget-content">
                            <canvas id="revenueChart" width="400" height="200"></canvas>
                        </div>
                    </div>
                </div>

                {* Top Performers *}
                <div class="col-md-6">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Top Performers')}</h3>
                        </div>
                        <div class="widget-content">
                            <div class="performers-list">
                                {foreach $top_performers as $performer}
                                    <div class="performer-item">
                                        <div class="performer-avatar">
                                            <img src="{$performer.avatar}" alt="{$performer.name}">
                                        </div>
                                        <div class="performer-info">
                                            <h4 class="performer-name">{$performer.name}</h4>
                                            <p class="performer-stats">
                                                {$performer.deals_closed} {_w('deals')} • 
                                                {$performer.revenue|money}
                                            </p>
                                        </div>
                                        <div class="performer-score">
                                            <span class="score">{$performer.score}%</span>
                                        </div>
                                    </div>
                                {/foreach}
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### Kanban Board Component

**Template: `includes/kanban-board.html`**
```smarty
{* Kanban board for sales pipeline *}
<div class="kanban-board" id="dealsPipeline">
    <div class="kanban-columns">
        {foreach $pipeline_stages as $stage}
            <div class="kanban-column" data-stage-id="{$stage.id}">
                <div class="kanban-header">
                    <h4 class="stage-title">{$stage.name}</h4>
                    <div class="stage-stats">
                        <span class="deal-count">{count($stage.deals)}</span>
                        <span class="stage-value">{$stage.total_value|money}</span>
                    </div>
                </div>
                
                <div class="kanban-body" data-droppable="true">
                    {foreach $stage.deals as $deal}
                        <div class="deal-card" data-deal-id="{$deal.id}" draggable="true">
                            <div class="deal-header">
                                <h5 class="deal-title">
                                    <a href="{$wa_app_url}deal/{$deal.id}">{$deal.name}</a>
                                </h5>
                                <div class="deal-value">{$deal.amount|money}</div>
                            </div>
                            
                            <div class="deal-meta">
                                <div class="deal-contact">
                                    <i class="fa fa-user"></i>
                                    <a href="{$wa_app_url}contact/{$deal.contact.id}">
                                        {$deal.contact.name}
                                    </a>
                                </div>
                                
                                <div class="deal-company">
                                    <i class="fa fa-building"></i>
                                    {$deal.contact.company}
                                </div>
                                
                                <div class="deal-date">
                                    <i class="fa fa-calendar"></i>
                                    {$deal.expected_close_date|date_format:"%b %d"}
                                </div>
                            </div>
                            
                            <div class="deal-progress">
                                <div class="progress-bar">
                                    <div class="progress-fill" style="width: {$deal.probability}%"></div>
                                </div>
                                <span class="probability">{$deal.probability}%</span>
                            </div>
                            
                            <div class="deal-actions">
                                <button class="btn btn-xs btn-primary" onclick="editDeal({$deal.id})">
                                    <i class="fa fa-edit"></i>
                                </button>
                                <button class="btn btn-xs btn-success" onclick="addActivity({$deal.id})">
                                    <i class="fa fa-plus"></i>
                                </button>
                            </div>
                        </div>
                    {/foreach}
                </div>
            </div>
        {/foreach}
    </div>
</div>

<script>
// Kanban drag and drop functionality
document.addEventListener('DOMContentLoaded', function() {
    const kanbanBoard = document.getElementById('dealsPipeline');
    
    // Make deal cards draggable
    const dealCards = kanbanBoard.querySelectorAll('.deal-card');
    dealCards.forEach(card => {
        card.addEventListener('dragstart', function(e) {
            e.dataTransfer.setData('text/plain', this.dataset.dealId);
            this.classList.add('dragging');
        });
        
        card.addEventListener('dragend', function(e) {
            this.classList.remove('dragging');
        });
    });
    
    // Make columns droppable
    const columns = kanbanBoard.querySelectorAll('.kanban-body');
    columns.forEach(column => {
        column.addEventListener('dragover', function(e) {
            e.preventDefault();
            this.classList.add('drag-over');
        });
        
        column.addEventListener('dragleave', function(e) {
            this.classList.remove('drag-over');
        });
        
        column.addEventListener('drop', function(e) {
            e.preventDefault();
            this.classList.remove('drag-over');
            
            const dealId = e.dataTransfer.getData('text/plain');
            const newStageId = this.closest('.kanban-column').dataset.stageId;
            
            // Update deal stage via AJAX
            fetch('{$wa_app_url}deal/move', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'X-Requested-With': 'XMLHttpRequest'
                },
                body: JSON.stringify({
                    deal_id: dealId,
                    stage_id: newStageId,
                    _csrf: '{$wa->csrf()}'
                })
            })
            .then(response => response.json())
            .then(data => {
                if (data.success) {
                    // Move the card to new column
                    const draggedCard = document.querySelector(`[data-deal-id="${dealId}"]`);
                    this.appendChild(draggedCard);
                    
                    // Update stage statistics
                    updateStageStats();
                } else {
                    alert(data.message || 'Failed to move deal');
                }
            })
            .catch(error => {
                console.error('Error:', error);
                alert('An error occurred while moving the deal');
            });
        });
    });
});
</script>
```

## Helpdesk Application Themes

The Helpdesk application manages customer support tickets, knowledge base, and customer service interactions.

### Helpdesk Theme Structure

```
helpdesk-theme/
├── theme.xml                    # Theme manifest
├── layout.html                  # Base layout
├── dashboard.html               # Support dashboard
├── tickets/                     # Ticket management
│   ├── list.html               # Ticket listing
│   ├── view.html               # Ticket details
│   ├── form.html               # New ticket form
│   └── widget.html             # Ticket widget
├── knowledge-base/              # Knowledge base
│   ├── index.html              # KB homepage
│   ├── category.html           # Article category
│   ├── article.html            # Article view
│   └── search.html             # KB search
├── customer/                    # Customer portal
│   ├── portal.html             # Customer dashboard
│   ├── submit.html             # Submit ticket
│   └── history.html            # Ticket history
└── includes/                    # Components
    ├── ticket-card.html        # Ticket card
    ├── chat-widget.html        # Live chat
    ├── rating-form.html        # Satisfaction rating
    └── escalation-panel.html   # Escalation controls
```

### Helpdesk Dashboard

**Template: `dashboard.html`**
```smarty
{* Helpdesk support dashboard *}
<div class="helpdesk-dashboard">
    <header class="dashboard-header">
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-6">
                    <h1 class="dashboard-title">{_w('Support Dashboard')}</h1>
                    <p class="dashboard-subtitle">{_w('Manage customer support efficiently')}</p>
                </div>
                <div class="col-md-6">
                    <div class="dashboard-filters">
                        <select class="form-control" id="agentFilter">
                            <option value="">{_w('All Agents')}</option>
                            {foreach $agents as $agent}
                                <option value="{$agent.id}">{$agent.name}</option>
                            {/foreach}
                        </select>
                        
                        <select class="form-control" id="statusFilter">
                            <option value="">{_w('All Statuses')}</option>
                            <option value="new">{_w('New')}</option>
                            <option value="open">{_w('Open')}</option>
                            <option value="pending">{_w('Pending')}</option>
                            <option value="resolved">{_w('Resolved')}</option>
                        </select>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <div class="dashboard-content">
        <div class="container-fluid">
            {* Support Metrics *}
            <div class="row dashboard-stats">
                <div class="col-md-3">
                    <div class="stat-card stat-danger">
                        <div class="stat-icon">
                            <i class="fa fa-exclamation-triangle"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.urgent_tickets}</h3>
                            <p class="stat-label">{_w('Urgent Tickets')}</p>
                            <span class="stat-change urgent">
                                {_w('Needs immediate attention')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-warning">
                        <div class="stat-icon">
                            <i class="fa fa-clock-o"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.avg_response_time}</h3>
                            <p class="stat-label">{_w('Avg Response Time')}</p>
                            <span class="stat-change {if $stats.response_time_trend > 0}negative{else}positive{/if}">
                                {if $stats.response_time_trend > 0}+{/if}{$stats.response_time_trend}% {_w('vs last week')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-success">
                        <div class="stat-icon">
                            <i class="fa fa-thumbs-up"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.satisfaction_rate}%</h3>
                            <p class="stat-label">{_w('Satisfaction Rate')}</p>
                            <span class="stat-change positive">
                                +{$stats.satisfaction_improvement}% {_w('this month')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-info">
                        <div class="stat-icon">
                            <i class="fa fa-check-circle"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.resolution_rate}%</h3>
                            <p class="stat-label">{_w('First Contact Resolution')}</p>
                            <span class="stat-change positive">
                                {$stats.resolved_today} {_w('resolved today')}
                            </span>
                        </div>
                    </div>
                </div>
            </div>

            <div class="row">
                {* Active Tickets Queue *}
                <div class="col-md-8">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Active Tickets')}</h3>
                            <div class="widget-tabs">
                                <ul class="nav nav-tabs">
                                    <li class="active">
                                        <a href="#my-tickets" data-toggle="tab">{_w('My Tickets')}</a>
                                    </li>
                                    <li>
                                        <a href="#unassigned" data-toggle="tab">{_w('Unassigned')}</a>
                                    </li>
                                    <li>
                                        <a href="#escalated" data-toggle="tab">{_w('Escalated')}</a>
                                    </li>
                                </ul>
                            </div>
                        </div>
                        <div class="widget-content">
                            <div class="tab-content">
                                <div class="tab-pane active" id="my-tickets">
                                    <div class="tickets-list">
                                        {foreach $my_tickets as $ticket}
                                            {include file="$wa_theme_path/includes/ticket-card.html" ticket=$ticket}
                                        {/foreach}
                                    </div>
                                </div>
                                
                                <div class="tab-pane" id="unassigned">
                                    <div class="tickets-list">
                                        {foreach $unassigned_tickets as $ticket}
                                            {include file="$wa_theme_path/includes/ticket-card.html" ticket=$ticket}
                                        {/foreach}
                                    </div>
                                </div>
                                
                                <div class="tab-pane" id="escalated">
                                    <div class="tickets-list">
                                        {foreach $escalated_tickets as $ticket}
                                            {include file="$wa_theme_path/includes/ticket-card.html" ticket=$ticket}
                                        {/foreach}
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                {* Team Performance *}
                <div class="col-md-4">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Team Performance')}</h3>
                        </div>
                        <div class="widget-content">
                            <div class="agent-performance">
                                {foreach $agent_stats as $agent}
                                    <div class="agent-item">
                                        <div class="agent-avatar">
                                            <img src="{$agent.avatar}" alt="{$agent.name}">
                                            <span class="status-indicator {$agent.status}"></span>
                                        </div>
                                        <div class="agent-info">
                                            <h4 class="agent-name">{$agent.name}</h4>
                                            <p class="agent-stats">
                                                {$agent.active_tickets} {_w('active')} • 
                                                {$agent.resolved_today} {_w('resolved today')}
                                            </p>
                                            <div class="performance-bar">
                                                <div class="performance-fill" 
                                                     style="width: {$agent.performance_score}%"></div>
                                            </div>
                                        </div>
                                    </div>
                                {/foreach}
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### Ticket Card Component

**Template: `includes/ticket-card.html`**
```smarty
{* Individual ticket card for lists *}
<div class="ticket-card priority-{$ticket.priority}" data-ticket-id="{$ticket.id}">
    <div class="ticket-header">
        <div class="ticket-id">
            <span class="ticket-number">#{$ticket.id}</span>
            <span class="priority-badge priority-{$ticket.priority}">
                {$ticket.priority_label}
            </span>
        </div>
        <div class="ticket-actions">
            <div class="dropdown">
                <button class="btn btn-xs btn-default dropdown-toggle" data-toggle="dropdown">
                    <i class="fa fa-ellipsis-h"></i>
                </button>
                <ul class="dropdown-menu">
                    <li><a href="{$wa_app_url}ticket/{$ticket.id}">{_w('View')}</a></li>
                    <li><a href="#" onclick="assignTicket({$ticket.id})">{_w('Assign')}</a></li>
                    <li><a href="#" onclick="escalateTicket({$ticket.id})">{_w('Escalate')}</a></li>
                    <li class="divider"></li>
                    <li><a href="#" onclick="closeTicket({$ticket.id})">{_w('Close')}</a></li>
                </ul>
            </div>
        </div>
    </div>
    
    <div class="ticket-content">
        <h4 class="ticket-subject">
            <a href="{$wa_app_url}ticket/{$ticket.id}">{$ticket.subject}</a>
        </h4>
        
        <p class="ticket-preview">{$ticket.preview}</p>
        
        <div class="ticket-meta">
            <div class="customer-info">
                <img src="{$ticket.customer.avatar}" alt="{$ticket.customer.name}" class="customer-avatar">
                <span class="customer-name">{$ticket.customer.name}</span>
                <span class="customer-email">{$ticket.customer.email}</span>
            </div>
            
            <div class="ticket-timing">
                <time class="created-time" datetime="{$ticket.created_datetime}">
                    {$ticket.created_datetime|wa_datetime:"humandiff"}
                </time>
                
                {if $ticket.last_response_time}
                    <span class="last-response">
                        {_w('Last response')}: {$ticket.last_response_time|wa_datetime:"humandiff"}
                    </span>
                {/if}
            </div>
        </div>
        
        <div class="ticket-status">
            <span class="status-badge status-{$ticket.status}">
                {$ticket.status_label}
            </span>
            
            {if $ticket.assigned_agent}
                <span class="assigned-agent">
                    <i class="fa fa-user"></i>
                    {$ticket.assigned_agent.name}
                </span>
            {/if}
            
            {if $ticket.tags}
                <div class="ticket-tags">
                    {foreach $ticket.tags as $tag}
                        <span class="tag tag-{$tag.color}">{$tag.name}</span>
                    {/foreach}
                </div>
            {/if}
        </div>
    </div>
    
    <div class="ticket-footer">
        <div class="response-time">
            {if $ticket.response_due}
                <span class="response-due {if $ticket.overdue}overdue{/if}">
                    <i class="fa fa-clock-o"></i>
                    {if $ticket.overdue}
                        {_w('Overdue by %s', $ticket.overdue_time)}
                    {else}
                        {_w('Due in %s', $ticket.response_due)}
                    {/if}
                </span>
            {/if}
        </div>
        
        <div class="quick-actions">
            <button class="btn btn-xs btn-success" onclick="quickReply({$ticket.id})">
                <i class="fa fa-reply"></i> {_w('Reply')}
            </button>
            
            {if !$ticket.assigned_agent}
                <button class="btn btn-xs btn-primary" onclick="takeTicket({$ticket.id})">
                    <i class="fa fa-hand-paper-o"></i> {_w('Take')}
                </button>
            {/if}
        </div>
    </div>
</div>
```

## Mailer Application Themes

The Mailer application handles email marketing campaigns, subscriber management, and email analytics.

### Mailer Theme Structure

```
mailer-theme/
├── theme.xml                    # Theme manifest
├── layout.html                  # Base layout
├── dashboard.html               # Campaign dashboard
├── campaigns/                   # Campaign management
│   ├── list.html               # Campaign listing
│   ├── create.html             # Campaign creator
│   ├── edit.html               # Campaign editor
│   └── stats.html              # Campaign analytics
├── templates/                   # Email templates
│   ├── gallery.html            # Template gallery
│   ├── editor.html             # WYSIWYG editor
│   └── preview.html            # Template preview
├── subscribers/                 # Subscriber management
│   ├── list.html               # Subscriber listing
│   ├── segments.html           # Audience segments
│   └── import.html             # Import subscribers
└── includes/                    # Components
    ├── campaign-card.html      # Campaign card
    ├── template-card.html      # Template card
    ├── stats-widget.html       # Analytics widget
    └── email-editor.html       # Email builder
```

### Mailer Dashboard

**Template: `dashboard.html`**
```smarty
{* Email marketing dashboard *}
<div class="mailer-dashboard">
    <header class="dashboard-header">
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-8">
                    <h1 class="dashboard-title">{_w('Email Marketing Dashboard')}</h1>
                    <p class="dashboard-subtitle">{_w('Manage campaigns and engage your audience')}</p>
                </div>
                <div class="col-md-4">
                    <div class="dashboard-actions">
                        <a href="{$wa_app_url}campaign/create" class="btn btn-primary btn-lg">
                            <i class="fa fa-plus"></i> {_w('Create Campaign')}
                        </a>
                        <div class="dropdown" style="display: inline-block;">
                            <button class="btn btn-default dropdown-toggle" data-toggle="dropdown">
                                <i class="fa fa-cog"></i> {_w('Quick Actions')} <span class="caret"></span>
                            </button>
                            <ul class="dropdown-menu">
                                <li><a href="{$wa_app_url}subscribers/import">{_w('Import Subscribers')}</a></li>
                                <li><a href="{$wa_app_url}templates/create">{_w('Create Template')}</a></li>
                                <li><a href="{$wa_app_url}segments/create">{_w('Create Segment')}</a></li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <div class="dashboard-content">
        <div class="container-fluid">
            {* Email Marketing Metrics *}
            <div class="row dashboard-stats">
                <div class="col-md-3">
                    <div class="stat-card stat-primary">
                        <div class="stat-icon">
                            <i class="fa fa-users"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.total_subscribers|number_format}</h3>
                            <p class="stat-label">{_w('Total Subscribers')}</p>
                            <span class="stat-change positive">
                                +{$stats.new_subscribers_today} {_w('today')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-success">
                        <div class="stat-icon">
                            <i class="fa fa-envelope-open"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.avg_open_rate}%</h3>
                            <p class="stat-label">{_w('Average Open Rate')}</p>
                            <span class="stat-change {if $stats.open_rate_trend > 0}positive{else}negative{/if}">
                                {if $stats.open_rate_trend > 0}+{/if}{$stats.open_rate_trend}% {_w('vs last month')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-warning">
                        <div class="stat-icon">
                            <i class="fa fa-mouse-pointer"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.avg_click_rate}%</h3>
                            <p class="stat-label">{_w('Average Click Rate')}</p>
                            <span class="stat-change {if $stats.click_rate_trend > 0}positive{else}negative{/if}">
                                {if $stats.click_rate_trend > 0}+{/if}{$stats.click_rate_trend}% {_w('vs last month')}
                            </span>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-3">
                    <div class="stat-card stat-info">
                        <div class="stat-icon">
                            <i class="fa fa-paper-plane"></i>
                        </div>
                        <div class="stat-content">
                            <h3 class="stat-number">{$stats.emails_sent_this_month|number_format}</h3>
                            <p class="stat-label">{_w('Emails Sent This Month')}</p>
                            <span class="stat-change neutral">
                                {$stats.deliverability_rate}% {_w('delivered')}
                            </span>
                        </div>
                    </div>
                </div>
            </div>

            <div class="row">
                {* Recent Campaigns *}
                <div class="col-md-8">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Recent Campaigns')}</h3>
                            <div class="widget-actions">
                                <a href="{$wa_app_url}campaigns/" class="btn btn-sm btn-default">
                                    {_w('View All Campaigns')}
                                </a>
                            </div>
                        </div>
                        <div class="widget-content">
                            <div class="campaigns-list">
                                {foreach $recent_campaigns as $campaign}
                                    {include file="$wa_theme_path/includes/campaign-card.html" campaign=$campaign}
                                {/foreach}
                            </div>
                        </div>
                    </div>
                </div>

                {* Performance Chart *}
                <div class="col-md-4">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Performance Trend')}</h3>
                        </div>
                        <div class="widget-content">
                            <canvas id="performanceChart" width="300" height="200"></canvas>
                            
                            <div class="chart-legend">
                                <div class="legend-item">
                                    <span class="legend-color" style="background: #3498db;"></span>
                                    <span class="legend-label">{_w('Open Rate')}</span>
                                </div>
                                <div class="legend-item">
                                    <span class="legend-color" style="background: #2ecc71;"></span>
                                    <span class="legend-label">{_w('Click Rate')}</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="row">
                {* Top Performing Content *}
                <div class="col-md-6">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Top Performing Content')}</h3>
                        </div>
                        <div class="widget-content">
                            <div class="content-performance">
                                {foreach $top_content as $content}
                                    <div class="content-item">
                                        <div class="content-info">
                                            <h4 class="content-title">{$content.subject}</h4>
                                            <p class="content-meta">
                                                {$content.campaign_name} • 
                                                {$content.sent_date|date_format:"%b %d"}
                                            </p>
                                        </div>
                                        <div class="content-stats">
                                            <div class="stat-item">
                                                <span class="stat-value">{$content.open_rate}%</span>
                                                <span class="stat-label">{_w('Opens')}</span>
                                            </div>
                                            <div class="stat-item">
                                                <span class="stat-value">{$content.click_rate}%</span>
                                                <span class="stat-label">{_w('Clicks')}</span>
                                            </div>
                                        </div>
                                    </div>
                                {/foreach}
                            </div>
                        </div>
                    </div>
                </div>

                {* Subscriber Growth *}
                <div class="col-md-6">
                    <div class="dashboard-widget">
                        <div class="widget-header">
                            <h3 class="widget-title">{_w('Subscriber Growth')}</h3>
                        </div>
                        <div class="widget-content">
                            <canvas id="subscriberChart" width="400" height="200"></canvas>
                            
                            <div class="growth-summary">
                                <div class="growth-item">
                                    <span class="growth-number positive">+{$stats.subscribers_gained_this_week}</span>
                                    <span class="growth-label">{_w('Gained This Week')}</span>
                                </div>
                                <div class="growth-item">
                                    <span class="growth-number negative">-{$stats.subscribers_lost_this_week}</span>
                                    <span class="growth-label">{_w('Unsubscribed This Week')}</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

## Hub Application Themes

The Hub application serves as a knowledge base and documentation system with advanced search and content organization.

### Hub Theme Structure

```
hub-theme/
├── theme.xml                    # Theme manifest
├── layout.html                  # Base layout
├── index.html                   # Knowledge base home
├── category.html                # Category listing
├── article.html                 # Article view
├── search.html                  # Advanced search
├── includes/                    # Components
│   ├── article-card.html       # Article preview
│   ├── category-nav.html       # Category navigation
│   ├── search-filters.html     # Search filtering
│   ├── rating-widget.html      # Article rating
│   └── related-articles.html   # Related content
└── assets/                      # Theme assets
    ├── css/
    ├── js/
    └── img/
```

### Hub Knowledge Base Homepage

**Template: `index.html`**
```smarty
{* Knowledge base homepage *}
<div class="hub-homepage">
    <header class="hero-section">
        <div class="container">
            <div class="hero-content">
                <h1 class="hero-title">{_w('How can we help you?')}</h1>
                <p class="hero-subtitle">{_w('Search our knowledge base for answers and solutions')}</p>
                
                <div class="search-container">
                    <form action="{$wa_app_url}search/" method="get" class="search-form">
                        <div class="search-input-group">
                            <input type="text" name="query" placeholder="{_w('Search for articles, guides, and more...')}" 
                                   class="search-input" value="{$search_query|escape}">
                            <button type="submit" class="search-button">
                                <i class="fa fa-search"></i>
                                <span class="sr-only">{_w('Search')}</span>
                            </button>
                        </div>
                        
                        <div class="search-suggestions">
                            <span class="suggestions-label">{_w('Popular searches:')}</span>
                            {foreach $popular_searches as $search}
                                <a href="{$wa_app_url}search/?query={$search|urlencode}" class="suggestion-tag">
                                    {$search}
                                </a>
                            {/foreach}
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </header>

    <div class="hub-content">
        <div class="container">
            {* Quick Access Categories *}
            <section class="categories-section">
                <div class="section-header">
                    <h2 class="section-title">{_w('Browse by Category')}</h2>
                    <p class="section-subtitle">{_w('Find information organized by topic')}</p>
                </div>
                
                <div class="categories-grid">
                    {foreach $categories as $category}
                        <div class="category-card">
                            <a href="{$wa_app_url}category/{$category.id}/" class="category-link">
                                <div class="category-icon">
                                    <i class="{$category.icon}"></i>
                                </div>
                                <h3 class="category-title">{$category.name}</h3>
                                <p class="category-description">{$category.description}</p>
                                <div class="category-stats">
                                    <span class="article-count">{$category.article_count} {_w('articles')}</span>
                                </div>
                            </a>
                        </div>
                    {/foreach}
                </div>
            </section>

            {* Featured Articles *}
            <section class="featured-section">
                <div class="section-header">
                    <h2 class="section-title">{_w('Featured Articles')}</h2>
                    <p class="section-subtitle">{_w('Our most helpful and popular content')}</p>
                </div>
                
                <div class="articles-grid">
                    {foreach $featured_articles as $article}
                        {include file="$wa_theme_path/includes/article-card.html" article=$article featured=true}
                    {/foreach}
                </div>
            </section>

            {* Recent Updates *}
            <section class="recent-section">
                <div class="row">
                    <div class="col-md-8">
                        <div class="section-header">
                            <h2 class="section-title">{_w('Recently Updated')}</h2>
                            <p class="section-subtitle">{_w('Latest additions and improvements')}</p>
                        </div>
                        
                        <div class="recent-articles">
                            {foreach $recent_articles as $article}
                                <div class="recent-article">
                                    <h4 class="article-title">
                                        <a href="{$wa_app_url}article/{$article.id}/">{$article.title}</a>
                                    </h4>
                                    <p class="article-excerpt">{$article.excerpt}</p>
                                    <div class="article-meta">
                                        <span class="article-category">
                                            <a href="{$wa_app_url}category/{$article.category.id}/">
                                                {$article.category.name}
                                            </a>
                                        </span>
                                        <span class="article-date">
                                            {$article.updated_date|date_format:"%B %d, %Y"}
                                        </span>
                                    </div>
                                </div>
                            {/foreach}
                        </div>
                    </div>
                    
                    <div class="col-md-4">
                        <div class="sidebar-widget">
                            <h3 class="widget-title">{_w('Need More Help?')}</h3>
                            <div class="widget-content">
                                <p>{_w('Can\'t find what you\'re looking for? Get in touch with our support team.')}</p>
                                
                                <div class="help-options">
                                    <a href="{$contact_url}" class="help-option">
                                        <i class="fa fa-envelope"></i>
                                        <span>{_w('Contact Support')}</span>
                                    </a>
                                    
                                    {if $live_chat_enabled}
                                        <a href="#" class="help-option" onclick="openLiveChat()">
                                            <i class="fa fa-comments"></i>
                                            <span>{_w('Live Chat')}</span>
                                        </a>
                                    {/if}
                                    
                                    <a href="{$community_url}" class="help-option">
                                        <i class="fa fa-users"></i>
                                        <span>{_w('Community Forum')}</span>
                                    </a>
                                </div>
                            </div>
                        </div>
                        
                        <div class="sidebar-widget">
                            <h3 class="widget-title">{_w('Popular Articles')}</h3>
                            <div class="widget-content">
                                <div class="popular-articles">
                                    {foreach $popular_articles as $article}
                                        <div class="popular-article">
                                            <h5 class="article-title">
                                                <a href="{$wa_app_url}article/{$article.id}/">{$article.title}</a>
                                            </h5>
                                            <div class="article-stats">
                                                <span class="views-count">
                                                    <i class="fa fa-eye"></i>
                                                    {$article.views|number_format} {_w('views')}
                                                </span>
                                                <span class="rating">
                                                    <i class="fa fa-star"></i>
                                                    {$article.rating}/5
                                                </span>
                                            </div>
                                        </div>
                                    {/foreach}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
        </div>
    </div>
</div>
```

### Article Card Component

**Template: `includes/article-card.html`**
```smarty
{* Article preview card for listings *}
<article class="article-card {if $featured}featured{/if}">
    {if $article.featured_image}
        <div class="article-image">
            <a href="{$wa_app_url}article/{$article.id}/">
                <img src="{$article.featured_image}" alt="{$article.title}" class="img-responsive">
            </a>
            
            {if $featured}
                <div class="featured-badge">
                    <i class="fa fa-star"></i>
                    <span>{_w('Featured')}</span>
                </div>
            {/if}
        </div>
    {/if}
    
    <div class="article-content">
        <div class="article-header">
            <div class="article-category">
                <a href="{$wa_app_url}category/{$article.category.id}/" class="category-link">
                    <i class="{$article.category.icon}"></i>
                    {$article.category.name}
                </a>
            </div>
            
            {if $article.difficulty_level}
                <div class="difficulty-level">
                    <span class="difficulty difficulty-{$article.difficulty_level}">
                        {$article.difficulty_label}
                    </span>
                </div>
            {/if}
        </div>
        
        <h3 class="article-title">
            <a href="{$wa_app_url}article/{$article.id}/">{$article.title}</a>
        </h3>
        
        <p class="article-excerpt">{$article.excerpt}</p>
        
        <div class="article-meta">
            <div class="article-stats">
                <span class="reading-time">
                    <i class="fa fa-clock-o"></i>
                    {$article.reading_time} {_w('min read')}
                </span>
                
                <span class="views-count">
                    <i class="fa fa-eye"></i>
                    {$article.views|number_format}
                </span>
                
                {if $article.rating}
                    <span class="article-rating">
                        <i class="fa fa-star"></i>
                        {$article.rating}/5 ({$article.rating_count})
                    </span>
                {/if}
            </div>
            
            <div class="article-date">
                <time datetime="{$article.updated_datetime}">
                    {$article.updated_date|date_format:"%B %d, %Y"}
                </time>
            </div>
        </div>
        
        {if $article.tags}
            <div class="article-tags">
                {foreach $article.tags as $tag}
                    <a href="{$wa_app_url}search/?tags={$tag.id}" class="tag">
                        {$tag.name}
                    </a>
                {/foreach}
            </div>
        {/if}
    </div>
</article>
```

## Cross-Application Theme Integration

### Unified Navigation Component

**Template: `includes/app-navigation.html`**
```smarty
{* Cross-application navigation menu *}
<nav class="app-navigation">
    <div class="nav-header">
        <div class="app-switcher">
            <button class="current-app" data-toggle="dropdown">
                <i class="app-icon {$current_app.icon}"></i>
                <span class="app-name">{$current_app.name}</span>
                <i class="fa fa-chevron-down"></i>
            </button>
            
            <div class="app-dropdown">
                <div class="app-search">
                    <input type="text" placeholder="{_w('Search apps...')}" class="app-search-input">
                </div>
                
                <div class="app-list">
                    {foreach $wa->apps() as $app}
                        <a href="{$app.url}" class="app-item {if $app.id == $current_app.id}active{/if}">
                            <i class="app-icon {$app.icon}"></i>
                            <span class="app-name">{$app.name}</span>
                            {if $app.notifications}
                                <span class="app-badge">{$app.notifications}</span>
                            {/if}
                        </a>
                    {/foreach}
                </div>
            </div>
        </div>
        
        <div class="nav-search">
            <div class="search-container">
                <input type="text" placeholder="{_w('Search...')}" class="global-search-input">
                <button class="search-button">
                    <i class="fa fa-search"></i>
                </button>
            </div>
        </div>
        
        <div class="nav-actions">
            <div class="notifications-menu">
                <button class="notifications-toggle" data-toggle="dropdown">
                    <i class="fa fa-bell"></i>
                    {if $notifications_count > 0}
                        <span class="notification-badge">{$notifications_count}</span>
                    {/if}
                </button>
                
                <div class="notifications-dropdown">
                    <div class="notifications-header">
                        <h4>{_w('Notifications')}</h4>
                        <a href="#" class="mark-all-read">{_w('Mark all as read')}</a>
                    </div>
                    
                    <div class="notifications-list">
                        {foreach $recent_notifications as $notification}
                            <div class="notification-item {if !$notification.read}unread{/if}">
                                <div class="notification-icon">
                                    <i class="{$notification.icon}"></i>
                                </div>
                                <div class="notification-content">
                                    <p class="notification-text">{$notification.text}</p>
                                    <span class="notification-time">
                                        {$notification.created_datetime|wa_datetime:"humandiff"}
                                    </span>
                                </div>
                            </div>
                        {/foreach}
                    </div>
                    
                    <div class="notifications-footer">
                        <a href="{$wa_url}webasyst/notifications/">{_w('View all notifications')}</a>
                    </div>
                </div>
            </div>
            
            <div class="user-menu">
                <button class="user-toggle" data-toggle="dropdown">
                    <img src="{$user.photo}" alt="{$user.name}" class="user-avatar">
                    <span class="user-name">{$user.name}</span>
                    <i class="fa fa-chevron-down"></i>
                </button>
                
                <div class="user-dropdown">
                    <div class="user-info">
                        <img src="{$user.photo}" alt="{$user.name}" class="user-avatar-large">
                        <h4 class="user-display-name">{$user.name}</h4>
                        <p class="user-email">{$user.email}</p>
                    </div>
                    
                    <div class="user-menu-items">
                        <a href="{$wa_url}webasyst/profile/" class="menu-item">
                            <i class="fa fa-user"></i>
                            {_w('Profile')}
                        </a>
                        <a href="{$wa_url}webasyst/settings/" class="menu-item">
                            <i class="fa fa-cog"></i>
                            {_w('Settings')}
                        </a>
                        <div class="menu-divider"></div>
                        <a href="{$wa_url}?action=logout" class="menu-item">
                            <i class="fa fa-sign-out"></i>
                            {_w('Sign out')}
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</nav>
```

## Shared Components and Patterns

### Activity Feed Component

**Template: `includes/activity-feed.html`**
```smarty
{* Universal activity feed for all applications *}
<div class="activity-feed">
    {foreach $activities as $activity}
        <div class="activity-item activity-{$activity.type}">
            <div class="activity-avatar">
                <img src="{$activity.user.avatar}" alt="{$activity.user.name}" class="avatar">
                <div class="activity-icon">
                    <i class="{$activity.icon}"></i>
                </div>
            </div>
            
            <div class="activity-content">
                <div class="activity-header">
                    <span class="activity-user">{$activity.user.name}</span>
                    <span class="activity-action">{$activity.action_text}</span>
                    {if $activity.target}
                        <a href="{$activity.target.url}" class="activity-target">
                            {$activity.target.title}
                        </a>
                    {/if}
                </div>
                
                {if $activity.description}
                    <p class="activity-description">{$activity.description}</p>
                {/if}
                
                {if $activity.attachments}
                    <div class="activity-attachments">
                        {foreach $activity.attachments as $attachment}
                            <a href="{$attachment.url}" class="attachment-link">
                                <i class="fa fa-{$attachment.icon}"></i>
                                {$attachment.name}
                            </a>
                        {/foreach}
                    </div>
                {/if}
                
                <div class="activity-meta">
                    <time class="activity-time" datetime="{$activity.created_datetime}">
                        {$activity.created_datetime|wa_datetime:"humandiff"}
                    </time>
                    
                    {if $activity.app}
                        <span class="activity-app">
                            <i class="{$activity.app.icon}"></i>
                            {$activity.app.name}
                        </span>
                    {/if}
                </div>
            </div>
        </div>
    {/foreach}
</div>
```

This comprehensive documentation covers the essential theme development patterns for CRM, Helpdesk, Mailer, and Hub applications, including their specific UI components, cross-application integration patterns, and shared functionality components that ensure consistent user experience across the Webasyst ecosystem.