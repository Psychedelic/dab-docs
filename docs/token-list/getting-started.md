---
date: "1"
---

#   💠 Getting Started - Integrate the Token List


All right! Ready to integrate DAB's Token list into your app? Your main point of interaction for interacting with DAB is the **[DAB-js library](https://github.com/Psychedelic/DAB-js/)**, which we built to provide a simple plug-n-play way of integrating DAB. With it, you will:

1. Query DAB's Token List to get an Array of the Tokens a user (Principal) owns.
2. Use the DAB-js standard interface wrapper to interact with listed assets (transfer, check a user's owned tokens,  details, etc.)

**Let's do a double click on that last point**. The list is standard-agnostic, meaning Tokens from different standards are available (DIP20, EXT.). To facilitate your integration to them, the **DAB-js library wraps all of the standards listed in DAB** into a **common javascript interface**. Instead of calling different methods, depending on the standard, you use DAB's common interface, and let DAB translate the calls.

> The mainnet canister ID for DAB's Token list is: `aipdg-waaaa-aaaah-aaq5q-cai`

---
## 0. ⚙️ Preparing your environment

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

## 2. 💠 Fetching All Tokens the User Owns (getAllUserTokens)

In this step, you will use the getAllUserTokens method to get an array with all the Tokens (and their details) the user (a Principal ID) owns. 

Here, DAB takes the identity you pass, and checks in every token currently on the DAB list (XTC, WICP...) for the individual assets the user owns (e.g. 1 XTC).

You need to pass:

- `agent`: An agent (an Http agent, instantiated with agent-js or Plug) <optional>
- `user`: A Principal object (a user's Principal ID instantiated as a principal object)

```js
import { Principal } from '@dfinity/principal';
import { getAllUserNFTs } from '@psychedelic/dab-js'
...
const getNFTCollections = async () => {
  const principal = 'r4rmh-mbkzp-gv2na-yvly3-zcp3r-ocllf-pt3p3-zsri5-6gqvr-stvs2-4ae';
  const collections = await getAllUserNFTs(
    { user: Principal.fromText(principal) }
  );
}
getNFTCollections();
```

This call will return an array that includes an interface with the details of each token where a user owns a balance (name, canister id, standard, tokens).

Inside that interface the **tokens object** includes an array with each individual asset, and its details (index, canister, id, name, url, metadata, standard). This is **all the data you need to surface in the UI for your user.**

```js
const getAllUserNFTs = async (
  agent: HttpAgent,
  user: Principal
): Promise<Array<{
  name: string;
  canisterId: string;
  standard: string;
  icon: string;
  description: string;
  tokens: Array<{
    index: bigint;
    canister: string;
    id?: string;
    name?: string;
    url: string;
    metadata: any;
    standard: string;
    collection?: string;
  }>;
}>>
```

Here's a more detailed breakdown of each interface the array returns for clarity:

```js
interface NFTDetails {
  index: bigint;
  canister: string;
  id?: string;
  name?: string;
  url: string;
  metadata: any;
  standard: string;
  collection?: string;
}

interface NFTCollection {
  name: string;
  canisterId: string;
  standard: string;
  tokens: NFTDetails[];
  icon: string;
  description: string;
}

const getAllUserNFTs = async (
  agent: HttpAgent,
  user: Principal
): Promise<NFTCollection[]>
```

---

## 3. 🌯 Interacting with Tokens (getUserTokens, transfer, details)

To interact with the user's Tokens and, for example, trigger a transfer, you need to **initialize/get an Token actor object**. This is done using the **getTokenActor** method, where you need to pass:

- `canisterID`: the Canister ID of the token you want to interact with (e.g XTC)
- `agent`: and HttpAgent (instantiated with agent-js or Plug)
- `standard`: a str with the name of the Token standard (DIP20, EXT)

> (Current standards supported and string name: DIP20, EXT)

```js
import { getAllUserNFTs } from '@psychedelic/dab-js'

export const getNFTActor = ({
  canisterId: string,
  agent: HttpAgent,
  standard: string
}): NFT => {
  return new NFT_STANDARDS[standard](canisterId, agent);
};
```

This should return an actor object with the following interfaces.

```js
export default abstract class NFT {
  abstract standard: string;

  agent: HttpAgent;

  canisterId: string;

  constructor(canisterId: string, agent: HttpAgent) {
    this.agent = agent;
    this.canisterId = canisterId;
  }

  abstract getUserTokens(principal: Principal): Promise<NFTDetails[]>;

  abstract transfer(to: Principal, tokenIndex: number): Promise<void>;

  abstract details(tokenIndex: number): Promise<NFTDetails>;
}
```

As you can see this actor contains the **standard javascript interface** of DAB's **Token standard wrapper**. It has generic calls to interact with tokens regardless of their standard (as long as their interface is wrapped in the standard wrapper).

- `getUserTokens`: Fetches an array of all tokens the passed identity owns. 
- `transfer`: Request the transfer of a token balance the user owns to another address.
- `details`: Returns the details of **any token**.


### getUserTokens - Fetch a User's Owned Tokens

This method allows you to fetch an array with the details of all the tokens a user owns in the token you have **initialized in the actor**.

Here, you would need to pass:

- `principal`: a str of the user's Principal ID you want to check for owned tokens.

> (See that in the variable TokenActor, we are instantiating the Token actor object, passing a canisterID for the token we want to interact with, an agent, and the name of the standard as a str).

```js

import { Principal } from '@dfinity/principal';
import { getNFTActor } from '@psychedelic/dab-js'

...
const getUserNFTs = async () => {
  const principal = 'r4rmh-mbkzp-gv2na-yvly3-zcp3r-ocllf-pt3p3-zsri5-6gqvr-stvs2-4ae';
  const canisterId = 'qcg3w-tyaaa-aaaah-qakea-cai';
  const standard = 'ICPunks';
  const NFTActor = getNFTActor(canisterId, agent, standard);
  const userTokens = await NFTActor.getUserTokens(Principal.fromText(principal));
}
getUserNFTs();
```

This returns an array with the following interface, with metadata of each owned asset:

```js
export interface NFTDetails {
  index: bigint;
  canister: string;
  id?: string;
  name?: string;
  url: string;
  metadata: any;
  standard: string;
  collection?: string;
}
```

### sendToken - Request to Transfer a User's Token to a Different Address

This method allows you to request the transfer of an Token the passed identity owns in the token you have **initialized in the actor**.

In this method you need to pass:

- `to`: a str of a Principal ID for the destination address.

> (See that in the variable TokenActor, we are instantiating the Token actor object, passing a canisterID for the token we want to interact with, an agent, and the name of the standard as a str).

```js
import { Principal } from '@dfinity/principal';
import { getNFTActor } from '@psychedelic/dab-js'

...
const sendNFT = async () => {
  const to = 'r4rmh-mbkzp-gv2na-yvly3-zcp3r-ocllf-pt3p3-zsri5-6gqvr-stvs2-4ae';
  const index = 5;
  const standard = 'ICPunks'
  const canisterId = 'qcg3w-tyaaa-aaaah-qakea-cai';
  const NFTActor = getNFTActor(canisterId, agent, standard);
  await NFTActor.transfer(Principal.fromText(to), index);
}
sendNFT();
```

The transfer call, **if successful** doesn't return anything after being executed. **If the transaction fails, it will return an error**.

### getTokenDetails - Fetch the Details of Any Specific Token.

This method allows you to fetch an array with the details and metadata of any asset on the index of the token you **initialized in the actor**.

In this method, you need to pass:

- `tokenIndex`: the index number for the token you want the details of.

> (See that in the variable TokenActor, we are instantiating the Token actor object, passing a canisterID for the token we want to interact with, an agent, and the name of the standard as a str).



```js
import { Principal } from '@dfinity/principal';
import { getNFTActor } from '@psychedelic/dab-js'

...
const getTokenDetails = async () => {
  const tokenIndex = 5;
  const canisterId = 'qcg3w-tyaaa-aaaah-qakea-cai';
  const standard = 'ICPunks';
  const NFTActor = getNFTActor(canisterId, agent, standard);

  const details = await NFTActor.details(tokenIndex);
}
getTokenDetails()
```

This call returns one object with the metadata of the specific token queried.
