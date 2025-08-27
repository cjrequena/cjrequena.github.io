---
layout: post
title: "[BITCOIN] Dissecting Bitcoin Block"
author: "Carlos Requena"
lang: en
ref: dissecting-bitcoin-block
tags:
   -  "Bitcoin"
---

<div style="text-align:center">
    <span style="color:red;font-weight: bold">"If you want to find the secrets of the universe, think in terms of energy, frequency and vibration.” </span> 
    <span style="color:black;font-weight: bold">--Nikola Tesla</span>
</div>
<br>

## Block Header

At the top of every Bitcoin block is an **80-byte (160 hex character) block header** that summarizes all the data in the block. This header is crucial for:

- Block identification and validation
- Creating the block hash
- Maintaining blockchain integrity through cryptographic linking

> Miners repeatedly hash this block header to try and get a result below the current target. Successfully finding such a hash allows the block to be added to the blockchain, a process known as **mining**.
> 

### Block Header Structure

The 80-byte header contains six key fields:

| Field               | Size (bytes) | Description                                      | Endianness                     |
| ------------------- | ------------ | ------------------------------------------------ | ------------------------------ |
| Version             | 4            | Block version number indicating validation rules | [Little-endian](https://learnmeabitcoin.com/technical/general/little-endian/)                 |
| Previous Block Hash | 32           | SHA-256 hash of the preceding block's header     | natural byte order |
| Merkle Root         | 32           | Hash representing all transactions in the block  | natural byte order |
| Timestamp           | 4            | Unix epoch time when mining began                | [Little-endian](https://learnmeabitcoin.com/technical/general/little-endian/)                 |
| Bits (nBits)        | 4            | Compact representation of the difficulty target  | [Little-endian](https://learnmeabitcoin.com/technical/general/little-endian/)                 |
| Nonce               | 4            | Counter used in mining to find valid hashes      | [Little-endian](https://learnmeabitcoin.com/technical/general/little-endian/)                |

### Retrieving Block Header Data

To get the block header for block height 1 using Blockchain.com's API:

```bash
curl 'https://blockchain.info/rawblock/1?format=hex' | head -c 160 > block-1.txt
```

```bash
010000006fe28c0ab6f1b372c1a6a246ae63f74f931e8365e15a089c68d6190000000000982051fd1e4ba744bbbe680e1fee14677ba1a3c3540bf7b1cdb606e857233e0e61bc6649ffff001d01e36299
```

### Decoding the Block Header

Here's a Python function to decode a block header from hex format:

```python
import struct
import binascii
import datetime

def decode_block_header(hex_header):
    header_bytes = bytes.fromhex(hex_header)
    
    version = struct.unpack('<I', header_bytes[0:4])[0]
    prev_block = header_bytes[4:36][::-1].hex()
    merkle_root = header_bytes[36:68][::-1].hex()
    timestamp = struct.unpack('<I', header_bytes[68:72])[0]
    bits = struct.unpack('<I', header_bytes[72:76])[0]
    nonce = struct.unpack('<I', header_bytes[76:80])[0]

    return {
        'version': version,
        'previous_block_hash': prev_block,
        'merkle_root': merkle_root,
        'timestamp': f"{timestamp} ({datetime.fromtimestamp(timestamp, UTC).strftime('%Y-%m-%d %H:%M:%S %Z')})",
        'bits': bits,
        'nonce': nonce
    }

#-----------------------------------------------------------------------------------------------------------
def main():
    # Example usage
    hex_header = '010000006fe28c0ab6f1b372c1a6a246ae63f74f931e8365e15a089c68d6190000000000982051fd1e4ba744bbbe680e1fee14677ba1a3c3540bf7b1cdb606e857233e0e61bc6649ffff001d01e36299'
    decoded = decode_block_header(hex_header)

    for key, value in decoded.items():
        print(f"{key}: {value}")

#-----------------------------------------------------------------------------------------------------------
if __name__ == "__main__":
    main()
```

**Example Output**

```yaml
version: 1
previous_block_hash: 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
merkle_root: 0e3e2357e806b6cdb1f70b54c3a3a17b6714ee1f0e68bebb44a74b1efd512098
timestamp: 1231469665 (2009-01-09 02:54:25)
bits: 486604799
nonce: 2573394689
```

### Key Notes

- All integer fields are stored in **little-endian** format
- Hash fields (Previous Block Hash and Merkle Root) need byte reversal for proper display
- The timestamp represents when mining began, not necessarily when the block was found

## Block Body

The **block body** contains the transactions that the block commits to. It immediately follows the 80-byte block header.

The block body (unlike the header) is variable length and encodes all spending activity included by miners in that block.

The block body is where all the actual bitcoin transfers happen - the header just contains metadata and proof-of-work, while the body contains the economic activity.

### Block Body Structure

| Field             | Example          | Size     | Format                                                                                                                                                            | Description                                                                     |
| ----------------- | ---------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Tx Count          | 01               | variable | [Compact Size](https://learnmeabitcoin.com/technical/general/compact-size/ "Compact Size \| CompactSize Unsigned Integers")                                       | Indicates the number of transactions.                                           |
| Version           | 02000000         | 4 bytes  | [Little Endian](https://learnmeabitcoin.com/technical/general/little-endian/ "Little-Endian \| A Backwards Byte Order used in Bitcoin")                           | The version number for the transaction. Used to enable new features.            |
| Marker            | 00               | 1 byte   |                                                                                                                                                                   | Used to indicate a segwit transaction. Must be 00.                              |
| Flag              | 01               | 1 byte   |                                                                                                                                                                   | Used to indicate a segwit transaction. Must be 01 or greater.                   |
| Input Count       | 01               | variable | [Compact Size](https://learnmeabitcoin.com/technical/general/compact-size/ "Compact Size \| CompactSize Unsigned Integers")                                       | Indicates the number of inputs.                                                 |
| TXID              | \[TX1D]          | 32 bytes | [Natural Byte Order](https://learnmeabitcoin.com/technical/general/byte-order/#natural-byte-order "Byte Order \| Transaction Hashes and Block Hashes in Bitcoin") | The TXID of the transaction containing the output you want to spend.            |
| VOUT              | 01000000         | 4 bytes  | [Little Endian](https://learnmeabitcoin.com/technical/general/little-endian/ "Little-Endian \| A Backwards Byte Order used in Bitcoin")                           | The index number of the output you want to spend.                               |
| ScriptSig Size    | 6b               | variable | [Compact Size](https://learnmeabitcoin.com/technical/general/compact-size/ "Compact Size \| CompactSize Unsigned Integers")                                       | The size in bytes of the upcoming ScriptSig.                                    |
| ScriptSig         | \[script]        | variable | [Script](https://learnmeabitcoin.com/technical/script/ "Bitcoin Script \| A Mini Programming Language")                                                           | The unlocking code for the output you want to spend.                            |
| Sequence          | fdfffff          | 4 bytes  | [Little Endian](https://learnmeabitcoin.com/technical/general/little-endian/ "Little-Endian \| A Backwards Byte Order used in Bitcoin")                           | Set whether the transaction can be replaced or when it can be mined.            |
| Output Count      | 02               | variable | [Compact Size](https://learnmeabitcoin.com/technical/general/compact-size/ "Compact Size \| CompactSize Unsigned Integers")                                       | Indicates the number of outputs.                                                |
| Amount            | e99e060000000000 | 8 bytes  | [Little Endian](https://learnmeabitcoin.com/technical/general/little-endian/ "Little-Endian \| A Backwards Byte Order used in Bitcoin")                           | The value of the output in satoshis.                                            |
| ScriptPubKey Size | 19               | variable | [Compact Size](https://learnmeabitcoin.com/technical/general/compact-size/ "Compact Size \| CompactSize Unsigned Integers")                                       | The size in bytes of the upcoming ScriptPubKey.                                 |
| ScriptPubKey      | \[script]        | variable | [Script](https://learnmeabitcoin.com/technical/script/ "Bitcoin Script \| A Mini Programming Language")                                                           | The locking code for this output.                                               |
| Witness Items     | 02               | variable | [Compact Size](https://learnmeabitcoin.com/technical/general/compact-size/ "Compact Size \| CompactSize Unsigned Integers")                                       | The number of items to be pushed on to the stack as part of the unlocking code. |
| Size              | 47               | variable | [Compact Size](https://learnmeabitcoin.com/technical/general/compact-size/ "Compact Size \| CompactSize Unsigned Integers")                                       | The size of the upcoming stack item.                                            |
| Item              | 304...b01        | variable | Bytes                                                                                                                                                             | The data to be pushed on to the stack.                                          |
| Locktime          | 00000000         | 4 bytes  | [Little Endian](https://learnmeabitcoin.com/technical/general/little-endian/ "Little-Endian \| A Backwards Byte Order used in Bitcoin")                           | Set a time or height after which the transaction can be mined.                  |

### Transaction Serialization (legacy)

```
[version: 4 bytes LE]
[input_count: varint]
  for each input:
    [prev_txid: 32 bytes (little-endian in storage)]
    [prev_index: 4 bytes LE]
    [scriptSig_length: varint]
    [scriptSig: scriptSig_length bytes]
    [sequence: 4 bytes LE]
[output_count: varint]
  for each output:
    [value: 8 bytes LE]        # satoshis
    [pk_script_length: varint]
    [pk_script: pk_script_length bytes]
[locktime: 4 bytes LE]

```

> Notes&#x20;
>
> - `prev_txid` is stored in byte-order used within the block (usually little-endian in raw bytes); when displayed by explorers the bytes are reversed to big-endian hex.
> - `value` is an unsigned 8-byte little-endian integer of satoshis.
> - `sequence` historically used for RBF / timelocks; typically `0xffffffff`.

### Transaction Serialization (SegWit - BIP141)

```
[version: 4 bytes LE]
[marker: 1 byte == 0x00]
[flag:   1 byte == 0x01]
[input_count: varint]
  for each input: (same fields as legacy *but* scriptSig is usually small or empty)
[output_count: varint]
  for each output: (same as legacy)
[witnesses for each input]
[locktime: 4 bytes LE]

```

> Notes&#x20;
>
> - SegWit transactions add a 2-byte header after `version`
> - for each stack element: `[witness_item_len: varint] [witness_item: bytes]`
> - The presence of SegWit is detected when `marker == 0x00` and `flag != 0x00` immediately after the version.

### Retrieving Block Body Data

To get the block body for block height 1 using Blockchain.com's API:

```bash
curl 'https://blockchain.info/rawblock/1?format=hex' | tail -c +161 > block-1-body.txt
```

```bash
0101000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0704ffff001d0104ffffffff0100f2052a0100000043410496b538e853519c726a2c91e61ec11600ae1390813a627c66fb8be7947be63c52da7589379515d4e0a604f8141781e62294721166bf621e73a82cbf2342c858eeac00000000
```

## Decoding the genesis block.

```python
import json
import struct
import requests
from typing import TypedDict


class BlockHeader(TypedDict):
    """Type definition for decoded block header data."""

    version: int
    previous_block_hash: str
    merkle_root: str
    timestamp: int
    bits: int
    nonce: int


class TransactionInput(TypedDict):
    """Type definition for decoded block tx input data."""

    previous_tx_hash: str
    previous_output_index: int
    script_sig: str
    script_sig_length: str
    sequence: str
    is_coinbase: bool


class TransactionOutput(TypedDict):
    """Type definition for decoded block transaction output data."""

    value_satoshi: int
    value_btc: float
    script_pubkey: str
    script_pubkey_length: int


class WitnessItem(TypedDict):
    """Type definition for a single witness item."""
    data: str
    length: int
    type: str
    description: str


class TransactionWitness(TypedDict):
    """Type definition for witness data of a single input."""
    input_index: int
    witness_count: int
    witness_items: list[WitnessItem]
    witness_type: str
    total_witness_size: int


class Transaction(TypedDict):
    version: int
    tx_type: str
    has_witness: bool
    tx_input_count: int
    tx_inputs: list[TransactionInput]
    tx_output_count: int
    tx_outputs: list[TransactionOutput]
    lock_time: int
    size_bytes: int
    total_output_value_satoshi: int
    total_output_value_btc: float
    tx_witnesses: list[TransactionWitness]


class BlockDecoder:
    """
    A comprehensive Bitcoin block decoder that handles both Legacy and SegWit transactions.
    Provides methods to decode block headers, block bodies, and complete blocks.
    """

    def __init__(self):
        """Initialize the Bitcoin block decoder."""
        pass

    # --------------------------------------------------------------------------------------------
    def decode_full_block(self, block):
        """
        """
        try:
            # First 80 bytes (160 hex characters) are the header
            if len(block) < 160:
                raise ValueError("Block data too short to contain a valid header")

            header = block[:160]
            body = block[160:]

            decoded_header = self.decode_block_header(header)
            decoded_body = self.decode_block_body(body)

            return {
                'block_hash': self.calculate_block_hash(header),
                'header': decoded_header,
                'body': decoded_body,
                'total_block_size_bytes': len(block) // 2,
                'header_size_bytes': 80,
                'body_size_bytes': len(body) // 2
            }

        except Exception as ex:
            raise ValueError({
                'error': f"Failed to decode full block: {str(ex)}",
                'raw_hex_length': len(block)
            })

    # --------------------------------------------------------------------------------------------
    @staticmethod
    def decode_block_header(header: str) -> BlockHeader:
        """

        """
        # Validate input
        match header:
            case str() if header.strip():
                header = header.strip().lower()
            case _:
                raise ValueError("Header must be a non-empty string")

        # Validate hex string length (80 bytes = 160 hex characters)
        if len(header) != 160:
            raise ValueError(f"Header must be exactly 160 hex characters, got {len(header)}")

        # Validate hex string format and convert to bytes
        try:
            header_bytes = bytes.fromhex(header)
        except ValueError as e:
            raise ValueError(f"Invalid hex string: {e}") from e

        try:
            (version,) = struct.unpack("<I", header_bytes[0:4])
            previous_block_hash = header_bytes[4:36][::-1].hex()
            merkle_root = header_bytes[36:68][::-1].hex()
            (timestamp,) = struct.unpack("<I", header_bytes[68:72])
            (bits,) = struct.unpack("<I", header_bytes[72:76])
            (nonce,) = struct.unpack("<I", header_bytes[76:80])
        except ValueError as e:
            raise struct.error(f"Failed to unpack header data: {e}") from e

        block_header: BlockHeader = {
            "version": version,
            "previous_block_hash": previous_block_hash,
            "merkle_root": merkle_root,
            "timestamp": timestamp,
            "bits": bits,
            "nonce": nonce,
        }
        return block_header

    # --------------------------------------------------------------------------------------------
    @staticmethod
    def calculate_block_hash(header) -> str:
        """
        Calculate the block hash from the header.

        Args:
            header (str): Block header as hex string

        Returns:
            str: Block hash (double SHA-256, reversed)
        """
        try:
            import hashlib
            header_bytes = bytes.fromhex(header)
            hash1 = hashlib.sha256(header_bytes).digest()
            hash2 = hashlib.sha256(hash1).digest()
            return hash2[::-1].hex()  # Reverse byte order
        except Exception as ex:
            raise ValueError(f"Error calculating hash: {str(ex)}")

    # --------------------------------------------------------------------------------------------
    def decode_block_body(self, body: str):
        """
        """

        # Validate data
        match body:
            case str() if body.strip():
                body = body.strip().lower()
            case _:
                raise ValueError("Block body data must be a non-empty string")

        # Validate hex string format and convert to bytes
        try:
            body_bytes: bytes = bytes.fromhex(body)
        except ValueError as ex:
            raise ValueError(f"Invalid hex string: {ex}") from ex

        try:
            offset: int = 0
            transactions = []
            total_inputs = 0
            total_outputs = 0
            total_value_satoshi = 0
            has_segwit_txs = False
            coinbase_count = 0

            # Transaction count (varint)
            tx_count, offset = self.decode_varint(body_bytes, offset)

            # Extract and retrieve the list of raw hex txs.
            raw_txs: list[str] = self.extract_raw_txs(body_bytes.hex())

            for raw_tx in raw_txs:
                try:
                    tx: Transaction = self.decode_raw_tx(raw_tx)
                    transactions.append(tx)
                    total_inputs += tx['tx_input_count']
                    total_outputs += tx['tx_output_count']
                    total_value_satoshi += tx['total_output_value_satoshi']

                    # Check if any transaction is SegWit
                    if tx['has_witness']:
                        has_segwit_txs = True

                    # Count coinbase transactions
                    if any(inp['is_coinbase'] for inp in tx['tx_inputs']):
                        coinbase_count += 1

                except Exception as ex:
                    raise Exception(f"Error decoding raw_tx {raw_tx}: {ex}") from ex

            return {
                'transaction_count': tx_count,
                'transactions_decoded': len([tx for tx in transactions if 'error' not in tx]),
                'transactions_failed': len([tx for tx in transactions if 'error' in tx]),
                'block_type': 'SegWit Block' if has_segwit_txs else 'Legacy Block',
                'has_segwit_transactions': has_segwit_txs,
                'coinbase_transactions': coinbase_count,
                'total_inputs': total_inputs,
                'total_outputs': total_outputs,
                'total_output_value_satoshis': total_value_satoshi,
                'total_output_value_btc': total_value_satoshi / 100000000.0,
                'block_body_size_bytes': len(body_bytes),
                'transactions': transactions
            }

        except Exception as ex:
            raise Exception(f"Error decoding block body {ex}") from ex

    # -------------------------------------------------------------------------------------------------
    def decode_raw_tx(self, tx: str) -> Transaction:
        """
        """

        # Validate data
        match tx:
            case str() if tx.strip():
                tx = tx.strip().lower()
            case _:
                raise ValueError("Transaction data must be a non-empty string")

        # Validate hex string format and convert to bytes
        try:
            tx_bytes: bytes = bytes.fromhex(tx)
        except ValueError as e:
            raise ValueError(f"Invalid hex string: {e}") from e

        try:
            offset: int = 0

            # Version (4 bytes)
            version: int = struct.unpack('<I', tx_bytes[offset:offset + 4])[0]
            offset += 4

            # Check for SegWit marker and flag
            has_witness = False
            if offset + 1 < len(tx_bytes) and tx_bytes[offset] == 0x00 and tx_bytes[offset + 1] == 0x01:
                has_witness = True
                offset += 2  # Skip marker (0x00) and flag (0x01)

            # Decode inputs
            tx_inputs: list[TransactionInput] = []
            # Input count (varint)
            tx_input_count, offset = self.decode_varint(tx_bytes, offset)
            for _ in range(tx_input_count):
                tx_inputs, offset = self.decode_transaction_inputs_from_raw_tx(tx_bytes.hex())

            # Decode outputs
            tx_outputs: list[TransactionOutput] = []
            # Output count (varint)
            tx_output_count, offset = self.decode_varint(tx_bytes, offset)
            for _ in range(tx_output_count):
                tx_outputs, offset = self.decode_transaction_outputs_from_raw_tx(tx_bytes.hex())

            # Decode witnesses
            tx_witnesses: list[TransactionWitness] = []
            if has_witness:
                # Witness count (varint)
                tx_witness_count, offset = self.decode_varint(tx_bytes, offset)
                for _ in range(tx_witness_count):
                    tx_witnesses, offset = self.decode_transaction_witnesses_from_raw_tx(tx_bytes.hex())

            # Lock time (4 bytes)
            lock_time = struct.unpack('<I', tx_bytes[offset:offset + 4])[0]
            offset += 4

            # Calculate total output value
            total_output_value = sum(tx_output['value_satoshi'] for tx_output in tx_outputs)

            decoded_tx: Transaction = {
                'version': version,
                'tx_type': 'SegWit' if has_witness else 'Legacy',
                'has_witness': has_witness,
                'tx_input_count': tx_input_count,
                'tx_inputs': tx_inputs,
                'tx_output_count': tx_output_count,
                'tx_outputs': tx_outputs,
                'lock_time': lock_time,
                'size_bytes': offset,
                'total_output_value_satoshi': total_output_value,
                'total_output_value_btc': total_output_value / 100000000.0,
                'tx_witnesses': tx_witnesses
            }

        except ValueError as e:
            raise struct.error(f"Failed to unpack transaction data: {e}") from e

        return decoded_tx

    # --------------------------------------------------------------------------------------------
    def extract_raw_txs(self, body: str, offset: int = 0) -> list[str]:
        """
        """

        # Validate data
        match body:
            case str() if body.strip():
                body = body.strip().lower()
            case _:
                raise ValueError("Block body data must be a non-empty string")

        # Validate hex string format and convert to bytes
        try:
            body_bytes: bytes = bytes.fromhex(body)
        except ValueError as ex:
            raise ValueError(f"Invalid hex string: {ex}") from ex

        txs: list[str] = []

        try:
            # read transaction count
            tx_count, offset = self.decode_varint(body_bytes, offset)

            for _ in range(tx_count):
                start_offset = offset

                # --- parse just enough to find tx end ---
                version = body_bytes[offset:offset + 4]
                offset += 4

                has_witness = False
                if body_bytes[offset] == 0x00 and body_bytes[offset + 1] == 0x01:
                    has_witness = True
                    offset += 2

                # inputs
                tx_inputs_count, offset = self.decode_varint(body_bytes, offset)

                for _ in range(tx_inputs_count):
                    offset += 32  # prev txid
                    offset += 4  # index
                    script_len, offset = self.decode_varint(body_bytes, offset)
                    offset += script_len
                    offset += 4  # sequence

                # outputs
                tx_outputs_count, offset = self.decode_varint(body_bytes, offset)
                for _ in range(tx_outputs_count):
                    offset += 8  # value
                    script_len, offset = self.decode_varint(body_bytes, offset)
                    offset += script_len

                # witnesses
                if has_witness:
                    for _ in range(tx_inputs_count):
                        n_stack, offset = self.decode_varint(body_bytes, offset)
                        for _ in range(n_stack):
                            item_len, offset = self.decode_varint(body_bytes, offset)
                            offset += item_len

                # locktime
                offset += 4

                # slice raw tx
                tx_end = offset
                txs.append(body_bytes[start_offset:tx_end].hex())
        except ValueError as ex:
            raise ValueError(f"Error extracting raw txs: {ex}") from ex

        return txs

    # --------------------------------------------------------------------------------------------
    def decode_transaction_inputs_from_raw_tx(self, tx: str, offset: int = 0) -> tuple[list[TransactionInput], int]:
        """
        """

        # Validate and convert input
        if not isinstance(tx, str) or not tx.strip():
            raise ValueError("Transaction must be a non-empty string")

        try:
            tx_bytes = bytes.fromhex(tx.strip())
        except ValueError as e:
            raise ValueError(f"Invalid hex string: {e}") from e

        if len(tx_bytes) < 10:  # Minimum transaction size
            raise ValueError("Transaction too short to be valid")

        try:
            # Skip version (4 bytes)
            offset += 4

            # Check for SegWit marker and flag
            has_witness = False
            if offset + 1 < len(tx_bytes) and tx_bytes[offset] == 0x00 and tx_bytes[offset + 1] == 0x01:
                has_witness = True
                offset += 2  # Skip marker (0x00) and flag (0x01)

            # Input count
            tx_inputs_count, offset = self.decode_varint(tx_bytes, offset)

            tx_inputs: list[TransactionInput] = []

            for _ in range(tx_inputs_count):
                prev_tx_hash = tx_bytes[offset:offset + 32][::-1].hex()
                offset += 32

                prev_output_index = struct.unpack("<I", tx_bytes[offset:offset + 4])[0]
                offset += 4

                script_len, offset = self.decode_varint(tx_bytes, offset)

                script_sig = tx_bytes[offset:offset + script_len].hex()
                offset += script_len

                sequence = struct.unpack("<I", tx_bytes[offset:offset + 4])[0]
                offset += 4

                is_coinbase = (prev_tx_hash == "00" * 32 and prev_output_index == 0xFFFFFFFF)

                tx_inputs.append({
                    "previous_tx_hash": prev_tx_hash,
                    "previous_output_index": prev_output_index,
                    "script_sig": script_sig,
                    "script_sig_length": hex(script_len),
                    "sequence": hex(sequence),
                    "is_coinbase": is_coinbase,
                })
        except (struct.error, IndexError) as e:
            raise ValueError(f"Failed to decode transaction: {e}") from e
        return tx_inputs, offset

    # -------------------------------------------------------------------------------------------------
    def decode_transaction_outputs_from_raw_tx(self, tx: str, offset: int = 0) -> tuple[list[TransactionOutput], int]:
        """
        """

        # Validate and convert input
        if not isinstance(tx, str) or not tx.strip():
            raise ValueError("Transaction must be a non-empty string")

        try:
            tx_bytes = bytes.fromhex(tx.strip())
        except ValueError as e:
            raise ValueError(f"Invalid hex string: {e}") from e

        if len(tx_bytes) < 10:  # Minimum transaction size
            raise ValueError("Transaction too short to be valid")

        try:
            # Skip version (4 bytes)
            offset += 4

            # Check for SegWit marker and flag
            has_witness = False
            if offset + 1 < len(tx_bytes) and tx_bytes[offset] == 0x00 and tx_bytes[offset + 1] == 0x01:
                has_witness = True
                offset += 2  # Skip marker (0x00) and flag (0x01)

            # Input count and skip inputs
            tx_inputs_count, offset = self.decode_varint(tx_bytes, offset)

            # Skip all inputs
            for _ in range(tx_inputs_count):
                # Skip previous tx hash (32 bytes) + output index (4 bytes)
                offset += 36

                # Skip script sig
                script_pubkey_length, offset = self.decode_varint(tx_bytes, offset)
                offset += script_pubkey_length

                # Skip sequence (4 bytes)
                offset += 4

            # Output count
            tx_outputs_count, offset = self.decode_varint(tx_bytes, offset)

            tx_outputs: list[TransactionOutput] = []

            for i in range(tx_outputs_count):
                if offset + 8 > len(tx_bytes):
                    raise ValueError(f"Insufficient data for output {i} value")

                # Value (8 bytes, little-endian)
                value_satoshi = struct.unpack("<Q", tx_bytes[offset:offset + 8])[0]
                offset += 8

                # Script length and script
                script_pubkey_length, offset = self.decode_varint(tx_bytes, offset)

                if offset + script_pubkey_length > len(tx_bytes):
                    raise ValueError(f"Insufficient data for output {i} script")

                script_pubkey = tx_bytes[offset:offset + script_pubkey_length].hex()
                offset += script_pubkey_length

                block_tx_output: TransactionOutput = {
                    "value_satoshi": value_satoshi,
                    "value_btc": value_satoshi / 100000000.0,
                    "script_pubkey": script_pubkey,
                    "script_pubkey_length": script_pubkey_length
                }

                tx_outputs.append(block_tx_output)

        except (struct.error, IndexError) as e:
            raise ValueError(f"Failed to decode transaction: {e}") from e

        return tx_outputs, offset

    def decode_transaction_witnesses_from_raw_tx(self, tx: str) -> tuple[list[TransactionWitness], int]:
        """
        """
        # Validate and convert input
        if not isinstance(tx, str) or not tx.strip():
            raise ValueError("Transaction must be a non-empty string")

        try:
            tx_bytes = bytes.fromhex(tx.strip())
        except ValueError as e:
            raise ValueError(f"Invalid hex string: {e}") from e

        if len(tx_bytes) < 10:  # Minimum transaction size
            raise ValueError("Transaction too short to be valid")

        offset = 0

        try:
            # Skip version (4 bytes)
            offset += 4

            # Check for SegWit marker + flag
            if not (offset + 1 < len(tx_bytes) and
                    tx_bytes[offset] == 0x00 and
                    tx_bytes[offset + 1] == 0x01):
                raise ValueError("Transaction is not SegWit format (no witness marker/flag)")

            offset += 2  # Skip marker and flag

            # Input count
            input_count, offset = self.decode_varint(tx_bytes, offset)

            # Skip all inputs
            for _ in range(input_count):
                # Skip previous tx hash (32 bytes) + output index (4 bytes)
                offset += 36

                # Skip script sig
                script_len, offset = self.decode_varint(tx_bytes, offset)
                offset += script_len

                # Skip sequence (4 bytes)
                offset += 4

            # Skip outputs
            output_count, offset = self.decode_varint(tx_bytes, offset)

            for _ in range(output_count):
                # Skip value (8 bytes)
                offset += 8

                # Skip script pubkey
                script_len, offset = self.decode_varint(tx_bytes, offset)
                offset += script_len

            # Now decode witness data
            witnesses: list[TransactionWitness] = []

            for input_index in range(input_count):
                # Number of witness items for this input
                witness_count, offset = self.decode_varint(tx_bytes, offset)

                witness_items: list[WitnessItem] = []
                total_witness_size = 0

                # Decode each witness item
                for item_index in range(witness_count):
                    # Length of this witness item
                    item_len, offset = self.decode_varint(tx_bytes, offset)

                    if offset + item_len > len(tx_bytes):
                        raise ValueError(f"Insufficient data for witness item {item_index} of input {input_index}")

                    # Extract witness item data
                    item_data = tx_bytes[offset:offset + item_len]
                    offset += item_len

                    # Analyze the witness item
                    witness_item = self.decode_witness_item(item_data, item_index, witness_count)
                    witness_items.append(witness_item)
                    total_witness_size += item_len

                # Determine witness type
                witness_type = self.determine_witness_type(witness_count, witness_items)

                witnesses.append(TransactionWitness(
                    input_index=input_index,
                    witness_count=witness_count,
                    witness_items=witness_items,
                    witness_type=witness_type,
                    total_witness_size=total_witness_size,
                ))

        except (struct.error, IndexError) as e:
            raise ValueError(f"Failed to decode transaction witnesses: {e}") from e

        return witnesses, offset

    # -------------------------------------------------------------------------------------------------
    def decode_witness_item(self, data: bytes, index: int, witness_count: int) -> WitnessItem:
        """
        """
        hex_data = data.hex()
        length = len(data)

        # Empty witness item
        if length == 0:
            return WitnessItem(
                data="",
                length=0,
                type="EMPTY",
                description="Empty witness item"
            )

        # For P2WPKH (2 items: signature + pubkey)
        if witness_count == 2:
            if index == 0:
                # First item should be signature (typically 70-72 bytes with DER encoding + sighash)
                if 70 <= length <= 73:
                    sighash_type = data[-1] if length > 0 else 0
                    sighash_desc = self.get_sighash_description(sighash_type)
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="SIGNATURE",
                        description=f"ECDSA signature ({length} bytes) - {sighash_desc}"
                    )
                else:
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="SIGNATURE",
                        description=f"ECDSA signature ({length} bytes) - non-standard length"
                    )
            elif index == 1:
                # Second item should be public key (33 bytes compressed or 65 bytes uncompressed)
                if length == 33:
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="PUBLIC_KEY",
                        description="Compressed public key (33 bytes)"
                    )
                elif length == 65:
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="PUBLIC_KEY",
                        description="Uncompressed public key (65 bytes)"
                    )
                else:
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="PUBLIC_KEY",
                        description=f"Public key ({length} bytes) - non-standard length"
                    )

        # For P2WSH (multisig or script)
        elif witness_count > 2:
            if index == 0:
                # First item is typically empty for multisig due to OP_CHECKMULTISIG bug
                if length == 0:
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="EMPTY",
                        description="Empty item (OP_CHECKMULTISIG dummy)"
                    )
            elif index == witness_count - 1:
                # Last item is typically the redeem script
                return WitnessItem(
                    data=hex_data,
                    length=length,
                    type="REDEEM_SCRIPT",
                    description=f"Witness script/redeem script ({length} bytes)"
                )
            else:
                # Middle items are typically signatures
                if 70 <= length <= 73:
                    sighash_type = data[-1] if length > 0 else 0
                    sighash_desc = self.get_sighash_description(sighash_type)
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="SIGNATURE",
                        description=f"ECDSA signature ({length} bytes) - {sighash_desc}"
                    )
                else:
                    return WitnessItem(
                        data=hex_data,
                        length=length,
                        type="SIGNATURE",
                        description=f"ECDSA signature ({length} bytes) - non-standard length"
                    )

        # Single witness item (unusual)
        elif witness_count == 1:
            return WitnessItem(
                data=hex_data,
                length=length,
                type="UNKNOWN",
                description=f"Single witness item ({length} bytes) - possibly custom script"
            )

        # Default case
        return WitnessItem(
            data=hex_data,
            length=length,
            type="UNKNOWN",
            description=f"Unknown witness data ({length} bytes)"
        )

    # -------------------------------------------------------------------------------------------------
    @staticmethod
    def determine_witness_type(witness_count: int, witness_items: list[WitnessItem], /) -> str:
        """
        Determine the overall witness type based on witness items.

        Args:
            witness_count: Number of witness items
            witness_items: List of analyzed witness items

        Returns:
            String describing the witness type
        """
        match witness_count:
            case 0:
                return "NO_WITNESS"
            case 1:
                return "CUSTOM_SCRIPT"
            case 2:
                # P2WPKH pattern: signature + pubkey
                if (len(witness_items) == 2 and
                        witness_items[0].get("type") == "SIGNATURE" and
                        witness_items[1].get("type") == "PUBLIC_KEY"):
                    return "P2WPKH"
                else:
                    return "UNKNOWN_2_ITEM"
            case count if count > 2:
                # P2WSH multisig pattern: empty + signatures + script
                if (witness_items and
                        witness_items[0].get("type") == "EMPTY" and
                        witness_items[-1].get("type") == "REDEEM_SCRIPT"):
                    sig_count = sum(1 for item in witness_items[1:-1]
                                    if item.get("type") == "SIGNATURE")
                    return f"P2WSH_MULTISIG_{sig_count}_OF_N"
                elif witness_items and witness_items[-1].get("type") == "REDEEM_SCRIPT":
                    return "P2WSH_CUSTOM_SCRIPT"
                else:
                    return f"COMPLEX_WITNESS_{count}_ITEMS"
            case _:
                return "UNKNOWN_WITNESS"

    # -------------------------------------------------------------------------------------------------
    @staticmethod
    def get_sighash_description(sighash_type: int, /) -> str:
        """
        Get description of sighash type.

        Args:
            sighash_type: Sighash type byte

        Returns:
            Human-readable description of sighash type
        """
        base_type = sighash_type & 0x1f
        anyonecanpay = bool(sighash_type & 0x80)

        match base_type:
            case 0x01:
                base_desc = "SIGHASH_ALL"
            case 0x02:
                base_desc = "SIGHASH_NONE"
            case 0x03:
                base_desc = "SIGHASH_SINGLE"
            case _:
                base_desc = f"SIGHASH_UNKNOWN({base_type:02x})"

        if anyonecanpay:
            return f"{base_desc} | SIGHASH_ANYONECANPAY"
        else:
            return base_desc

    # --------------------------------------------------------------------------------------------
    @staticmethod
    def decode_varint(data: bytes, offset: int) -> tuple[int, int]:
        """
        Decode a variable-length integer from the data starting at offset.

        Args:
            data (bytes): The byte data
            offset (int): Starting position

        Returns:
            tuple: (value, new_offset)
        """
        first_byte = data[offset]
        if first_byte < 0xFD:
            return first_byte, offset + 1
        elif first_byte == 0xFD:
            return struct.unpack("<H", data[offset + 1: offset + 3])[0], offset + 3
        elif first_byte == 0xFE:
            return struct.unpack("<I", data[offset + 1: offset + 5])[0], offset + 5
        elif first_byte == 0xFF:
            return struct.unpack("<Q", data[offset + 1: offset + 9])[0], offset + 9
        else:
            raise ValueError("Invalid varint prefix byte")


# -------------------------------------------------------------------------------------------------
def main():
    block_decoder: BlockDecoder = BlockDecoder()

    # set block height here
    block_height = 0  # change this to any block height you want

    url = f"https://blockchain.info/rawblock/{block_height}?format=hex"
    resp = requests.get(url)

    if resp.status_code == 200:
        block = resp.text.strip()  # raw block hex
        block_bytes = bytes.fromhex(block)
        decoded_block = block_decoder.decode_full_block(block_bytes.hex())
        print(json.dumps(decoded_block, indent=2))
    else:
        print("Error:", resp.status_code, resp.text)


if __name__ == "__main__":
    main()
```