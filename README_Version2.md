# Bitget Proof-of-Reserves (PoR) — Reference Repository

This repo is a full reference implementation for producing privacy-preserving Proof-of-Reserves:
- Build a Merkle tree from a liabilities dataset (with optional salting/hashing of account IDs)
- Export per-account Merkle proofs
- Verify proofs locally (Python) and on-chain (Solidity verifier)
- Produce custody address signed messages (Ethereum example)
- Package outputs for auditors (zip with root, sample proofs, custody signatures, and audit README)

Security reminders
- NEVER store private keys or seed phrases in the repo.
- Use hardware wallets or HSMs to sign custody address messages.
- Use salted/hashed account identifiers to improve privacy; share salts only with auditors under NDA.

Quick start (local)
1. Install Python deps:
   python -m pip install -r requirements.txt
2. Prepare a CSV in `data/accounts.csv` with columns: `account_id,liability[,salt]`
   - You can pre-hash account IDs (e.g., SHA256(account_id || ":" || nonce)) to improve privacy.
3. Build Merkle tree and export proofs:
   python3 src/merkle.py build --input data/accounts.csv --out build/
4. Verify a proof:
   python3 -c "from src.proof_utils import verify_proof; import json; print(verify_proof('leaf_hex', json.load(open('build/proof_0.json'))['proof'], json.load(open('build/merkle_tree.json'))['root'], 0))"
5. Produce custody signatures following `scripts/eth_sign.md`
6. Package deliverable:
   ./scripts/package_audit.sh build/

Repository layout
- src/: merkle builder, verifier utilities
- data/: example liabilities
- contracts/: on-chain verifier
- scripts/: packaging and signing instructions
- auditor/: guidance for auditors and expected deliverables
- .github/: CI workflow
- tests/: unit tests

If you want, I can:
- Walk through signing a message with your specific wallet (Trezor, Ledger, Geth, Electrum, etc.)
- Add a full auditor example (sample NDA flow, auditor script)
- Add an on-chain verification demo (Truffle/Hardhat tests)