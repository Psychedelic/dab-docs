---
date: "1"
---

#  📓 Getting Started - Integrate the Address Book

You can integrate the Address Book to your dApp or wallet to both show users all contacts saved in the service for their Principal ID; or allow them to manage their contacts (add/remove). A universal address book across all interfaces!

Your main point of interaction for interacting with DAB is the **[DAB-js library](https://github.com/Psychedelic/DAB-js/)**, which we built to provide a simple plug-n-play way of integrating DAB. With it, you will:

> The mainnet canister ID for DAB's Address Book is: `i73cm-daaaa-aaaah-abhea-cai`

---
## 0. ⚙️ Preparing your environment

To pull and install from [@Psychedelic](https://github.com/psychedelic) via the NPM CLI, you'll need:

- A Github account
- A Github personal access token (you can create a personal access token [here](https://github.com/settings/tokens))
- The personal access token with the correct scopes, **repo** and **read:packages** to be granted access to the [GitHub Package Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-to-github-packages).
- Authentication via `npm login`, using your Github email for the **username** and the **personal access token** as your **password**:

Once you have those ready, run:

```bash
npm login --registry=https://npm.pkg.github.com --scope=@psychedelic
```

> **Note:** You only need to configure this once to install the package!
    On npm login provide your Github email as your username and the Personal access token as the password.

You can also setup your npm global settings to fetch from the Github registry everytime it finds a **@Psychdelic** package:

```bash
npm set //npm.pkg.github.com/:_authToken "$PAT"
```

⚠️ Alternatively, a token could be passed to the `.npmrc` as `//npm.pkg.github.com/:_authToken=xxxxxx` but we'd like to keep it clean, tokenless. Here's an example where the `PAT` is an environment variable:

```bash
@psychedelic:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${PAT}
```

## 1. 🧰 Setting up DAB-js in your project

First, you need to install the DAB-js **npm package** into your project.

You can do so from the command line:
```js
npm install @psychedelic/dab-js@latest
```

Find more details about installing versions in the package page [here](https://github.com/Psychedelic/DAB-js/packages/987540)


---

## 2. 📓 Interacting with a User's Address Book (getAddressBookActor)

To interact with the user's Address Book and, for example, get your contacts, you need to **initialize/get an Address Book actor object**. This is done using the **getAddressBookActor** method, where you need to pass:

- `agent`: and HttpAgent (instantiated with agent-js or Plug)

```js
export const getAddressBookActor = (agent: HttpAgent) => {
    return Actor.createActor<AddressBookInterface>(addressBookIDL, { agent, canisterId: CANISTER_ID });
}
```

This should return an actor object with the following interfaces. This actor allows you to interact with the user's address book entries in the canister.
```js
interface Value {
  PrincipalId: Principal,
  AccountId: string,
  Icns: string,
}

interface Address {
  name: string,
  description: string,
  emoji: string,
  value: Value,
}

export default class AddresBookActor {
  agent: HttpAgent;
  canisterId: string;

  abstract name(): Promise<string>;  

  abstract add(address: Address): Promise<OperationResponse>;

  abstract getAll(): Promise<Array<Address>>;

  abstract remove(entry: string): Promise<void>;
}

```

### getAddresses - Fetch All of a User's Contacts

This auxiliary method allows you to fetch an array including all of the user's contacts in their address book in DAB.
This is equivalent to creating an AddressBook actor and calling its `get_all` method.

Here, you would need to pass an agent (plug's agent or one instantiated with agent-js):

```js
import { getAddresses } from '@psychedelic/dab-js'

const getUserAddresses = async () => getAddresses(ic?.plug?.agent);
```


### addAddress - Add a New Contact to the User's Address Book

This method allows you to add a new contact to the user's address book.

In this method you need to pass an object with the following interface:

```ts
interface AddressBookValue {
  PrincipalId: Principal,
  AccountId: string,
  Icns: string,
}

interface Address {
  name: string,             // Contact's name
  description: string,      // Contact's description
  emoji: string,            // Contact's emoji icon
  value: AddressBookValue,  // Contact's address (one of PrincipalId, AccountId or ICNS)
}
```

DAB's address book currently supports **Account IDs, Canister IDs, Principal IDs, and ICNS .icp names** as valid contact addresses.


```js


import { Principal } from '@dfinity/principal';
import { addAddress } from '@psychedelic/dab-js'

...
const addAddress = async (contact) => {
  return addAddress(window?.ic?.plug?.agent, contact);
};

// ICNS Example
await addAddress({
  name: 'nico',
  description: 'A friend of mine',
  emoji: 😀,
  value: {
    Icns: 'nico.icp',
  }
});

// PID Example
await addAddress({
  name: 'rocky',
  description: 'Another friend',
  emoji: 😀,
  value: {
    Principal: Principal.from('xksyk-jrty5-s6ei6-k3cak-wb2mv-5rtv5-atxvn-gnqsm-i6kuh-irkqa-4ae'),
  }
});

// AccountId Example
await addAddress({
  name: 'Exchange',
  description: 'My cex address',
  emoji: 😀,
  value: {
    AccountId: '21f2c489e7626ef1af7866ff4fecd15335731657000bea2c1a6ab665111467e8',
  }
});
```

The call returns a `Response` object which has the following format:
```ts
export type Response = { 'Ok' : [] | [string] } |
  { 'Err' : Error };
```

If the call was **successful**, an `{ Ok: [] | [string] }` object is returned.
If the call **errored out instead**, you'll get an `{ 'Err' : Error }` with the error cause.



### removeAddress - Remove a Contact From the Address Book.

This method allows you to eliminate a contact from a user's address book.

In this method, you need to pass:

- `addressName`: A string of the name of the contact to be removed.
- `agent`: An agent, either plug's or one instantiated with agent-js.


```ts
import { removeAddress } from '@psychedelic/dab-js'

const removeUserAddress = async (addressName: string) => removeAddress(agent, addressName);
```


---