---
layout:     post
title:      "Django 2.1 官方文档翻译"
subtitle:   "目录"
date:       2019-04-01
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - django
---

# 1. Django documentation
## 1.1. Getting help
## 1.2. How the documentation is organized
## 1.3. First steps
## 1.4. The model layer
## 1.5. The view layer
## 1.6. The template layer
## 1.7. Forms
## 1.8. The development process
## 1.9. The admin
## 1.10. Security
## 1.11. Internationalization and localization
## 1.12. Performance and optimization
## 1.13. Geographic framework
## 1.14. Common Web application tools
## 1.15. Other core functionalities
## 1.16. The Django open-source project
# 2. Getting started
## 2.1. Django at a glance
## 2.2. Quick install guide
## 2.3. Writing your first Django app, part 1
## 2.4. Writing your first Django app, part 2
## 2.5. Writing your first Django app, part 3
## 2.6. Writing your first Django app, part 4
## 2.7. Writing your first Django app, part 5
## 2.8. Writing your first Django app, part 6
## 2.9. Writing your first Django app, part 7
## 2.10. Advanced tutorial: How to write reusable apps
## 2.11. What to read next
## 2.12. Writing your first patch for Django
# 3. Using Django
## 3.1. How to install Django
### 3.1.1. 安装Python
### 3.1.2. 安装Apache和mod_wsgi
### 3.1.3. 运行数据库
### 3.1.4. 安装Django
#### 3.1.4.1. 使用pip安装官方版
#### 3.1.4.2. 安装发行版
#### 3.1.4.3. 安装开发版
## 3.2. Models and databases
### 3.2.1. Models
#### 3.2.1.1. Quick example
#### 3.2.1.2. Using models
#### 3.2.1.3. Fields
##### 3.2.1.3.1. Field types
##### 3.2.1.3.2. Field options
##### 3.2.1.3.3. Automatic primary key fields
##### 3.2.1.3.4. Verbose field names
##### 3.2.1.3.5. Relationships
###### 3.2.1.3.5.1. Many-to-one relationships
###### 3.2.1.3.5.2. Many-to-many relationships
###### 3.2.1.3.5.3. Extra fields on many-to-many relationships
###### 3.2.1.3.5.4. One-to-one relationships
##### 3.2.1.3.6. Models across files
##### 3.2.1.3.7. Field name restrictions
##### 3.2.1.3.8. Custom field types
#### 3.2.1.4. Meta options
#### 3.2.1.5. Model attributes
#### 3.2.1.6. Model methods
##### 3.2.1.6.1. Overriding predefined model methods
##### 3.2.1.6.2. Executing custom SQL
#### 3.2.1.7. Model inheritance
##### 3.2.1.7.1. Abstract base classes
###### 3.2.1.7.1.1. Meta inheritance
###### 3.2.1.7.1.2. Be careful with related_name and related_query_name
##### 3.2.1.7.2. Multi-table inheritance
###### 3.2.1.7.2.1. Meta and multi-table inheritance
###### 3.2.1.7.2.2. Inheritance and reverse relations
###### 3.2.1.7.2.3. Specifying the parent link field
##### 3.2.1.7.3. Proxy models
###### 3.2.1.7.3.1. QuerySets still return the model that was requested
###### 3.2.1.7.3.2. Base class restrictions
###### 3.2.1.7.3.3. Proxy model managers
###### 3.2.1.7.3.4. Differences between proxy inheritance and unmanaged models
##### 3.2.1.7.4. Multiple inheritance
##### 3.2.1.7.5. Field name 'hiding' is not permitted
#### 3.2.1.8. Organizing models in a package
### 3.2.2. Making queries
#### 3.2.2.1. Creating objects
#### 3.2.2.2. Saving changes to objects
##### 3.2.2.2.1. Saving ForeignKey and ManyToManyField fields
#### 3.2.2.3. Retrieving objects
##### 3.2.2.3.1. Retrieving all objects
##### 3.2.2.3.2. Retrieving specific objects with filters
###### 3.2.2.3.2.1. Chaining filters
###### 3.2.2.3.2.2. Filtered QuerySets are unique
###### 3.2.2.3.2.3. QuerySets are lazy
##### 3.2.2.3.3. Retrieving a single object with get()
##### 3.2.2.3.4. Other QuerySet methods
##### 3.2.2.3.5. Limiting QuerySets
##### 3.2.2.3.6. Field lookups
##### 3.2.2.3.7. Lookups that span relationships
###### 3.2.2.3.7.1. Spanning multi-valued relationships
##### 3.2.2.3.8. Filters can reference fields on the model
##### 3.2.2.3.9. The pk lookup shortcut
##### 3.2.2.3.10. Escaping percent signs and underscores in LIKE statements
##### 3.2.2.3.11. Caching and QuerySets
###### 3.2.2.3.11.1. When QuerySets are not cached
##### 3.2.2.3.12. Complex lookups with Q objects
##### 3.2.2.3.13. Comparing objects
##### 3.2.2.3.14. Deleting objects
##### 3.2.2.3.15. Copying model instances
##### 3.2.2.3.16. Updating multiple objects at once
##### 3.2.2.3.17. Related objects
###### 3.2.2.3.17.1. One-to-many relationships
###### 3.2.2.3.17.2. Forward
###### 3.2.2.3.17.3. Following relationships 'backward'
###### 3.2.2.3.17.4. Using a custom reverse manager
###### 3.2.2.3.17.5. Additional methods to handle related objects
###### 3.2.2.3.17.6. Many-to-many relationships
###### 3.2.2.3.17.7. One-to-one relationships
###### 3.2.2.3.17.8. How are the backward relationships possible?
###### 3.2.2.3.17.9. Queries over related objects
##### 3.2.2.3.18. Falling back to raw SQL
### 3.2.3. Aggregation
#### 3.2.3.1. Cheat sheet
#### 3.2.3.2. Generating aggregates over a QuerySet
#### 3.2.3.3. Generating aggregates for each item in a QuerySet
##### 3.2.3.3.1. Combining multiple aggregations
#### 3.2.3.4. Joins and aggregates
##### 3.2.3.4.1. Following relationships backwards
#### 3.2.3.5. Aggregations and other QuerySet clauses
##### 3.2.3.5.1. filter() and exclude()
###### 3.2.3.5.1.1. Filtering on annotations
###### 3.2.3.5.1.2. Order of annotate() and filter() clauses
##### 3.2.3.5.2. order_by()
##### 3.2.3.5.3. values()
###### 3.2.3.5.3.1. Order of annotate() and values() clauses
###### 3.2.3.5.3.2. Interaction with default ordering or order_by()
##### 3.2.3.5.4. Aggregating annotations
### 3.2.4. Search
#### 3.2.4.1. Use Cases
##### 3.2.4.1.1. Standard textual queries
##### 3.2.4.1.2. A database's more advanced comparison functions
##### 3.2.4.1.3. Document-based search
###### 3.2.4.1.3.1. PostgreSQL support
### 3.2.5. Managers
#### 3.2.5.1. class Manager
##### 3.2.5.1.1. Manager names
##### 3.2.5.1.2. Custom managers
###### 3.2.5.1.2.1. Adding extra manager methods
###### 3.2.5.1.2.2. Modifying a manager's initial QuerySet
###### 3.2.5.1.2.3. Default managers
###### 3.2.5.1.2.4. Base managers
###### 3.2.5.1.2.5. Using managers for related object access
###### 3.2.5.1.2.6. Don't filter away any results in this type of manager subclass
###### 3.2.5.1.2.7. Calling custom QuerySet methods from the manager
###### 3.2.5.1.2.8. Creating a manager with QuerySet methods
###### 3.2.5.1.2.9. from_queryset()
###### 3.2.5.1.2.10. Custom managers and model inheritance
###### 3.2.5.1.2.11. Implementation concerns
### 3.2.6. Performing raw SQL queries
#### 3.2.6.1. Performing raw queries
##### 3.2.6.1.1. Mapping query fields to model fields
##### 3.2.6.1.2. Index lookups
##### 3.2.6.1.3. Deferring model fields
##### 3.2.6.1.4. Adding annotations
##### 3.2.6.1.5. Passing parameters into raw()
#### 3.2.6.2. Executing custom SQL directly
##### 3.2.6.2.1. Connections and cursors
###### 3.2.6.2.1.1. Calling stored procedures
### 3.2.7. Database transactions
#### 3.2.7.1. Managing database transactions
##### 3.2.7.1.1. Django's default transaction behavior
##### 3.2.7.1.2. Tying transactions to HTTP requests
##### 3.2.7.1.3. Controlling transactions explicitly
#### 3.2.7.2. Autocommit
##### 3.2.7.2.1. Why Django uses autocommit
##### 3.2.7.2.2. Deactivating transaction management
#### 3.2.7.3. Performing actions after commit
##### 3.2.7.3.1. Savepoints
##### 3.2.7.3.2. Order of execution
##### 3.2.7.3.3. Exception handling
##### 3.2.7.3.4. Timing of execution
##### 3.2.7.3.5. Use in tests
##### 3.2.7.3.6. Why no rollback hook?
#### 3.2.7.4. Low-level APIs
##### 3.2.7.4.1. Autocommit
##### 3.2.7.4.2. Transactions
##### 3.2.7.4.3. Savepoints
#### 3.2.7.5. Database-specific notes
##### 3.2.7.5.1. Savepoints in SQLite
##### 3.2.7.5.2. Transactions in MySQL
##### 3.2.7.5.3. Handling exceptions within PostgreSQL transactions
###### 3.2.7.5.3.1. Transaction rollback
###### 3.2.7.5.3.2. Savepoint rollback
### 3.2.8. Multiple databases
#### 3.2.8.1. Defining your databases
#### 3.2.8.2. Synchronizing your databases
##### 3.2.8.2.1. Using other management commands
#### 3.2.8.3. Automatic database routing
##### 3.2.8.3.1. Database routers
###### 3.2.8.3.1.1. Hints
##### 3.2.8.3.2. Using routers
##### 3.2.8.3.3. An example
#### 3.2.8.4. Manually selecting a database
##### 3.2.8.4.1. Manually selecting a database for a QuerySet
##### 3.2.8.4.2. Selecting a database for save()
###### 3.2.8.4.2.1. Moving an object from one database to another
##### 3.2.8.4.3. Selecting a database to delete from
##### 3.2.8.4.4. Using managers with multiple databases
###### 3.2.8.4.4.1. Using get_queryset() with multiple databases
#### 3.2.8.5. Exposing multiple databases in Django's admin interface
#### 3.2.8.6. Using raw cursors with multiple databases
#### 3.2.8.7. Limitations of multiple databases
##### 3.2.8.7.1. Cross-database relations
##### 3.2.8.7.2. Behavior of contrib apps
### 3.2.9. Tablespaces
#### 3.2.9.1. Declaring tablespaces for tables
#### 3.2.9.2. Declaring tablespaces for indexes
#### 3.2.9.3. An example
#### 3.2.9.4. Database support
### 3.2.10. Database access optimization
#### 3.2.10.1. Profile first
#### 3.2.10.2. Use standard DB optimization techniques
#### 3.2.10.3. Understand QuerySets
##### 3.2.10.3.1. Understand QuerySet evaluation
##### 3.2.10.3.2. Understand cached attributes
##### 3.2.10.3.3. Use the with template tag
##### 3.2.10.3.4. Use iterator()
##### 3.2.10.3.5. Use explain()
#### 3.2.10.4. Do database work in the database rather than in Python
##### 3.2.10.4.1. Use RawSQL
##### 3.2.10.4.2. Use raw SQL
#### 3.2.10.5. Retrieve individual objects using a unique, indexed column
#### 3.2.10.6. Retrieve everything at once if you know you will need it
##### 3.2.10.6.1. Use QuerySet.select_related() and prefetch_related()
#### 3.2.10.7. Don't retrieve things you don't need
##### 3.2.10.7.1. Use QuerySet.values() and values_list()
##### 3.2.10.7.2. Use QuerySet.defer() and only()
##### 3.2.10.7.3. Use QuerySet.count()
##### 3.2.10.7.4. Use QuerySet.exists()
##### 3.2.10.7.5. Don't overuse count() and exists()
##### 3.2.10.7.6. Use QuerySet.update() and delete()
##### 3.2.10.7.7. Use foreign key values directly
##### 3.2.10.7.8. Don't order results if you don't care
#### 3.2.10.8. Insert in bulk
### 3.2.11. Database instrumentation
#### 3.2.11.1. connection.execute_wrapper()
### 3.2.12. Examples of model relationship API usage
#### 3.2.12.1. Many-to-many relationships
#### 3.2.12.2. Many-to-one relationships
#### 3.2.12.3. One-to-one relationships
## 3.3. Handling HTTP requests
### 3.3.1. URL dispatcher 
#### 3.3.1.1. Overview
#### 3.3.1.2. How Django processes a request
#### 3.3.1.3. Example
#### 3.3.1.4. Path converters
#### 3.3.1.5. Registering custom path converters
#### 3.3.1.6. Using regular expressions
##### 3.3.1.6.1. Using unnamed regular expression groups
##### 3.3.1.6.2. Nested arguments
#### 3.3.1.7. What the URLconf searches against
#### 3.3.1.8. Specifying defaults for view arguments
#### 3.3.1.9. Performance
#### 3.3.1.10. Syntax of the urlpatterns variable
#### 3.3.1.11. Error handling
#### 3.3.1.12. Including other URLconfs
##### 3.3.1.12.1. Captured parameters
#### 3.3.1.13. Passing extra options to view functions
##### 3.3.1.13.1. Passing extra options to include()
#### 3.3.1.14. Reverse resolution of URLs
##### 3.3.1.14.1. Examples
#### 3.3.1.15. Naming URL patterns
#### 3.3.1.16. URL namespaces
##### 3.3.1.16.1. Introduction
##### 3.3.1.16.2. Reversing namespaced URLs
###### 3.3.1.16.2.1. Example
##### 3.3.1.16.3. URL namespaces and included URLconfs
### 3.3.2. Writing views 
#### 3.3.2.1. A simple view
#### 3.3.2.2. Mapping URLs to views
#### 3.3.2.3. Returning errors
##### 3.3.2.3.1. The Http404 exception
#### 3.3.2.4. Customizing error views
##### 3.3.2.4.1. Testing custom error views
### 3.3.3. View decorators 
#### 3.3.3.1. Allowed HTTP methods
#### 3.3.3.2. Conditional view processing
#### 3.3.3.3. GZip compression
#### 3.3.3.4. Vary headers
#### 3.3.3.5. Caching
### 3.3.4. File upload 
#### 3.3.4.1. Basic file uploads
##### 3.3.4.1.1. Handling uploaded files with a model
##### 3.3.4.1.2. Uploading multiple files
#### 3.3.4.2. Upload Handlers
##### 3.3.4.2.1. Where uploaded data is stored
##### 3.3.4.2.2. Changing upload handler behavior
##### 3.3.4.2.3. Modifying upload handlers on the fly
### 3.3.5. Django shortcut functions
#### 3.3.5.1. render()
##### 3.3.5.1.1. Required arguments
##### 3.3.5.1.2. Optional arguments
##### 3.3.5.1.3. Example
#### 3.3.5.2. render_to_response()
#### 3.3.5.3. redirect()
##### 3.3.5.3.1. Examples
#### 3.3.5.4. get_object_or_404()
##### 3.3.5.4.1. Required arguments
##### 3.3.5.4.2. Example
#### 3.3.5.5. get_list_or_404()
##### 3.3.5.5.1. Required arguments
##### 3.3.5.5.2. Example
### 3.3.6. Generic views
### 3.3.7. Middleware
#### 3.3.7.1. Writing your own middleware
##### 3.3.7.1.1. __init__(get_response)
##### 3.3.7.1.2. Marking middleware as unused
#### 3.3.7.2. Activating middleware
#### 3.3.7.3. Middleware order and layering
#### 3.3.7.4. Other middleware hooks
##### 3.3.7.4.1. process_view()
##### 3.3.7.4.2. process_exception()
##### 3.3.7.4.3. process_template_response()
#### 3.3.7.5. Dealing with streaming responses
#### 3.3.7.6. Exception handling
#### 3.3.7.7. Upgrading pre-Django 1.10-style middleware
### 3.3.8. How to use sessions
#### 3.3.8.1. Enabling sessions
#### 3.3.8.2. Configuring the session engine
##### 3.3.8.2.1. Using database-backed sessions
##### 3.3.8.2.2. Using cached sessions
##### 3.3.8.2.3. Using file-based sessions
##### 3.3.8.2.4. Using cookie-based sessions
#### 3.3.8.3. Using sessions in views
##### 3.3.8.3.1. Session serialization
###### 3.3.8.3.1.1. Bundled serializers
###### 3.3.8.3.1.2. Write your own serializer
##### 3.3.8.3.2. Session object guidelines
##### 3.3.8.3.3. Examples
#### 3.3.8.4. Setting test cookies
#### 3.3.8.5. Using sessions out of views
#### 3.3.8.6. When sessions are saved
#### 3.3.8.7. Browser-length sessions vs. persistent sessions
#### 3.3.8.8. Clearing the session store
#### 3.3.8.9. Settings
#### 3.3.8.10. Session security
#### 3.3.8.11. Technical details
##### 3.3.8.11.1. The SessionStore object
#### 3.3.8.12. Extending database-backed session engines
##### 3.3.8.12.1. Example
#### 3.3.8.13. Session IDs in URLs
## 3.4. Working with forms
### 3.4.1. HTML forms
#### 3.4.1.1. GET and POST
### 3.4.2. Django's role in forms
### 3.4.3. Forms in Django
#### 3.4.3.1. The Django Form class
#### 3.4.3.2. Instantiating, processing, and rendering forms
### 3.4.4. Building a form
#### 3.4.4.1. The work that needs to be done
#### 3.4.4.2. Building a form in Django
##### 3.4.4.2.1. The Form class
##### 3.4.4.2.2. The view
##### 3.4.4.2.3. The template
### 3.4.5. More about Django Form classes
#### 3.4.5.1. Bound and unbound form instances
#### 3.4.5.2. More on fields
##### 3.4.5.2.1. Widgets
##### 3.4.5.2.2. Field data
### 3.4.6. Working with form templates
#### 3.4.6.1. Form rendering options
#### 3.4.6.2. Rendering fields manually
##### 3.4.6.2.1. Rendering form error messages
#### 3.4.6.3. Looping over the form's fields
##### 3.4.6.3.1. Looping over hidden and visible fields
#### 3.4.6.4. Reusable form templates
### 3.4.7. Further topics
#### 3.4.7.1. Formsets
##### 3.4.7.1.1. Using initial data with a formset
##### 3.4.7.1.2. Limiting the maximum number of forms
##### 3.4.7.1.3. Formset validation
##### 3.4.7.1.4. Understanding the ManagementForm
##### 3.4.7.1.5. Custom formset validation
##### 3.4.7.1.6. Validating the number of forms in a formset
##### 3.4.7.1.7. Dealing with ordering and deletion of forms
##### 3.4.7.1.8. Adding additional fields to a formset
##### 3.4.7.1.9. Passing custom parameters to formset forms
##### 3.4.7.1.10. Customizing a formset's prefix
##### 3.4.7.1.11. Using a formset in views and templates
##### 3.4.7.1.12. Manually rendered can_delete and can_order
##### 3.4.7.1.13. Using more than one formset in a view
#### 3.4.7.2. Creating forms from models
##### 3.4.7.2.1. ModelForm
###### 3.4.7.2.1.1. Field types
###### 3.4.7.2.1.2. A full example
###### 3.4.7.2.1.3. Validation on a ModelForm
###### 3.4.7.2.1.4. Overriding the clean() method
###### 3.4.7.2.1.5. Interaction with model validation
###### 3.4.7.2.1.6. Considerations regarding model's error_messages
###### 3.4.7.2.1.7. The save() method
###### 3.4.7.2.1.8. Selecting the fields to use
###### 3.4.7.2.1.9. Overriding the default fields
###### 3.4.7.2.1.10. Enabling localization of fields
###### 3.4.7.2.1.11. Form inheritance
###### 3.4.7.2.1.12. Providing initial values
###### 3.4.7.2.1.13. ModelForm factory function
##### 3.4.7.2.2. Model formsets
###### 3.4.7.2.2.1. Changing the queryset
###### 3.4.7.2.2.2. Changing the form
###### 3.4.7.2.2.3. Specifying widgets to use in the form with widgets
###### 3.4.7.2.2.4. Enabling localization for fields with localized_fields
###### 3.4.7.2.2.5. Providing initial values
###### 3.4.7.2.2.6. Saving objects in the formset
###### 3.4.7.2.2.7. Limiting the number of editable objects
###### 3.4.7.2.2.8. Using a model formset in a view
###### 3.4.7.2.2.9. Overriding clean() on a ModelFormSet
###### 3.4.7.2.2.10. Using a custom queryset
###### 3.4.7.2.2.11. Using the formset in the template
##### 3.4.7.2.3. Inline formsets
###### 3.4.7.2.3.1. Overriding methods on an InlineFormSet
###### 3.4.7.2.3.2. More than one foreign key to the same model
###### 3.4.7.2.3.3. Using an inline formset in a view
###### 3.4.7.2.3.4. Specifying widgets to use in the inline form
#### 3.4.7.3. Form Assets (the Media class)
##### 3.4.7.3.1. Assets as a static definition
###### 3.4.7.3.1.1. css
###### 3.4.7.3.1.2. js
###### 3.4.7.3.1.3. extend
##### 3.4.7.3.2. Media as a dynamic property
##### 3.4.7.3.3. Paths in asset definitions
##### 3.4.7.3.4. Media objects
###### 3.4.7.3.4.1. Subsets of assets
###### 3.4.7.3.4.2. Combining Media objects
###### 3.4.7.3.4.3. Order of assets
##### 3.4.7.3.5. Media on Forms
## 3.5. Templates
### 3.5.1. Support for template engines
#### 3.5.1.1. Configuration
#### 3.5.1.2. Usage
#### 3.5.1.3. Built-in backends
#### 3.5.1.4. Custom backends
#### 3.5.1.5. Debug integration for custom engines
##### 3.5.1.5.1. Template postmortem
##### 3.5.1.5.2. Contextual line information
##### 3.5.1.5.3. Origin API and 3rd-party integration
### 3.5.2. The Django template language
#### 3.5.2.1. Syntax
##### 3.5.2.1.1. Variables
##### 3.5.2.1.2. Tags
##### 3.5.2.1.3. Filters
##### 3.5.2.1.4. Comments
#### 3.5.2.2. Components
##### 3.5.2.2.1. Engine
##### 3.5.2.2.2. Template
##### 3.5.2.2.3. Context
##### 3.5.2.2.4. Loaders
##### 3.5.2.2.5. Context processors
## 3.6. Class-based views
### 3.6.1. Introduction to class-based views
#### 3.6.1.1. The relationship and history of generic views, class-based views, and class-based generic views
#### 3.6.1.2. Using class-based views
#### 3.6.1.3. Using mixins
#### 3.6.1.4. Handling forms with class-based views
#### 3.6.1.5. Decorating class-based views
##### 3.6.1.5.1. Decorating in URLconf
##### 3.6.1.5.2. Decorating the class
### 3.6.2. Built-in class-based generic views
#### 3.6.2.1. Extending generic views
#### 3.6.2.2. Generic views of objects
##### 3.6.2.2.1. Making 'friendly' template contexts
##### 3.6.2.2.2. Adding extra context
##### 3.6.2.2.3. Viewing subsets of objects
##### 3.6.2.2.4. Dynamic filtering
##### 3.6.2.2.5. Performing extra work
### 3.6.3. Form handling with class-based views
#### 3.6.3.1. Basic forms
#### 3.6.3.2. Model forms
#### 3.6.3.3. Models and request.user
#### 3.6.3.4. AJAX example
### 3.6.4. Using mixins with class-based views
#### 3.6.4.1. Context and template responses
#### 3.6.4.2. Building up Django's generic class-based views
##### 3.6.4.2.1. DetailView: working with a single Django object
##### 3.6.4.2.2. ListView: working with many Django objects
#### 3.6.4.3. Using Django's class-based view mixins
##### 3.6.4.3.1. Using SingleObjectMixin with View
##### 3.6.4.3.2. Using SingleObjectMixin with ListView
#### 3.6.4.4. Avoid anything more complex
##### 3.6.4.4.1. Using FormMixin with DetailView
##### 3.6.4.4.2. A better solution
##### 3.6.4.4.3. An alternative better solution
#### 3.6.4.5. More than just HTML
### 3.6.5. Basic examples
### 3.6.6. Simple usage in your URLconf
### 3.6.7. Subclassing generic views
#### 3.6.7.1. Supporting other HTTP methods
## 3.7. Migrations
### 3.7.1. The Commands
### 3.7.2. Backend Support
#### 3.7.2.1. PostgreSQL
#### 3.7.2.2. MySQL
#### 3.7.2.3. SQLite
### 3.7.3. Workflow
#### 3.7.3.1. Version control
### 3.7.4. Dependencies
### 3.7.5. Migration files
#### 3.7.5.1. Custom fields
#### 3.7.5.2. Model managers
#### 3.7.5.3. Initial migrations
#### 3.7.5.4. History consistency
### 3.7.6. Adding migrations to apps
### 3.7.7. Historical models
### 3.7.8. Considerations when removing model fields
### 3.7.9. Data Migrations
#### 3.7.9.1. Accessing models from other apps
#### 3.7.9.2. More advanced migrations
### 3.7.10. Squashing migrations
### 3.7.11. Serializing values
#### 3.7.11.1. Adding a deconstruct() method
### 3.7.12. Supporting multiple Django versions
## 3.8. Managing files
### 3.8.1. Using files in models
### 3.8.2. The File object
### 3.8.3. File storage
#### 3.8.3.1. Storage objects
#### 3.8.3.2. The built-in filesystem storage class
## 3.9. Testing in Django
### 3.9.1. Writing and running tests
#### 3.9.1.1. Writing tests
#### 3.9.1.2. Running tests
#### 3.9.1.3. The test database
#### 3.9.1.4. Order in which tests are executed
#### 3.9.1.5. Rollback emulation
#### 3.9.1.6. Other test conditions
#### 3.9.1.7. Understanding the test output
#### 3.9.1.8. Speeding up the tests
##### 3.9.1.8.1. Running tests in parallel
##### 3.9.1.8.2. Password hashing
##### 3.9.1.8.3. Preserving the test database
### 3.9.2. Testing tools
#### 3.9.2.1. The test client
##### 3.9.2.1.1. Overview and a quick example
##### 3.9.2.1.2. Making requests
##### 3.9.2.1.3. Testing responses
##### 3.9.2.1.4. Exceptions
##### 3.9.2.1.5. Persistent state
##### 3.9.2.1.6. Setting the language
##### 3.9.2.1.7. Example
#### 3.9.2.2. Provided test case classes
##### 3.9.2.2.1. SimpleTestCase
##### 3.9.2.2.2. TransactionTestCase
##### 3.9.2.2.3. TestCase
##### 3.9.2.2.4. LiveServerTestCase
#### 3.9.2.3. Test cases features
##### 3.9.2.3.1. Default test client
##### 3.9.2.3.2. Customizing the test client
##### 3.9.2.3.3. Fixture loading
##### 3.9.2.3.4. URLconf configuration
##### 3.9.2.3.5. Multi-database support
##### 3.9.2.3.6. Overriding settings
##### 3.9.2.3.7. Emptying the test outbox
##### 3.9.2.3.8. Assertions
##### 3.9.2.3.9. Tagging tests
#### 3.9.2.4. Email services
#### 3.9.2.5. Management Commands
#### 3.9.2.6. Skipping tests
### 3.9.3. Advanced testing topics
#### 3.9.3.1. The request factory
##### 3.9.3.1.1. Example
#### 3.9.3.2. Tests and multiple host names
#### 3.9.3.3. Tests and multiple databases
##### 3.9.3.3.1. Testing primary/replica configurations
##### 3.9.3.3.2. Controlling creation order for test databases
#### 3.9.3.4. Advanced features of TransactionTestCase
#### 3.9.3.5. Using the Django test runner to test reusable applications
#### 3.9.3.6. Using different testing frameworks
##### 3.9.3.6.1. Defining a test runner
###### 3.9.3.6.1.1. Attributes
###### 3.9.3.6.1.2. Methods
##### 3.9.3.6.2. Testing utilities
###### 3.9.3.6.2.1. django.test.utils
###### 3.9.3.6.2.2. django.db.connection.creation
##### 3.9.3.6.3. Integration with coverage.py
## 3.10. User authentication in Django
### 3.10.1. Using the Django authentication system
#### 3.10.1.1. User objects
##### 3.10.1.1.1. Creating users
##### 3.10.1.1.2. Creating superusers
##### 3.10.1.1.3. Changing passwords
##### 3.10.1.1.4. Authenticating users
#### 3.10.1.2. Permissions and Authorization
##### 3.10.1.2.1. Default permissions
##### 3.10.1.2.2. Groups
##### 3.10.1.2.3. Programmatically creating permissions
##### 3.10.1.2.4. Permission caching
#### 3.10.1.3. Authentication in Web requests
##### 3.10.1.3.1. How to log a user in
###### 3.10.1.3.1.1. Selecting the authentication backend
##### 3.10.1.3.2. How to log a user out
##### 3.10.1.3.3. Limiting access to logged-in users
###### 3.10.1.3.3.1. The raw way
###### 3.10.1.3.3.2. The login_required decorator
###### 3.10.1.3.3.3. The LoginRequired mixin
###### 3.10.1.3.3.4. Limiting access to logged-in users that pass a test
###### 3.10.1.3.3.5. The permission_required decorator
###### 3.10.1.3.3.6. The PermissionRequiredMixin mixin
##### 3.10.1.3.4. Redirecting unauthorized requests in class-based views
###### 3.10.1.3.4.1. Session invalidation on password change
##### 3.10.1.3.5. Authentication Views
###### 3.10.1.3.5.1. Using the views
###### 3.10.1.3.5.2. All authentication views
##### 3.10.1.3.6. Helper functions
##### 3.10.1.3.7. Built-in forms
##### 3.10.1.3.8. Authentication data in templates
###### 3.10.1.3.8.1. Users
###### 3.10.1.3.8.2. Permissions
#### 3.10.1.4. Managing users in the admin
##### 3.10.1.4.1. Creating users
##### 3.10.1.4.2. Changing passwords
### 3.10.2. Password management in Django
#### 3.10.2.1. How Django stores passwords
##### 3.10.2.1.1. Using Argon2 with Django
##### 3.10.2.1.2. Using bcrypt with Django
##### 3.10.2.1.3. Increasing the work factor
###### 3.10.2.1.3.1. PBKDF2 and bcrypt
###### 3.10.2.1.3.2. Argon2
##### 3.10.2.1.4. Password upgrading
##### 3.10.2.1.5. Password upgrading without requiring a login
##### 3.10.2.1.6. Included hashers
##### 3.10.2.1.7. Writing your own hasher
#### 3.10.2.2. Manually managing a user's password
#### 3.10.2.3. Password validation
##### 3.10.2.3.1. Enabling password validation
##### 3.10.2.3.2. Included validators
##### 3.10.2.3.3. Integrating validation
##### 3.10.2.3.4. Writing your own validator
### 3.10.3. Customizing authentication in Django
#### 3.10.3.1. Other authentication sources
##### 3.10.3.1.1. Specifying authentication backends
##### 3.10.3.1.2. Writing an authentication backend
##### 3.10.3.1.3. Handling authorization in custom backends
###### 3.10.3.1.3.1. Authorization for anonymous users
###### 3.10.3.1.3.2. Authorization for inactive users
###### 3.10.3.1.3.3. Handling object permissions
#### 3.10.3.2. Custom permissions
#### 3.10.3.3. Extending the existing User model
#### 3.10.3.4. Substituting a custom User model
##### 3.10.3.4.1. Using a custom user model when starting a project
##### 3.10.3.4.2. Changing to a custom user model mid-project
##### 3.10.3.4.3. Reusable apps and AUTH_USER_MODEL
##### 3.10.3.4.4. Referencing the User model
##### 3.10.3.4.5. Specifying a custom user model
##### 3.10.3.4.6. Writing a manager for a custom user model
##### 3.10.3.4.7. Extending Django's default User
##### 3.10.3.4.8. Custom users and the built-in auth forms
##### 3.10.3.4.9. Custom users and django.contrib.admin
##### 3.10.3.4.10. Custom users and permissions
##### 3.10.3.4.11. Custom users and proxy models
##### 3.10.3.4.12. A full example
### 3.10.4. Overview
### 3.10.5. Installation
### 3.10.6. Usage
## 3.11. Django's cache framework
### 3.11.1. Setting up the cache
#### 3.11.1.1. Memcached
#### 3.11.1.2. Database caching
##### 3.11.1.2.1. Creating the cache table
##### 3.11.1.2.2. Multiple databases
#### 3.11.1.3. Filesystem caching
#### 3.11.1.4. Local-memory caching
#### 3.11.1.5. Dummy caching (for development)
#### 3.11.1.6. Using a custom cache backend
#### 3.11.1.7. Cache arguments
### 3.11.2. The per-site cache
### 3.11.3. The per-view cache
#### 3.11.3.1. Specifying per-view cache in the URLconf
### 3.11.4. Template fragment caching
### 3.11.5. The low-level cache API
#### 3.11.5.1. Accessing the cache
#### 3.11.5.2. Basic usage
#### 3.11.5.3. Cache key prefixing
#### 3.11.5.4. Cache versioning
#### 3.11.5.5. Cache key transformation
#### 3.11.5.6. Cache key warnings
### 3.11.6. Downstream caches
### 3.11.7. Using Vary headers
### 3.11.8. Controlling cache: Using other headers
### 3.11.9. Order of MIDDLEWARE
## 3.12. Conditional View Processing
### 3.12.1. The condition decorator
### 3.12.2. Shortcuts for only computing one value
#### 3.12.2.1. Use condition when testing both conditions
### 3.12.3. Using the decorators with other HTTP methods
### 3.12.4. Comparison with middleware conditional processing
## 3.13. Cryptographic signing
### 3.13.1. Protecting the SECRET_KEY
### 3.13.2. Using the low-level API
#### 3.13.2.1. Using the salt argument
#### 3.13.2.2. Verifying timestamped values
#### 3.13.2.3. Protecting complex data structures
## 3.14. Sending email
### 3.14.1. Quick example
### 3.14.2. send_mail()
### 3.14.3. send_mass_mail()
### 3.14.4. mail_admins()
### 3.14.5. mail_managers()
### 3.14.6. Examples
### 3.14.7. Preventing header injection
### 3.14.8. The EmailMessage class
#### 3.14.8.1. EmailMessage Objects
#### 3.14.8.2. Sending alternative content types
### 3.14.9. Email backends
#### 3.14.9.1. Obtaining an instance of an email backend
##### 3.14.9.1.1. SMTP backend
##### 3.14.9.1.2. Console backend
##### 3.14.9.1.3. File backend
##### 3.14.9.1.4. In-memory backend
##### 3.14.9.1.5. Dummy backend
#### 3.14.9.2. Defining a custom email backend
#### 3.14.9.3. Sending multiple emails
### 3.14.10. Configuring email for development
## 3.15. Internationalization and localization
### 3.15.1. Translation
#### 3.15.1.1. Overview
#### 3.15.1.2. Internationalization: in Python code
##### 3.15.1.2.1. Standard translation
##### 3.15.1.2.2. Comments for translators
##### 3.15.1.2.3. Marking strings as no-op
##### 3.15.1.2.4. Pluralization
##### 3.15.1.2.5. Contextual markers
##### 3.15.1.2.6. Lazy translation
###### 3.15.1.2.6.1. Model fields and relationships verbose_name and help_text option values
###### 3.15.1.2.6.2. Model verbose names values
###### 3.15.1.2.6.3. Model methods short_description attribute values
##### 3.15.1.2.7. Working with lazy translation objects
###### 3.15.1.2.7.1. Lazy translations and plural
###### 3.15.1.2.7.2. Formatting strings: format_lazy()
###### 3.15.1.2.7.3. Other uses of lazy in delayed translations
##### 3.15.1.2.8. Localized names of languages
#### 3.15.1.3. Internationalization: in template code
##### 3.15.1.3.1. trans template tag
##### 3.15.1.3.2. blocktrans template tag
##### 3.15.1.3.3. String literals passed to tags and filters
##### 3.15.1.3.4. Comments for translators in templates
##### 3.15.1.3.5. Switching language in templates
##### 3.15.1.3.6. Other tags
###### 3.15.1.3.6.1. get_available_languages
###### 3.15.1.3.6.2. get_current_language
###### 3.15.1.3.6.3. get_current_language_bidi
###### 3.15.1.3.6.4. i18n context processor
###### 3.15.1.3.6.5. get_language_info
###### 3.15.1.3.6.6. get_language_info_list
###### 3.15.1.3.6.7. Template filters
#### 3.15.1.4. Internationalization: in JavaScript code
##### 3.15.1.4.1. The JavaScriptCatalog view
##### 3.15.1.4.2. Using the JavaScript translation catalog
###### 3.15.1.4.2.1. gettext
###### 3.15.1.4.2.2. ngettext
###### 3.15.1.4.2.3. interpolate
###### 3.15.1.4.2.4. get_format
###### 3.15.1.4.2.5. gettext_noop
###### 3.15.1.4.2.6. pgettext
###### 3.15.1.4.2.7. npgettext
###### 3.15.1.4.2.8. pluralidx
##### 3.15.1.4.3. The JSONCatalog view
##### 3.15.1.4.4. Note on performance
#### 3.15.1.5. Internationalization: in URL patterns
##### 3.15.1.5.1. Language prefix in URL patterns
##### 3.15.1.5.2. Translating URL patterns
##### 3.15.1.5.3. Reversing in templates
#### 3.15.1.6. Localization: how to create language files
##### 3.15.1.6.1. Message files
##### 3.15.1.6.2. Compiling message files
##### 3.15.1.6.3. Troubleshooting: gettext() incorrectly detects python-format in strings with percent signs
##### 3.15.1.6.4. Creating message files from JavaScript source code
##### 3.15.1.6.5. gettext on Windows
##### 3.15.1.6.6. Customizing the makemessages command
#### 3.15.1.7. Miscellaneous
##### 3.15.1.7.1. The set_language redirect view
##### 3.15.1.7.2. Explicitly setting the active language
##### 3.15.1.7.3. Using translations outside views and templates
##### 3.15.1.7.4. Language cookie
#### 3.15.1.8. Implementation notes
##### 3.15.1.8.1. Specialties of Django translation
##### 3.15.1.8.2. How Django discovers language preference
##### 3.15.1.8.3. How Django discovers translations
##### 3.15.1.8.4. Using a non-English base language
### 3.15.2. Format localization
#### 3.15.2.1. Overview
#### 3.15.2.2. Locale aware input in forms
#### 3.15.2.3. Controlling localization in templates
##### 3.15.2.3.1. Template tags
###### 3.15.2.3.1.1. localize
##### 3.15.2.3.2. Template filters
###### 3.15.2.3.2.1. localize
###### 3.15.2.3.2.2. unlocalize
##### 3.15.2.3.3. Creating custom format files
##### 3.15.2.3.4. Limitations of the provided locale formats
###### 3.15.2.3.4.1. Switzerland (German)
### 3.15.3. Time zones
#### 3.15.3.1. Overview
#### 3.15.3.2. Concepts
##### 3.15.3.2.1. Naive and aware datetime objects
##### 3.15.3.2.2. Interpretation of naive datetime objects
##### 3.15.3.2.3. Default time zone and current time zone
##### 3.15.3.2.4. Selecting the current time zone
#### 3.15.3.3. Time zone aware input in forms
#### 3.15.3.4. Time zone aware output in templates
##### 3.15.3.4.1. Template tags
###### 3.15.3.4.1.1. localtime
###### 3.15.3.4.1.2. timezone
###### 3.15.3.4.1.3. get_current_timezone
##### 3.15.3.4.2. Template filters
###### 3.15.3.4.2.1. localtime
###### 3.15.3.4.2.2. utc
###### 3.15.3.4.2.3. timezone
#### 3.15.3.5. Migration guide
##### 3.15.3.5.1. Database
###### 3.15.3.5.1.1. PostgreSQL
###### 3.15.3.5.1.2. Other databases
##### 3.15.3.5.2. Code
##### 3.15.3.5.3. Fixtures
#### 3.15.3.6. FAQ
##### 3.15.3.6.1. Setup
##### 3.15.3.6.2. Troubleshooting
##### 3.15.3.6.3. Usage
### 3.15.4. Overview
### 3.15.5. Definitions
## 3.16. Logging
### 3.16.1. A quick logging primer
#### 3.16.1.1. The cast of players
##### 3.16.1.1.1. Loggers
##### 3.16.1.1.2. Handlers
##### 3.16.1.1.3. Filters
##### 3.16.1.1.4. Formatters
### 3.16.2. Using logging
#### 3.16.2.1. Naming loggers
#### 3.16.2.2. Making logging calls
### 3.16.3. Configuring logging
#### 3.16.3.1. Examples
#### 3.16.3.2. Custom logging configuration
#### 3.16.3.3. Disabling logging configuration
### 3.16.4. Django's logging extensions
#### 3.16.4.1. Loggers
##### 3.16.4.1.1. django
##### 3.16.4.1.2. django.request
##### 3.16.4.1.3. django.server
##### 3.16.4.1.4. django.template
##### 3.16.4.1.5. django.db.backends
##### 3.16.4.1.6. django.security.*
##### 3.16.4.1.7. django.db.backends.schema
#### 3.16.4.2. Handlers
#### 3.16.4.3. Filters
### 3.16.5. Django's default logging configuration
## 3.17. Pagination
### 3.17.1. Example
### 3.17.2. Using Paginator in a view
### 3.17.3. Paginator objects
#### 3.17.3.1. Required arguments
#### 3.17.3.2. Optional arguments
#### 3.17.3.3. Methods
#### 3.17.3.4. Attributes
### 3.17.4. InvalidPage exceptions
### 3.17.5. Page objects
#### 3.17.5.1. Methods
#### 3.17.5.2. Attributes
## 3.18. Security in Django
### 3.18.1. Cross site scripting (XSS) protection
### 3.18.2. Cross site request forgery (CSRF) protection
### 3.18.3. SQL injection protection
### 3.18.4. Clickjacking protection
### 3.18.5. SSL/HTTPS
### 3.18.6. Host header validation
### 3.18.7. Session security
### 3.18.8. User-uploaded content
### 3.18.9. Additional security topics
## 3.19. Performance and optimization
### 3.19.1. Introduction
### 3.19.2. General approaches
#### 3.19.2.1. What are you optimizing for?
#### 3.19.2.2. Performance benchmarking
##### 3.19.2.2.1. Django tools
##### 3.19.2.2.2. Third-party services
#### 3.19.2.3. Get things right from the start
##### 3.19.2.3.1. Work at the appropriate level
### 3.19.3. Caching
#### 3.19.3.1. The caching framework
#### 3.19.3.2. cached_property
### 3.19.4. Understanding laziness
#### 3.19.4.1. Laziness in Django
### 3.19.5. Databases
#### 3.19.5.1. Database optimization
#### 3.19.5.2. Other database-related tips
### 3.19.6. HTTP performance
#### 3.19.6.1. Middleware
##### 3.19.6.1.1. ConditionalGetMiddleware
##### 3.19.6.1.2. GZipMiddleware
#### 3.19.6.2. Sessions
##### 3.19.6.2.1. Using cached sessions
#### 3.19.6.3. Static files
##### 3.19.6.3.1. CachedStaticFilesStorage
##### 3.19.6.3.2. 'Minification'
### 3.19.7. Template performance
#### 3.19.7.1. The cached template loader
### 3.19.8. Using different versions of available software
#### 3.19.8.1. Newer is often - but not always - better
#### 3.19.8.2. Alternatives to Django's template language
#### 3.19.8.3. Alternative software implementations
##### 3.19.8.3.1. PyPy
##### 3.19.8.3.2. C implementations of Python libraries
## 3.20. Serializing Django objects
### 3.20.1. Serializing data
#### 3.20.1.1. Subset of fields
#### 3.20.1.2. Inherited models
### 3.20.2. Deserializing data
### 3.20.3. Serialization formats
#### 3.20.3.1. XML
#### 3.20.3.2. JSON
##### 3.20.3.2.1. DjangoJSONEncoder
#### 3.20.3.3. YAML
### 3.20.4. Natural keys
#### 3.20.4.1. Deserialization of natural keys
#### 3.20.4.2. Serialization of natural keys
#### 3.20.4.3. Dependencies during serialization
## 3.21. Django settings
### 3.21.1. The basics
### 3.21.2. Designating the settings
#### 3.21.2.1. The django-admin utility
#### 3.21.2.2. On the server (mod_wsgi)
### 3.21.3. Default settings
#### 3.21.3.1. Seeing which settings you've changed
### 3.21.4. Using settings in Python code
### 3.21.5. Altering settings at runtime
### 3.21.6. Security
### 3.21.7. Available settings
### 3.21.8. Creating your own settings
### 3.21.9. Using settings without setting DJANGO_SETTINGS_MODULE
#### 3.21.9.1. Custom default settings
#### 3.21.9.2. Either configure() or DJANGO_SETTINGS_MODULE is required
#### 3.21.9.3. Calling django.setup() is required for 'standalone' Django usage
## 3.22. Signals
### 3.22.1. Listening to signals
#### 3.22.1.1. Receiver functions
#### 3.22.1.2. Connecting receiver functions
#### 3.22.1.3. Connecting to signals sent by specific senders
#### 3.22.1.4. Preventing duplicate signals
#### 3.22.1.5. Preventing duplicate signals
### 3.22.2. Defining and sending signals
#### 3.22.2.1. Defining signals
#### 3.22.2.2. Sending signals
### 3.22.3. Disconnecting signals
## 3.23. System check framework
### 3.23.1. Writing your own checks
#### 3.23.1.1. Messages
#### 3.23.1.2. Registering and labeling checks
#### 3.23.1.3. Field, model, manager, and database checks
#### 3.23.1.4. Writing tests
## 3.24. External packages
### 3.24.1. Localflavor
### 3.24.2. Comments
### 3.24.3. Formtools
# 4. 'How-to' guides
## 4.1. Authentication using REMOTE_USER
## 4.2. Writing custom django-admin commands
## 4.3. Writing custom model fields
## 4.4. Custom Lookups
## 4.5. Custom template tags and filters
## 4.6. Writing a custom storage system
## 4.7. Deploying Django
## 4.8. Upgrading Django to a newer version
## 4.9. Error reporting
## 4.10. Providing initial data for models
## 4.11. Django on Jython
## 4.12. Integrating Django with a legacy database
## 4.13. Outputting CSV with Django
## 4.14. Outputting PDFs with Django
## 4.15. Overriding templates
## 4.16. Managing static files (e.g. images, JavaScript, CSS)
## 4.17. Deploying static files
## 4.18. How to install Django on Windows
## 4.19. Writing database migrations
# 5. Django FAQ
## 5.1. FAQ: General
## 5.2. FAQ: Installation
## 5.3. FAQ: Using Django
## 5.4. FAQ: Getting Help
## 5.5. FAQ: Databases and models
## 5.6. FAQ: The admin
## 5.7. FAQ: Contributing code
## 5.8. Troubleshooting
# 6. API Reference
## 6.1. Applications
## 6.2. System check framework
## 6.3. Built-in class-based views API
## 6.4. Clickjacking Protection
## 6.5. contrib packages
## 6.6. Cross Site Request Forgery protection
## 6.7. Databases
## 6.8. django-admin and manage.py
## 6.9. Running management commands from your code
## 6.10. Django Exceptions
## 6.11. File handling
## 6.12. Forms
## 6.13. Middleware
## 6.14. Migration Operations
## 6.15. Models
## 6.16. Request and response objects
## 6.17. SchemaEditor
## 6.18. Settings
## 6.19. Signals
## 6.20. Templates
## 6.21. TemplateResponse and SimpleTemplateResponse
## 6.22. Unicode data
## 6.23. django.urls utility functions
## 6.24. django.urls functions for use in URLconfs
## 6.25. django.conf.urls functions for use in URLconfs
## 6.26. Django Utils
## 6.27. Validators
## 6.28. Built-in Views
# 7. Meta-documentation and miscellany
## 7.1. API stability
## 7.2. Design philosophies
## 7.3. Third-party distributions of Django
# 8. Glossary
# 9. Release notes
## 9.1. Final releases
## 9.2. Security releases
# 10. Django internals
## 10.1. Contributing to Django
## 10.2. Mailing lists
## 10.3. Organization of the Django Project
## 10.4. Django's security policies
## 10.5. Django's release process
## 10.6. Django Deprecation Timeline
## 10.7. The Django source code repository
## 10.8. How is Django Formed?
# 11. Indices, glossary and tables
# 12. Python Module Index

