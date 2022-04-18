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

This should return an actor object with the following interfaces. This actor allows you to interact with the user's address book entries. 

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

This method allows you to fetch an array including all of the user's contact in their address book in DAB.

Here, you would need to pass:

- 

> (See that in the variable AddressBookActor, we are instantiating the Address Book actor object, passing on an agent and the address book's canister).

```js

example needed

import { Principal } from '@dfinity/principal';
import { getTokenActor } from '@psychedelic/dab-js'

...
const getUserBalance = async () => {
  const principal = 'r4rmh-mbkzp-gv2na-yvly3-zcp3r-ocllf-pt3p3-zsri5-6gqvr-stvs2-4ae';
  const canisterId = 'utozz-siaaa-aaaam-qaaxq-cai';
  const standard = 'WICP';
  const TokenActor = getTokenActor({canisterId, agent, standard});
  const userTokens = await TokenActor.getBalance(Principal.fromText(principal));
}
getUserBalance();
```


### addAddress - Add a New Contact to the User's Address Book

This method allows you to add a new contact to the user's address book.

In this method you need to pass:

- `name`: 
- `description`: 
- `emoji`: 
- `value`: 

DAB's address book currently supports **Account IDs, Canister IDs, Principal IDs, and ICNS .icp names** as valid contact addresses.

> (See that in the variable AddressBookActor, we are instantiating the Address Book actor object, passing on an agent and the address book's canister).

```js

need example

import { Principal } from '@dfinity/principal';
import { getTokenActor } from '@psychedelic/dab-js'

...
const send = async () => {
  const from = 'r4rmh-mbkzp-gv2na-yvly3-zcp3r-ocllf-pt3p3-zsri5-6gqvr-stvs2-4ae';
  const to = 'j63dc-rpowz-5pt7o-nah2l-z33hc-yacyk-ep3kc-fcxsp-evlxc-p3rv5-2qe'
  const canisterId = 'utozz-siaaa-aaaam-qaaxq-cai';
  const standard = 'WICP';
  const TokenActor = getTokenActor({canisterId, agent, standard});
  await TokenActor.transfer({from, to, amount: '1.2'});
}
send();
```

The call, **if successful**  returns a response. **If the transaction fails, it will return an error**.

### removeAddress - Remove a Contact From the Address Book.

This method allows you to eliminate a contact from a user's address book.

In this method, you need to pass:

- `addressName`: A string of the name of the contact to be removed.

> (See that in the variable AddressBookActor, we are instantiating the Address Book actor object, passing on an agent and the address book's canister).

```js

example needed
 
import { Principal } from '@dfinity/principal';
import { getTokenActor } from '@psychedelic/dab-js'

...
const getTokenDetails = async () => {
  const canisterId = 'utozz-siaaa-aaaam-qaaxq-cai';
  const standard = 'WICP';
  const TokenActor = getTokenActor({canisterId, agent, standard});

  const details = await TokenActor.getMetadata();
}
getTokenDetails()
```


---