---
date: "1"
---

#   💠 Getting Started - Integrate the Token List


All right! Ready to integrate DAB's Token list into your app? Your main point of interaction for interacting with DAB is the **[DAB-js library](https://github.com/Psychedelic/DAB-js/)**, which we built to provide a simple plug-n-play way of integrating DAB. With it, you will:

1. Query DAB's Token List to get an Array of the Tokens a user (Principal) owns.
2. Use the DAB-js standard interface wrapper to interact with listed assets (transfer, check a user's owned tokens,  details, etc.)

**Let's do a double click on that last point**. The list is standard-agnostic, meaning Tokens from different standards are available (DIP20, XTC, EXT, WICP.). To facilitate your integration to them, the **DAB-js library wraps all of the standards listed in DAB** into a **common javascript interface**. Instead of calling different methods, depending on the standard, you use DAB's common interface, and let DAB translate the calls.

> The mainnet canister ID for DAB's Token list is: `qwt65-nyaaa-aaaah-qcl4q-cai`

---
## 0. ⚙️ Preparing your environment

To pull and install from [@Psychedelic](https://github.com/psychedelic) via the NPM CLI, you'll need:

- A Github account
- A Github personal access token (you can create a personal access token [here](https://github.com/settings/tokens))
- The personal access token with the correct scopes, **repo** and **read:packages** to be granted access to the [GitHub Package Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-to-github-packages).
- Authentication via `npm login`, using your Github email for the **username** and the **personal access token** as your **password**:

Once you have those ready, run:

```
npm login --registry=https://npm.pkg.github.com --scope=@Psychedelic
```

> **Note:** You only need to configure this once to install the package!
    On npm login provide your Github email as your username and the Personal access token as the password.

You can also setup your npm global settings to fetch from the Github registry everytime it finds a **@Psychdelic** package:

```sh
npm set //npm.pkg.github.com/:_authToken "$PAT"
```

⚠️ Alternatively, a token could be passed to the `.npmrc` as `//npm.pkg.github.com/:_authToken=xxxxxx` but we'd like to keep it clean, tokenless. Here's an example where the `PAT` is an environment variable:

```sh
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

## 2. 💠 Interacting with Tokens (getTokenActor, transfer, details)

To interact with the user's Tokens and, for example, trigger a transfer, you need to **initialize/get an Token actor object**. This is done using the **getTokenActor** method, where you need to pass:

- `canisterID`: the Canister ID of the token you want to interact with (e.g XTC)
- `agent`: and HttpAgent (instantiated with agent-js or Plug)
- `standard`: a str with the name of the Token standard (DIP20, EXT)

**It's important to note:** without passing a template interface, you can use the following methods:

- send
- getMetadata
- getBalance
- burnXTC (in the case of XTC)

These are the common methods shared across all interfaces/standards that DAB-js wraps in its common universal interface. To **use other methods that are not shared across standards**, you can import and pass that standard's interface into the type variable of getTokenActor and use the base methods as "_method" (e.g. if transfer, do _transfer).

> (Current standards supported and string name: DIP20, EXT, XTC, WICP)

```js
import { getTokenActor } from '@psychedelic/dab-js'

export const getTokenActor = <T={}>({
  canisterId: string,
  agent: HttpAgent,
  standard: string
}): TokenActor => {
  return createTokenActor<T>(canisterID, agent, standard);
};
```

This should return an actor object with the following interfaces.

```js
export default class TokenActor {
  agent: HttpAgent;

  canisterId: string;

  constructor(canisterId: string, agent: HttpAgent) {
    this.agent = agent;
    this.canisterId = canisterId;
  }

  abstract send({to: string, from: string, amount: string}): Promise<SendResponse>;

  abstract getBalance(user: Principal): Promise<Balance>;

  abstract getMetadata(): Promise<Metadata>;

  abstract burnXTC({to: Principal, amount: string}): Promise<BurnResult>;
}
```

As you can see this actor contains the **standard javascript interface** of DAB's **Token standard wrapper**. It has generic calls to interact with tokens regardless of their standard (as long as their interface is wrapped in the standard wrapper).

- `send`: Request the transfer of a token balance the user owns to another address. 
- `getMetadata`: Returns the details of the token.
- `getBalance`: Returns the balance of a specific user.
- `burnXTC`: Request burning XTC to transfer raw unwrapped cycles to an address.

Here's a more detailed breakdown of the interfaces it returns:

```js
export type SendResponse =
  | { height: string }
  | { amount: string }
  | { transactionId: string };

export interface SendParams {
  to: string;
  from: string;
  amount: string;
}

export interface BurnParams {
  to: Principal;
  amount: string;
}

export interface Balance {
  value: string;
  decimals: number;
}

export type Metadata = FungibleMetadata | NonFungibleMetadata;

export interface FungibleMetadata {
  fungible: TokenMetaData & {
    metadata?: Int8Array[];
  };
}

export interface TokenMetaData {
  name: string;
  decimals: number;
  symbol: string;
}

export interface NonFungibleMetadata {
  nonfungible: {
    metadata: Int8Array[];
  };
```

### getBalance - Request a User's Balance for a Token

This method allows you to fetch an object with a `value`(str) `decimals`(number) with the balance a user owns in a specific token and its decimals.

Here, you would need to pass:

- `principal`: a str of the user's Principal ID you want to check for owned tokens.

> (See that in the variable TokenActor, we are instantiating the Token actor object, passing a canisterID for the token we want to interact with, an agent, and the name of the standard as a str).

```js

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


### send - Request to Transfer a User's Token to a Different Address

This method allows you to request the transfer of an Token the passed identity owns in the token you have **initialized in the actor**.

In this method you need to pass:

- `to`: a str of a Principal ID for the destination address.
- `from`: origin address of the user whose balance is reduced.
- `amount`: the amount of tokens to be transferred.

> (See that in the variable TokenActor, we are instantiating the Token actor object, passing a canisterID for the token we want to interact with, an agent, and the name of the standard as a str).

```js
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

The transfer call, **if successful**  returns a send response. **If the transaction fails, it will return an error**.

### getMetadata - Fetch the Details of a Specific Token.

This method allows you to fetch an array with the details and metadata of token you **initialized in the actor**.

In this method, you don't need to pass anything.

> (See that in the variable TokenActor, we are instantiating the Token actor object, passing a canisterID for the token we want to interact with, an agent, and the name of the standard as a str).

```js
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

This call returns one object with the metadata of the specific token queried.

---

### burnXTC - Burn XTC and Transfer Raw Cycles.

This method allows you to request burning (unwrapping) an amount of XTC into raw cycles to transfer them to a destination address:

In this method, you need to pass:

- `to`: a str of a Principal ID for the destination address.
- `amount`: the amount of tokens to be transferred.

> (See that in the variable TokenActor, we are instantiating the Token actor object, passing a canisterID for the token we want to interact with, an agent, and the name of the standard as a str).

```js
import { Principal } from '@dfinity/principal';
import { getTokenActor } from '@psychedelic/dab-js'

...
const burnXTC = async () => {
  const canisterId = 'utozz-siaaa-aaaam-qaaxq-cai';
  const to = 'r4rmh-mbkzp-gv2na-yvly3-zcp3r-ocllf-pt3p3-zsri5-6gqvr-stvs2-4ae';
  const standard = 'WICP';
  const TokenActor = getTokenActor({canisterId, agent, standard});

  const details = await TokenActor.burnXTC({to, amount: '1.2'});
}
burnXTC()
```

This call returns one object with the metadata of the specific token queried.

---
## 3. 📮 Using DAB Registry Standard Methods (name, get, add, remove)

Since the Token list also follows the DAB Registry standard, we can interact with it's base methods thrugh the use of the `TokenRegistry` object that DAB-js provides.  

This pre-made object allows you to skip the step of creating a new instance of the Registry Class for the NFT registry, like we would do in the [Create Your Own Registry](../../standard/getting-started/#2-dab-registry-standard-methods) section.

```js
import { TokenRegistry } from '@psychedelic/dab-js';

const token_registry = new TokenRegistry();
const getId = "PRINCIPAL-ID-HERE";

const testRegistryMethods = async (registry: Registry, getId: string) => {
    const name = await registry.name();
    console.log('Registry Name: ', name);

    console.log('Getting metadata for id', getId);
    const getResponse = await registry.get(getId);
    console.log('Get Response: ', getResponse);
};

testRegistryMethods(token_registry, getId);
```

You're able to interact with `add` and `remove` in the same way. However, these methods require you to be registry's admin.
 