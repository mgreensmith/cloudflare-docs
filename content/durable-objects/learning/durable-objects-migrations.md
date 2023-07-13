---
pcx_content_type: concept
title: Durable Objects migrations
---

# Durable Objects migrations

You must initiate a migration process when you create a new Durable Object class, rename, delete, or transfer an existing Durable Objects class. This process informs the Workers runtime of the changes and provides it with instructions on how to deal with those changes.

{{<Aside type="note">}}

Updating code for an existing Durable Object class does not require a migration. To update code for an existing Durable Object class, run [`wrangler deploy`](/workers/wrangler/commands/#deploy). This is true even for changes to how code interacts with persistent storage. Because of [global uniqueness](/durable-objects/learning/limitations/#global-uniqueness), you do not have to be concerned about old and new code interacting with the same storage simultaneously. However, it is your responsibility to ensure that new code is backwards compatible with existing stored data.

{{</Aside>}}


The most common migration performed is a new class migration, which informs the system that a new Durable Object class is being uploaded.

Migrations can also be used for transferring stored data between two Durable Object classes. Rename migrations are used to transfer stored objects between two Durable Object classes in the same script. Transfer migrations are used to transfer stored objects between two Durable Object classes in different scripts.

The destination class (the class that stored objects are being transferred to) for a rename or transfer migration must be exported by the deployed script.

{{<Aside type="warning" header="Important">}}

After a rename or transfer migration, requests to the destination Durable Object class will have access to the source Durable Object's stored data.

After a migration, any existing bindings to the original Durable Object class (for example, from other Workers) will automatically forward to the updated destination class. However, any Worker scripts bound to the updated Durable Object class must update their `[durable_objects]` configuration in the `wrangler.toml` file for their next deployment.

{{</Aside>}}

Migrations can also be used to delete a Durable Object class and its stored objects.

{{<Aside type="warning" header="Important">}}

Running a delete migration will delete all Durable Object instances associated with the deleted class, including all of their stored data. Do not run a delete migration on a class without first ensuring that you are not relying on the Durable Objects within that class anymore. Copy any important data to some other location before deleting.

{{</Aside>}}

### Durable Object migrations in `wrangler.toml`

Migrations are performed through the `[[migrations]]` configurations key in your `wrangler.toml` file. 

Migrations require a migration tag, which is defined by the `tag` property in each migration entry. 

Migration tags are treated like unique names and are used to determine which migrations have already been applied. Once a given script has a migration tag set on it, all future script uploads must include a migration tag.

The migration list is an ordered array of tables, specified as a top-level key in your `wrangler.toml` file. The migration list is inherited by all environments and cannot be overridden by a specific environment.

All migrations are applied at deployment. Each migration can only be applied once per [environment](/workers/wrangler/environments/).

To illustrate an example migrations workflow, the `DurableObjectExample` class can be initially defined with:

```toml
[[migrations]]
tag = "v1" # Should be unique for each entry
new_classes = ["DurableObjectExample"] # Array of new classes
```

Each migration in the list can have multiple directives, and multiple migrations can be specified as your project grows in complexity. For example, you may want to rename the `DurableObjectExample` class to `UpdatedName` and delete an outdated `DeprecatedClass` entirely.

```toml
[[migrations]]
tag = "v1" # Should be unique for each entry
new_classes = ["DurableObjectExample"] # Array of new classes

[[migrations]]
tag = "v2"
renamed_classes = [{from = "DurableObjectExample", to = "UpdatedName" }] # Array of rename directives
deleted_classes = ["DeprecatedClass"] # Array of deleted class names
```

{{<Aside type="note">}}

Note that `.toml` files do not allow line breaks in inline tables (the `{key = "value"}` syntax), but line breaks in the surrounding inline array are acceptable.

{{</Aside>}}