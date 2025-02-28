# How to Deploy a Subnet with Multisig Authorization

Subnet creators can control critical Subnet operations with a N of M multisig. This multisig must be
setup at deployment time and can't be edited afterward. Multisigs can are available on both the Fuji
Testnet and Mainnet.

To setup your multisig, you need to know the P-Chain address of each key holder and what you
want your signing threshold to be.

:::note

Avalanche-CLI requires Ledgers for Mainnet deployments. This how-to guide assumes the use of Ledgers
for setting up your multisig.

:::

## Prerequisites

- [`Avalanche-CLI`](https://github.com/ava-labs/avalanche-cli) installed
- Familiarity with process of [Deploying a Subnet on Testnet](create-a-fuji-subnet) and [Deploying a
  Permissioned Subnet on Mainnet](create-a-mainnet-subnet)
- Multiple Ledger devices [configured for Avalanche](./create-a-mainnet-subnet#setting-up-your-ledger)
- A Subnet configuration ready to deploy to either Fuji Testnet or Mainnet

## Deploy a Subnet with a Multisig

When issuing the transactions to create the Subnet, you need to sign the TXs with multiple keys
from the multisig.

### Specify Network

Start the Subnet deployment with

```bash
avalanche subnet deploy testsubnet
```

First step is to specify `Fuji` or `Mainnet` as the network:

```text
Use the arrow keys to navigate: ↓ ↑ → ←
? Choose a network to deploy on:
    Local Network
    Fuji
  ▸ Mainnet
```

```text
Deploying [testsubnet] to Mainnet
```

Ledger is automatically recognized as the signature mechanism on `Mainnet`.

After that, the CLI shows the first `Mainnet` Ledger address.

```text
Ledger address: P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
```

### Set Control Keys

Next the CLI asks the user to specify the control keys. This is where you setup your multisig.

```text
Configure which addresses may make changes to the subnet.
These addresses are known as your control keys. You are going to also
set how many control keys are required to make a subnet change (the threshold).
Use the arrow keys to navigate: ↓ ↑ → ←
? How would you like to set your control keys?:
  ▸ Use ledger address
    Custom list
```

Select `Custom list` and add every address that you'd like to be a key holder on the multisig.

```text
✔ Custom list
? Enter control keys:
  ▸ Add
    Delete
    Preview
    More Info
↓   Done
```

Use the given menu to add each key, and select `Done` when finished.

The output at this point should look something like:

```text
✔ Custom list
✔ Add
Enter P-Chain address (Example: P-...): P-avax1wryu62weky9qjlp40cpmnqf6ml2hytnagj5q28
✔ Add
Enter P-Chain address (Example: P-...): P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
✔ Add
Enter P-Chain address (Example: P-...): P-avax12gcy0xl0al6gcjrt0395xqlcuq078ml93wl5h8
✔ Add
Enter P-Chain address (Example: P-...): P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
✔ Add
Enter P-Chain address (Example: P-...): P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g
✔ Done
Your Subnet's control keys: [P-avax1wryu62weky9qjlp40cpmnqf6ml2hytnagj5q28 P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5 P-avax12gcy0xl0al6gcjrt0395xqlcuq078ml93wl5h8 P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g]
```

:::note

When deploying a Subnet with Ledger's, you must include the Ledger's default address determined in
[Specify Network](#specify-network) for the deployment to succeed. You may see an error like

```text
Error: wallet does not contain subnet auth keys
exit status 1
```

:::

### Set Threshold

Next, specify the threshold. In your N of M multisig, your threshold is N, and M is the number
of control keys you added in the previous step.

```text
Use the arrow keys to navigate: ↓ ↑ → ←
? Select required number of control key signatures to make a subnet change:
  ▸ 1
    2
    3
    4
    5
```

### Specify Control Keys to Sign the Chain Creation TX

You now need N of your key holders to sign the Subnet deployment transaction. You must select which
addresses you want to sign the TX.

```text
? Choose a subnet auth key:
  ▸ P-avax1wryu62weky9qjlp40cpmnqf6ml2hytnagj5q28
    P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
    P-avax12gcy0xl0al6gcjrt0395xqlcuq078ml93wl5h8
    P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
    P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g
```

A successful control key selection looks like:

```text
✔ 2
✔ P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
✔ P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
Your subnet auth keys for chain creation: [P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5 P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af]
*** Please sign subnet creation hash on the ledger device ***
```

#### Potential Errors

If the currently connected Ledger address isn't included in your TX signing group, the operation
fails with:

```text
✔ 2
✔ P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
✔ P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g
Your subnet auth keys for chain creation: [P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g]
Error: wallet does not contain subnet auth keys
exit status 1
```

This can happen either because the original specified control keys -previous step- don't contain the
Ledger address, or because the Ledger address control key wasn't selected in the current step.

If the user has the correct address but doesn't have sufficient balance to pay for the TX, the
operation fails with:

```text
✔ 2
✔ P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
✔ P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g
Your subnet auth keys for chain creation: [P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g]
*** Please sign subnet creation hash on the ledger device ***
Error: insufficient funds: provided UTXOs need 1000000000 more units of asset "rgNLkDPpANwqg3pHC4o9aGJmf2YU4GgTVUMRKAdnKodihkqgr"
exit status 1
```

### Sign Subnet Deployment TX with the First Address

The Subnet Deployment TX is ready for signing.

```text
*** Please sign subnet creation hash on the ledger device ***
```

This activates a `Please review` window on the Ledger. Navigate to the Ledger's `APPROVE` window by
using the Ledger's right button, and then authorize the request by pressing both left and right buttons.

```text
Subnet has been created with ID: 2qUKjvPx68Fgc1NMi8w4mtaBt5hStgBzPhsQrS1m7vSub2q9ew. Now creating blockchain...
*** Please sign blockchain creation hash on the ledger device ***
```

After successful Subnet creation, the CLI asks the user to sign the blockchain creation TX.

This activates a `Please review` window on the Ledger. Navigate to the Ledger's `APPROVE` window by
using the Ledger's right button, and then authorize the request by pressing both left and right buttons.

On success, the CLI provides Subnet deploy details. As only one address signed the chain creation
TX, the CLI writes a file to disk to save the TX to continue the signing process with another
command.

```text
+--------------------+----------------------------------------------------+
| DEPLOYMENT RESULTS |                                                    |
+--------------------+----------------------------------------------------+
| Chain Name         | testsubnet                                         |
+--------------------+----------------------------------------------------+
| Subnet ID          | 2qUKjvPx68Fgc1NMi8w4mtaBt5hStgBzPhsQrS1m7vSub2q9ew |
+--------------------+----------------------------------------------------+
| VM ID              | rW1esjm6gy4BtGvxKMpHB2M28MJGFNsqHRY9AmnchdcgeB3ii  |
+--------------------+----------------------------------------------------+

1 of 2 required Blockchain Creation signatures have been signed. Saving TX to disk to enable remaining signing.

Path to export partially signed TX to:
```

Enter the name of file to write to disk, such as `partiallySigned.txt`. This file shouldn't exist already.


```text
Path to export partially signed TX to: partiallySigned.txt

Addresses remaining to sign the tx
  P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af

Connect a ledger with one of the remaining addresses or choose a stored key and run the signing command, or send "partiallySigned.txt" to another user for signing.

Signing command:
  avalanche transaction sign testsubnet --input-tx-filepath partiallySigned.txt
```

## Gather Remaining Signatures and Issue the Subnet Deployment TX

So far, one address has signed the Subnet deployment TX, but you need N signatures. Your Subnet has
not been fully deployed yet. To get the remaining signatures, you may connect a different Ledger to
the same computer you've been working on. Alternatively, you may send the `partiallySigned.txt` file
to other users to sign themselves.

The remainder of this section assumes that you are working on a machine with access to both the
remaining keys and the `partiallySigned.txt` file.

### Issue the Command to Sign the Chain Creation TX

Avalanche-CLI can detect the deployment network automatically. For `Mainnet` TXs, it uses your
Ledger automatically. For `Fuji Testnet`, the CLI prompts the user to choose the signing mechanism.

You can start the signing process with the `transaction sign` command:

```bash
avalanche transaction sign testsubnet --input-tx-filepath partiallySigned.txt
```

```text
Ledger address: P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
*** Please sign TX hash on the ledger device ***
```

Next, the CLI starts a new signing process for the Subnet deployment TX. If the Ledger isn't the correct
one, the following error should appear instead:

```text
Ledger address: P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
Error: wallet does not contain subnet auth keys
exit status 1
```

This activates a `Please review` window on the Ledger. Navigate to the Ledger's `APPROVE` window by
using the Ledger's right button, and then authorize the request by pressing both left and right buttons.

Repeat this processes until all required parties have signed the TX. You should see a message
like this:

```text
All 2 required Tx signatures have been signed. Saving TX to disk to enable commit.

Overwritting partiallySigned.txt

Tx is fully signed, and ready to be committed

Commit command:
  avalanche transaction commit testsubnet --input-tx-filepath partiallySigned.txt
```

Now, `partiallySigned.txt` contains a fully signed TX.

### Commit the Subnet Deployment TX

To run submit the fully signed TX, run

```bash
avalanche transaction commit testsubnet --input-tx-filepath partiallySigned.txt
```

The CLI recognizes the deployment network automatically and submits the TX appropriately.

```text
+--------------------+-------------------------------------------------------------------------------------+
| DEPLOYMENT RESULTS |                                                                                     |
+--------------------+-------------------------------------------------------------------------------------+
| Chain Name         | testsubnet                                                                          |
+--------------------+-------------------------------------------------------------------------------------+
| Subnet ID          | 2qUKjvPx68Fgc1NMi8w4mtaBt5hStgBzPhsQrS1m7vSub2q9ew                                  |
+--------------------+-------------------------------------------------------------------------------------+
| VM ID              | rW1esjm6gy4BtGvxKMpHB2M28MJGFNsqHRY9AmnchdcgeB3ii                                   |
+--------------------+-------------------------------------------------------------------------------------+
| Blockchain ID      | 2fx9EF61C964cWBu55vcz9b7gH9LFBkPwoj49JTSHA6Soqqzoj                                  |
+--------------------+-------------------------------------------------------------------------------------+
| RPC URL            | http://127.0.0.1:9650/ext/bc/2fx9EF61C964cWBu55vcz9b7gH9LFBkPwoj49JTSHA6Soqqzoj/rpc |
+--------------------+-------------------------------------------------------------------------------------+
| P-Chain TXID       | 2fx9EF61C964cWBu55vcz9b7gH9LFBkPwoj49JTSHA6Soqqzoj                                  |
+--------------------+-------------------------------------------------------------------------------------+
```

Your Subnet successfully deployed with a multisig.

## Add Validators Using the Multisig

The `addValidator` command also requires use of the multisig. Before starting, make sure to connect,
unlock, and run the Avalanche Ledger app.

```bash
avalanche subnet addValidator testsubnet
```

### Select Network

First specify the network. Select either `Fuji` or `Mainnet`

```text
Use the arrow keys to navigate: ↓ ↑ → ←
? Choose a network to add validator to.:
  ▸ Fuji
    Mainnet
```

### Choose Signing Keys

Then, similar to the `deploy` command, the command asks the user to select the N control keys needed
to sign the TX.

```text
✔ Mainnet
Use the arrow keys to navigate: ↓ ↑ → ←
? Choose a subnet auth key:
  ▸ P-avax1wryu62weky9qjlp40cpmnqf6ml2hytnagj5q28
    P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
    P-avax12gcy0xl0al6gcjrt0395xqlcuq078ml93wl5h8
    P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
    P-avax1g4eryh40dtcsltmxn9zk925ny07gdq2xyjtf4g
````

```text
✔ Mainnet
✔ P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
✔ P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
Your subnet auth keys for add validator TX creation: [P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5 P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af].
```

### Finish Assembling the TX

Take a look at [Add a Validator](./create-a-mainnet-subnet#add-a-validator) for additional help
issuing this transaction.

:::note

If setting up a multisig, don't select your validator start time to be in one minute. Finishing the
signing process takes significantly longer when using a multisig.

:::


```text
Next, we need the NodeID of the validator you want to whitelist.

Check https://docs.avax.network/apis/avalanchego/apis/info#infogetnodeid for instructions about how to query the NodeID from your node
(Edit host IP address and port to match your deployment, if needed).
What is the NodeID of the validator you'd like to whitelist?: NodeID-7Xhw2mDxuDS44j42TCB6U5579esbSt3Lg
✔ Default (20)
When should your validator start validating?
If you validator is not ready by this time, subnet downtime can occur.
✔ Custom
When should the validator start validating? Enter a UTC datetime in 'YYYY-MM-DD HH:MM:SS' format: 2022-11-22 23:00:00
✔ Until primary network validator expires
NodeID: NodeID-7Xhw2mDxuDS44j42TCB6U5579esbSt3Lg
Network: Local Network
Start time: 2022-11-22 23:00:00
End time: 2023-11-22 15:57:27
Weight: 20
Inputs complete, issuing transaction to add the provided validator information...
```

```text
Ledger address: P-avax1kdzq569g2c9urm9887cmldlsa3w3jhxe0knfy5
*** Please sign add validator hash on the ledger device ***
```

After that, the command shows the connected Ledger's address, and asks the user to sign the TX with
the Ledger.

```text
Partial TX created

1 of 2 required Add Validator signatures have been signed. Saving TX to disk to enable remaining signing.

Path to export partially signed TX to:
```

Because you've setup a multisig, TX isn't fully signed, and the commands asks a file to write into. Use
something like `partialAddValidatorTx.txt`.

```text
Path to export partially signed TX to: partialAddValidatorTx.txt

Addresses remaining to sign the tx
  P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af

Connect a Ledger with one of the remaining addresses or choose a stored key and run the signing command, or send "partialAddValidatorTx.txt" to another user for signing.

Signing command:
  avalanche transaction sign testsubnet --input-tx-filepath partialAddValidatorTx.txt
```

## Sign and Commit the Add Validator TX

The process is very similar to signing of Subnet Deployment TX. So far, one address has signed the
TX, but you need N signatures. To get the remaining signatures, you may connect a different Ledger
to the same computer you've been working on. Alternatively, you may send the
`partialAddValidatorTx.txt` file to other users to sign themselves.

The remainder of this section assumes that you are working on a machine with access to both the
remaining keys and the `partialAddValidatorTx.txt` file.

### Issue the Command to Sign the Add Validator TX

Avalanche-CLI can detect the deployment network automatically. For `Mainnet` TXs, it uses your Ledger
automatically. For `Fuji Testnet`, the CLI prompts the user to choose the signing mechanism.

```bash
avalanche transaction sign testsubnet --input-tx-filepath partialAddValidatorTx.txt
```

```text
Ledger address: P-avax1g7nkguzg8yju8cq3ndzc9lql2yg69s9ejqa2af
*** Please sign TX hash on the ledger device ***
```

Next, the command is going to start a new signing process for the Add Validator TX.

This activates a `Please review` window on the Ledger. Navigate to the Ledger's `APPROVE` window by
using the Ledger's right button, and then authorize the request by pressing both left and right buttons.

Repeat this processes until all required parties have signed the TX. You should see a message like this:

```text

All 2 required Tx signatures have been signed. Saving TX to disk to enable commit.

Overwritting partialAddValidatorTx.txt

Tx is fully signed, and ready to be committed

Commit command:
  avalanche transaction commit testsubnet --input-tx-filepath partialAddValidatorTx.txt
```

Now, `partialAddValidatorTx.txt` contains a fully signed TX.

### Issue the Command to Commit the add validator TX

To run submit the fully signed TX, run

```bash
avalanche transaction commit testsubnet --input-tx-filepath partialAddValidatorTx.txt
```

The CLI recognizes the deployment network automatically and submits the TX appropriately.

```text
Transaction successful, transaction ID: K7XNSwcmgjYX7BEdtFB3hEwQc6YFKRq9g7hAUPhW4J5bjhEJG
```

You've successfully added the validator to the Subnet.

