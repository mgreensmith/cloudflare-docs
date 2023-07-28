---
pcx_content_type: concept
title: KV bindings
weight: 7
---

# KV bindings

KV bindings allow communication between a Worker and a KV namespace.

## Reference KV from Workers

A KV namespace is a key-value database that is replicated to Cloudflare's global network. To connect to a KV namespace from within a Worker, you must define a binding that points to the namespace's ID.

The name of your binding does not need to match the KV namespace's name. Instead, the binding should be a valid JavaScript identifier because it will exist as a global variable within your Worker.

A KV namespace will have a name you choose (for example, `My tasks`), and an assigned ID (for example, `06779da6940b431db6e566b4846d64db`).

To execute your Worker, define the binding. In the following example, the binding is called `TODO`. In the `kv_namespaces` portion of your `wrangler.toml` file, add:

```toml
---
filename: wrangler.toml
---
name = "worker"

# ...

kv_namespaces = [
  { binding = "TODO", id = "06779da6940b431db6e566b4846d64db" }
]
```

With this, the deployed Worker will have a `TODO` global variable. Any methods on the `TODO` binding will map to the KV namespace with an ID of `06779da6940b431db6e566b4846d64db` – which you called `My Tasks` earlier.

{{<tabs labels="js/esm | js/sw">}}
{{<tab label="js/esm" default="true">}}

```js
export default {
  async fetch(request, env, ctx) {
    // Get the value for the "to-do:123" key
    // NOTE: Relies on the `TODO` KV binding that maps to the "My Tasks" namespace.
    let value = await env.TODO.get("to-do:123");

    // Return the value, as is, for the Response
    return new Response(value);
  },
};
```
{{</tab>}}
{{<tab label="js/sw">}}

```js
addEventListener("fetch", async (event) => {
  // Get the value for the "to-do:123" key
  // NOTE: Relies on the `TODO` KV binding that maps to the "My Tasks" namespace.
  let value = await TODO.get("to-do:123");

  // Return the value, as is, for the Response
  event.respondWith(new Response(value));
});
```
{{</tab>}}
{{</tabs>}}

## Reference KV from a Module Worker

When using the Module Worker syntax, `env` is passed in the fetch handler. 

```js
export default {
  async fetch(request, env) {
    const valueFromKV = await env.NAMESPACE.get("someKey");
    return new Response(valueFromKV);
  }
}
```