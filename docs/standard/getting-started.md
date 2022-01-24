## Create Your Own Registry - Registry Standard

To create your own DAB Registry that is able automatically integrate with any programs & applications that interact with DAB Regstries (such as DAB-js and the DAB Router), your canister interface must follow that of the DAB Registry Standard.

For anyone wondering what this might look like, we have created the DAB Registry Canister. This example canister fully implements the DAB Registry Standard and can act as a guide for you to do so as well.

- **DAB Registry Standard Candid Interface [ADD LINK]**
- **DAB Registry Canister [ADD LINK]**

Any addition logic you'd like to add is completely up to your descretion, as long as the base methods are met. There **must be no descrepancies between your registry's candid interface and that of the standard.**
## Registry Standard's Required Interface Methods
In order to follow the DAB Registry Standard, these four base methods must be met:
### 1. Get Registry's Name - name
Returns the name of your registry canister.

```bash
"name"   : () -> (text) query
```

### 2. Get an Item From Registry - get
Returns the metadata associated with the passed in Principal ID if it exists in the registry.

```bash
"get"    : (principal) -> (opt metadata) query
```

### 3. Add an Iten to Registry - add 
Adds metadata against the Principal ID in your registry. Only canister admins can access this method. 

```bash
"add"    : (principal, metadata) -> (response)
```

### 4. Remove an Item From Registry - remove
Removes an item and its associated metadata from your registry. Only canister admins can access this method.

```bash
 "remove" : (principal) -> (response)
```

## Interacting With Custom Registries

[ how to use DAB-js to interface with any DRS canister GOES HERE ]