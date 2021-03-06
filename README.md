# zkRelay

zkRelay facilitates a chain-relay from Bitcoin to Ethereum through zkSNARKS.

The implementation is based on [ZoKrates](https://github.com/Zokrates/ZoKrates) and performs off-chain Bitcoin header chain validations, while only the resulting proof is submitted to the target ledger.
The main branch of this repository includes an implementation that performs batch validations for 504 Bitcoin blocks.

The workflow of zkRelay is seperated into two steps, a one-time compilation and setup step and many-time validation.

As a prerequisite, [ZoKrates](https://github.com/Zokrates/ZoKrates) needs to be installed for both steps.

## Compilation and Setup

- First, the off-chain validation program has to be compiled:

  `zokrates compile --light -i validate.zok`

- Second, the proving and verfication keys are generated in the setup step:
  
  `zokrates setup --light`
  
- Third, a smart contract is generated that validates proofs:

  `zokrates export-verifier`
  
- Last, the zkRelay contract `batch_verifier.sol` has to be deployed using a tool of choice. It references the generated verification contract `verifier.sol` which has to be available during deployment.

## Off-chain validation

- As a prerequisite, a the current implementation presumes a local Bitcoin client at `127.0.0.1:8332`, the credentials have to be stored in `preprocessing/pw`. The script requires Python3.

- To validate a batch of 504, run the following script, where `n` corresponds to the block number of the first block in the batch: 

  `python preprocessing/batch.py n`
  
- The script retrieves the blocks from the Bitcoin client, formats them and uses ZoKrates to perform the off-chain validation. Thereafter it creates a proof for the given batch.

- The resulting witness and proof are stored in the `outputs` folder

- To retrieve a proof format that can be easily submitted, use ZoKrates' print command (the target proof has to be moved to the same directory and name `proof.json`):

  `zokrates print-proof --format [remix, json]`

