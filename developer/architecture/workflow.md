# Workflow
Reaction template display (layout) and workflow rules are created and stored in the Package registry.

A workflow is similar to `package.registry` entries,  but  `package.layout` entries are defined by the following properties:
- It does not control routes anywhere.
- each workflow in the package registry has a definition in Shops
- stores **status** in a **collection** specified in Shops
- collections should have attached _Workflow schema_
- **audience** is just another label for 'permissions', essentially just permissions for UIX.
- can be used to control page layout for the content manager
- audience can be used to control different check out, different product views,  for users with different roles..
- a **workflow** controls "the how and where" of layout components
- can be used to trigger events from analytics events
- to disable a workflow template remove audience roles
- routes could be dynamically generated from registry

<u>_The default workflow configuration components are configured in the following manner:_</u>

## Shops Collection

```
    "layout" : [
        {
            "layout" : "coreLayout",
            "workflow" : "coreLayout",
            "theme" : "default",
            "enabled" : true
        },
        {
            "layout" : "coreLayout",
            "workflow" : "coreCartWorkflow",
            "theme" : "default",
            "collection" : "Cart",
            "enabled" : true
        },
        {
            "layout" : "coreLayout",
            "workflow" : "coreOrderWorkflow",
            "theme" : "default",
            "collection" : "Orders",
            "enabled" : true
        }
    ]
```

## Collection Workflows
For each collection using a workflow, a workflow schema is attached.  The state/status of a workflow is stored as an object on each document in the collection.

```javascript
"workflow" : {
    "status" : "checkoutLogin"
    "workflow": ["new", "checkoutLogin"]
},
```

Where **status** is the current workflow, and **workflow** are the workflow steps that have begun, or finished processing.

If `workflow.workflow` contains the current `workflow.status`, that means the workflow is processing, and when status now longer contains the workflow, but it exists in `workflow.workflow` the workflow has been completed.

## Workflow Schema
For reference, the Workflow schema is:

```javascript
ReactionCore.Schemas.Workflow = new SimpleSchema({
  template: {
    type: String,
    optional: true
  },
  label: {
    type: String,
    optional: true
  },
  provides: {
    type: String,
    optional: true
  },
  audience: {
    type: [String],
    optional: true
  },
  status: {
    type: String,
    optional: true
  },
  workflow: {
    type: [String],
    optional: true
  }
});
```
