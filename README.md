# CustomerScope

Provide a scope mechanism to associate customers to entities.

## Installation

### Manually

* Copy the module into the ```<thelia_root>/local/modules/``` directory and make sure that the name of the module is CustomerScope.
* Activate it in your Thelia administration panel

### Composer

Add it in your main Thelia composer.json file

```
composer require thelia/customer-scope-module:~0.1
```

## Concepts

A *scope* is a kind of association that a customer can have. It is defined by a code, and the class of entity that will
be associated. Any model class generated by Propel can be used to create a scope.

Scopes must be associated to a *scope group*.

Customer can be associated to a specific entity instance of a class that has a scope, creating a *customer scope*.
A customer can be associated with any number of entities in any number of scopes.
What this association means is up to your business logic.

For exemple, let's say you manage physical stores what to associate customers to them. You can use scopes for that.

Assuming that you have a `Store` class representing your stores, you can create:

- A `store` scope using the `Store` class
- A `places` scope group to contain the `store` scope

You can then associate a customer to any instance of the `Store` class, and then easily check to what stores
your customer is associated to in your application.

## Usage

For the time being, scope and scope groups have to be inserted directly into the database.
You could for example do this in a module specific to your site, on activation.

## Handler

The customer scope handler service allows you to add customers to scopes, and to check the scopes a customer belongs to.

It is registered in the container under the id `customerscope.customerscope.handler`.
See the `CustomerScopeHandler` class for the available methods.

## Loops

### customer-scope

A loop is also available to access the customer scopes.

#### Input arguments

|Argument        |Description                  |
|----------------|-----------------------------|
|**customer_id** | Id or list of customer ids. |
|**scope_id**    | Id or list of scope ids.    |
|**entity_id**   | Id or list of entity ids.   |
|**entity**      | Entity class.               |

#### Output arguments

|Variable                     |Description    |
|-----------------------------|---------------|
|$CUSTOMER_SCOPE_CUSTOMER_ID  | Customer id.  |
|$CUSTOMER_SCOPE_SCOPE_ID     | Scope id.     |
|$CUSTOMER_SCOPE_ENTITY_ID    | Entity id.    |
|$CUSTOMER_SCOPE_SCOPE_ENTITY | Entity class. |

## Query

### CustomerQuery

This module provides a `CustomerQuery` class that extends the base Thelia query and provides methods to filter cutomers by scope.

It can be used in replacement of the base CustomerQuery.

```PHP
use CustomerScope\Model\CustomerScope as CustomerScopeCustomerQuery;

$customers = CustomerScopeCustomerQuery::create()
    ->filterByScope([
        "ScopeEntity" => "myScope",
        "EntityId" => $myEntity->getId(),
    ])
    ->find();
```

Or mixed in your own query class by using the static methods.
These methods take a `ModelCriteria` query class and will act in it.

They assume that the customer table is already present in the query scope, so your query must for exemple extends `CustomerQuery` or make a join to the customer table.

```PHP
use CustomerScope\Model\CustomerScope as CustomerScopeCustomerQuery;

// MyQuery extends CustomerQuery
// or MyQuery has a join to the customer table somewhere
$myQuery = MyQuery::create();

CustomerScopeCustomerQuery::addScopeFilter(
    $myQuery,
    [
        "ScopeEntity" => "myScope",
        "EntityId" => $myEntity->getId(),
    ]
);
```
