---
date: "1"
---
# Welcome to DAB 👋

![](imgs/main.png)

Welcome to DAB's documentation. DAB is an open internet service for data on the Internet Computer. All the data an IC app needs to make a seamless experience, accessible directly on the IC. 

DAB keeps tracks its own list of 'verified' registries but encourages anyone to create their own registry by following [the Registry Standard](standard/getting-started.md).


- Visit [our website](https://dab.ooo)
- Visit [DAB's main repository](https://github.com/psychedelic/dab)
- Visit [DAB-js repository](https://github.com/psychedelic/dab-js)


## #️⃣ V0.4.0 - DAB's Current Registries.

In v0.4.0, DAB has three registries that developers can integrate with, or submit items to:

- The NFT List (auto-surface NFTs in apps and multi-standard support).
- The Canister List (associate metadata to Canister IDs and auto-surface it in UIs).
- The Token List (auto-surface tokens in apps and multi-standard support).

----

## 🎨 The NFT List

**The NFT list** DAB provides a list of NFTs that apps & developers can **consume to surface new NFTs as they are listed in DAB, instead of manually adding them one by one**.

DAB's NFT list is **standard agnostic** and through the DAB-js library, developers can easily integrate and make calls to any NFT collection on the list regardless of their NFT standard interface (EXT, Departure Labs, etc.), **because in its library DAB wraps all standards into a common javascript interface**.

> Want to submit an NFT collection so its listed in DAB for apps to auto-surface? See below!

### 🧰 Getting Started with DAB - the NFT List

Want to connect your app to **DAB's NFT list** to auto-surface a user's NFT collections and easily integrate multiple assets and standards at once in your UI/app?

To interact with DAB's services you need to use the DAB-js library. Read our documentation or visit the DAB-js repository to get started.

- [Read our getting started guide](https://docs.dab.ooo/nft-list/getting-started/)
- [DAB-js library - Repository](https://github.com/psychedelic/dab-js)


### Making New Submissions
You can see the current listed NFT collections on our website. **Want to submit a new NFT collection to the list? Use the form below.**

- [View the current NFT Collection List📜](https://dab.ooo)
- [Submit a new NFT to the list 📫](https://dab-ooo.typeform.com/nft-list)


----

## 💠 The Token List

The Token Registry will work exactly like the NFT List. Any Token can get listed on this open registry, regardless of its standard (DIP20, EXT to start), adding metadata for UIs to surface (name, symbol, image, Canister ID, standard…)

Then UIs, apps, and DeFi experiences can consume this list & integrate it using DAB-js to integrate and auto-surface and support all tokens on the list for your users (showing their balance, allowing them to interact with them for example to make transfers), as well as anyone that’s added in the future, without having to do per-token or per-standard integrations.

- [Read our getting started guide](https://docs.dab.ooo/token-list/getting-started/)
- [DAB-js library - Repository](https://github.com/psychedelic/dab-js)

###  Making New Submissions
You can see the current listed tokens on our website. **Want to submit a new Token to the list? Use the form below.**

- [View the current Token List📜](https://github.com/Psychedelic/dab/blob/main/registries/tokens/list.json)
- [Submit a new Token to the list 📫](https://dab-ooo.typeform.com/token-list)

----

## 🛢️ The Canister List

**The Canister List** is a canister registry where you can associate Canister IDs to a metadata profile (name, front-end URL, description, logo...) to make them more discoverable by UIs. 

Apps that show Canister IDs in their UIs/apps can **integrate to the Canister List** to check if that Canister ID has associated metadata, and display it for their users to see in a more descriptive and human-readable way.

- It helps make Canister ID human-readable and identifiable.
- It helps give users information to judge whether to trust a canister or not
- It can help in the future to identify duplicates or impersonations.

[**View the Canister Registry Source Code**](https://github.com/Psychedelic/dab/tree/main/registries/canister_registry)

### Submitting/Adding a Canister ID to the Canister List

Want to submit a new Canister ID to the registry to associate metadat to it, and have integrated apps auto-surface it? Use the form below.

- [**Current List**](https://github.com/Psychedelic/dab/blob/main/registries/canister_registry/list.md)
- [**Submit a new Canister to the list 📫**](https://dab-ooo.typeform.com/canister-list)

Currently, the review process for submissions is manual and done by the DAB core team; in the future we will automate the process, and migrate to a community-governed and trustless system.

**We are exploring an automated way of adding Canister IDs and their metadata to the registry**. The main issues are confirming the controller is the one submitting it, and then adding a verification layer to avoid duplicates/phishing/impersonation.

### 🧰 Start Integrating DAB's Canister List into your App

To interact with DAB's services you need to use the DAB-js library. Read our documentation or visit the DAB-js repository to get started.

- [Read our getting started guide](https://docs.dab.ooo/canister-list/getting-started/)
* [**DAB-js library - Repository**](https://github.com/psychedelic/dab-js)

---

## 📬 Coming Next: Dapp lists (Early Applications)
After V0.3.0, we'll be working on a Dapp registries. This is still in development, **but we are receiving early submissions to be added to these lists on release**.

- [Dapp List](https://dab-ooo.typeform.com/dapp-list)