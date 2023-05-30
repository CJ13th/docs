---
description: How do I add my project to the registry?
---

# Getting Started

## Project Registry Location

Project Registry is deployed on several chains. To add a project, interact with the contract on your chosen chain. You should interact with the Project Registry proxy address, which is listed below.

| Chain            | Address                                    |
| ---------------- | ------------------------------------------ |
| Ethereum Mainnet | 0x03506eD3f57892C85DB20C36846e9c808aFe9ef4 |
| Optimism Mainnet | 0x8e1bD5Da87C14dd8e08F7ecc2aBf9D1d558ea174 |
| Fantom Mainnet   | 0x8e1bD5Da87C14dd8e08F7ecc2aBf9D1d558ea174 |
| Fantom Testnet   | 0xCA73C80BA8E64161EA79583c43eBF9A6424D9c19 |
| Goerli           | 0x832c5391dc7931312CbdBc1046669c9c3A4A28d5 |



## Interacting with the Project Registry

The following sections contain scripts to help you register and update your project on the Project Registry. For more context about these actions and the metaPtr object, please see the [Project Registry](./) page.&#x20;

### Adding a Project

To add a project, use the createProject(metaPtr) function. The script below adds a project to the Project Registry on Goerli.&#x20;

{% hint style="info" %}
Your project id will be emitted when this transaction is completed successfully. You can find this in etherscan in the transaction details.
{% endhint %}

```typescript
import { ethers } from "hardhat";
import * as dotenv from 'dotenv';
dotenv.config()


async function main() {
    console.log("Testing Project Registry");
    const contractAddress = "0x832c5391dc7931312CbdBc1046669c9c3A4A28d5";
    const testMetaPtr = {protocol: 1, pointer: "test-data"};
    
    const provider = ethers.getDefaultProvider("goerli", {alchemy: process.env.ALCHEMY_API_KEY});
    const wallet = ethers.Wallet.fromMnemonic(process.env.MNEMONIC_ACCT1 ?? "");

    const signer = wallet.connect(provider);
    console.log(`Connected to the wallet ${signer.address}`);

    const projectRegistryContract = await ethers.getContractAt('ProjectRegistry', contractAddress, signer);

    const tx = await projectRegistryContract.createProject(testMetaPtr);
    await tx.wait();
    console.log(`Done! Transaction hash: ${tx.hash}`)
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});

```

### Updating Project Metadata

To update the project metadata, use the updateProjectMetadata(projectId, metaPtr) function.

```typescript
import { ethers } from "hardhat";
import * as dotenv from 'dotenv';
dotenv.config()


async function main() {
    console.log("Testing Project Registry");
    const projectId = 159;
    const contractAddress = "0x832c5391dc7931312CbdBc1046669c9c3A4A28d5";
    const updateMetaPtr = {protocol: 1, pointer: "new-test-data"};
    
    const provider = ethers.getDefaultProvider("goerli", {alchemy: process.env.ALCHEMY_API_KEY});
    const wallet = ethers.Wallet.fromMnemonic(process.env.MNEMONIC_ACCT1 ?? "");

    const signer = wallet.connect(provider);
    console.log(`Connected to the wallet ${signer.address}`);
    console.log(`Calling contract ${contractAddress}`);

    const projectRegistryContract = await ethers.getContractAt('ProjectRegistry', contractAddress, signer);

    const tx = await projectRegistryContract.updateProjectMetadata(projectId, updateMetaPtr);
    await tx.wait();
    console.log(`Done! Transaction hash: ${tx.hash}`)
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});

```

### Add Project Owner

To update the project metadata, use the addProjectOwner(uint256 projectId,address newOwner) function.

```typescript
import { ethers } from "hardhat";
import * as dotenv from 'dotenv';
dotenv.config()


async function main() {
    console.log("Testing Project Registry");
    const projectId = 159;
    const newOwner = "new owner address";
    const contractAddress = "0x832c5391dc7931312CbdBc1046669c9c3A4A28d5";
    
    const provider = ethers.getDefaultProvider("goerli", {alchemy: process.env.ALCHEMY_API_KEY});
    const wallet = ethers.Wallet.fromMnemonic(process.env.MNEMONIC_ACCT1 ?? "");

    const signer = wallet.connect(provider);
    console.log(`Connected to the wallet ${signer.address}`);
    console.log(`Calling contract ${contractAddress}`);

    const projectRegistryContract = await ethers.getContractAt('ProjectRegistry', contractAddress, signer);

    const tx = await projectRegistryContract.addProjectOwner(projectId, newOwner);
    await tx.wait();
    console.log(`Done! Transaction hash: ${tx.hash}`)
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});

```

### Remove Project Owner

To update the project metadata, use the updateProjectMetadata(uint256 projectId,address prevOwner,address owner) function.

```typescript
import { ethers } from "hardhat";
import * as dotenv from 'dotenv';
dotenv.config()


async function main() {
    console.log("Testing Project Registry");
    const projectId = 159;
    const prevOwner = "address to remove";
    const owner = "address to add";
    const contractAddress = "0x832c5391dc7931312CbdBc1046669c9c3A4A28d5";
    
    const provider = ethers.getDefaultProvider("goerli", {alchemy: process.env.ALCHEMY_API_KEY});
    const wallet = ethers.Wallet.fromMnemonic(process.env.MNEMONIC_ACCT1 ?? "");

    const signer = wallet.connect(provider);
    console.log(`Connected to the wallet ${signer.address}`);

    const projectRegistryContract = await ethers.getContractAt('ProjectRegistry', contractAddress, signer);

    const tx = await projectRegistryContract.removeProjectOwner(projectId, prevOwner, owner);
    await tx.wait();
    console.log(`Done! Transaction hash: ${tx.hash}`)
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});

```
