## Create Your Own Registry - Registry Standard

To create your own DAB Registry that is able automatically integrate with any programs & applications that interact with DAB Registries (such as DAB-js and the DAB Router), **your canister interface must follow that of the DAB Registry Standard**.

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
DAB-js comes with the DAB Registry Standard candid file so that we can interact with the base functionality of *any* registry that follows the DAB Registry Standard. Let's learn how!

### 0. Preparing Your Environment

To pull and install from [@Psychedelic](https://github.com/psychedelic) via the NPM CLI, you'll need:

- A Github account
- A Github personal access token (you can create a personal acess token [here](https://github.com/settings/tokens))
- The personal access token with the correct scopes, **repo** and **read:packages** to be granted access to the [GitHub Package Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-to-github-packages).
- Authentication via `npm login`, using your Github email for the **username** and the **personal access token** as your **password**:

Once you have those ready, run:

```
npm login --registry=https://npm.pkg.github.com --scope=@Psychedelic
```

> **Note:** You only need to configure this once to install the package!
    On npm login provide your Github email as your username and the Personal access token as the password.

You can also setup your npm global settings to fetch from the Github registry everytime it finds a **@Psychdelic** package, find the instructions [here](https://docs.npmjs.com/configuring-your-registry-settings-as-an-npm-enterprise-user).

⚠️ Alternatively, a token could be passed to the `.npmrc` as `//npm.pkg.github.com/:_authToken=xxxxxx` but we'd like to keep it clean and tokenless.

### 1. Setting up DAB-js in your project

First, you need to install the DAB-js **npm package** into your project.

You can do so from the command line
```js
npm install @psychedelic/dab-js@latest
```

Find more details about installing versions in the package page [here](https://github.com/Psychedelic/DAB-js/packages/987540)

### 2. DAB Registry Standard Methods
In this step, we'll go over the possible methods you can call to any canister that follows the DAB Registry Standard.

{ methods and how to call them here }




