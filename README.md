---

# EVM Cheatsheet

## Table of Contents
1. [What is EVM?](#what-is-evm)
2. [EVM Architecture](#evm-architecture)
3. [EVM Bytecode and Opcodes](#evm-bytecode-and-opcodes)
4. [Memory vs. Storage](#memory-vs-storage)
5. [Gas and Gas Management](#gas-and-gas-management)
6. [Solidity and EVM](#solidity-and-evm)
7. [Debugging and Tools](#debugging-and-tools)
8. [Security Considerations](#security-considerations)
9. [EVM Execution Model](#evm-execution-model)
10. [Transaction Lifecycle](#transaction-lifecycle)
11. [Advanced EVM Concepts](#advanced-evm-concepts)
12. [Common Opcode Cheatsheet](#opcode-cheatsheet)
13. [Understanding EVM Contract Address Generation, Bytecode Decompilation, and Memory/Storage Management](#understanding-evm-contract-address-generation-bytecode-decompilation-and-memorystorage-management)
---

## What is EVM?
The Ethereum Virtual Machine (EVM) is a state machine responsible for executing smart contracts on the Ethereum blockchain. It acts as a decentralized computer, maintaining a global state and executing program instructions defined in smart contracts.

```
+---------------------+
| Ethereum Blockchain |
+---------------------+
         |
         v
+---------------------+
| Ethereum Virtual    |
| Machine (EVM)       |
+---------------------+
         |
         v
+---------------------+
| Executes Smart      |
| Contracts           |
+---------------------+
```

### Key Features:
- **State Machine:** Executes transactions and changes the blockchain state.
- **Deterministic Execution:** Given the same input, it will always produce the same output.
- **Isolated Execution:** Executes code in a sandboxed environment.

---

## EVM Architecture
The EVM is a stack-based architecture with a 256-bit word size. It uses various components like stack, memory, storage, and an instruction set for smart contract execution.

```
+-----------------------+
|       EVM Stack       |
|  (LIFO structure,     |
|   1024 slots max)     |
+-----------------------+
         |
         v
+-----------------------+
|       Memory          |
|  (Temporary, volatile)|
|  for computations     |
+-----------------------+
         |
         v
+-----------------------+
|      Storage          |
| (Persistent, costly)  |
|   for state variables |
+-----------------------+
```

### Explanation:
- **Stack:** A Last-In-First-Out (LIFO) structure for holding intermediate values during execution. Maximum depth is 1024.
- **Memory:** Volatile, linear memory available during contract execution. It is cheaper to access but resets after each transaction.
- **Storage:** Persistent state associated with a smart contract. More expensive to use but retains data across transactions.

---

## EVM Bytecode and Opcodes
The EVM executes bytecode, which is a low-level representation of smart contracts compiled from high-level languages like Solidity. Bytecode is composed of various instructions called opcodes.

### Example: Solidity to Bytecode Conversion
```solidity
// Solidity code
uint x = 2 + 3;
```
Compiles to EVM bytecode:
```
PUSH1 0x02    // Push value 2 onto the stack
PUSH1 0x03    // Push value 3 onto the stack
ADD           // Add the two values
```

### Common Opcodes
```
+-----------+--------------------------+-------------+
| Opcode    | Description              | Gas Cost    |
+-----------+--------------------------+-------------+
| PUSH1     | Push a byte onto stack   | 3 gas       |
| POP       | Remove the top item      | 2 gas       |
| ADD       | Add top two stack values | 3 gas       |
| MSTORE    | Store a value in memory  | 3 gas       |
| JUMP      | Jump to a program point  | 8 gas       |
+-----------+--------------------------+-------------+
```

### Bytecode to Human Readable Format
Opcodes correspond to specific byte values, which can be challenging to interpret directly. A disassembler can convert bytecode back to a more readable format.

---

## Memory vs. Storage
Memory and storage are used differently within the EVM:

```
+-----------------------+       +------------------------+
|       Memory          |       |       Storage          |
|  - Temporary, volatile|       |  - Persistent, costly  |
|  - Cheaper to use     |       |  - High gas cost       |
|  - Resets per txn     |       |  - Persists across txns|
+-----------------------+       +------------------------+
```

### Key Differences:
- **Memory:** Used for temporary calculations and does not persist after the transaction ends.
- **Storage:** Used for contract state variables and is persistent, but accessing and modifying it incurs a higher gas cost.

### Use Cases:
- **Memory:** Suitable for temporary data structures like arrays.
- **Storage:** Ideal for variables that need to persist across multiple transactions.

---

## Gas and Gas Management
Gas is the internal pricing mechanism used to mitigate spam and allocate resources on the Ethereum network. Each EVM instruction requires a certain amount of gas, reflecting the computational effort needed to execute it.

### Gas Flow
```
+------------------------------+
|      Gas Limit (Max allowed) |
+------------------------------+
            |
            v
+------------------------------+
|       Gas Used (Actual cost) |
+------------------------------+
            |
            v
+------------------------------+
|   Gas Refund (Unused Gas)    |
+------------------------------+
```

### Common Gas Costs
- **Arithmetic operations (ADD, SUB):** 3 gas
- **Storage operations (SSTORE):** 20,000 gas (new value), 5,000 gas (update)
- **Calls (CALL, DELEGATECALL):** 700 gas

### Optimization Tips
- **Minimize Storage Writes:** Storage access is expensive; try using memory wherever possible.
- **Efficient Looping:** Avoid unnecessary iterations; use optimized algorithms.

---

## Solidity and EVM
Solidity is a high-level programming language that compiles down to EVM bytecode. Understanding how Solidity interacts with the EVM can help optimize smart contract execution.

### Function Visibility
```
public    --> Accessible from outside
external  --> Only called externally
internal  --> Only called internally
private   --> Only within the same contract
```

### Low-Level Calls in Solidity
- **call:** General-purpose low-level call.
- **delegatecall:** Executes in the context of the calling contract.
- **staticcall:** Calls a contract without modifying the state.

---

## Debugging and Tools
When developing for the EVM, it's crucial to use debugging tools and techniques to understand and resolve issues.

### Common Tools
- **Remix:** Browser-based IDE for Solidity development.
- **Hardhat:** Development environment that provides EVM-related utilities.
- **Ganache:** Personal blockchain for Ethereum development, allows for local testing.

### Debugging Techniques
- **Events:** Emit events for key state changes or results.
- **Revert/Error Messages:** Use `require`, `assert`, and custom error messages for debugging.

---

## Security Considerations
Security is a critical aspect of smart contract development due to the immutability of the blockchain.

### Common Vulnerabilities
1. **Reentrancy:** External calls can re-enter a contract function.
2. **Integer Overflow/Underflow:** Arithmetic operations exceed data type limits.
3. **Access Control Issues:** Incorrect use of permissions or modifiers.
4. **Unchecked Delegatecall:** Can lead to code execution vulnerabilities.

### Best Practices
- **Use SafeMath Libraries:** Prevent overflow/underflow.
- **Implement Reentrancy Guards:** Avoid multiple external calls.
- **Follow Coding Standards:** Use established patterns for common tasks.

---

## EVM Execution Model
The EVM executes transactions and smart contracts in a sequential order using a stack-based approach. Each instruction consumes gas and may affect the state or perform computational tasks.

### Execution Flow
1. **Fetch Instruction:** Get the current opcode.
2. **Decode:** Interpret the opcode.
3. **Execute:** Perform the corresponding action (e.g., PUSH, POP, ADD).
4. **Update State:** Modify stack, memory, or storage if needed.
5. **Advance Program Counter:** Move to the next instruction.

---

## Transaction Lifecycle
The lifecycle of a transaction involves several stages:

```
1. User Initiates Transaction
   └─> From address, to address, gas limit, data, etc.
2. Validate Transaction
   └─> Signature and balance checks
3. Execute Transaction
   └─> Run bytecode and modify state
4. Update State
   └─> Persist changes to storage
5. Produce Receipt
   └─> Result of execution (success/failure)
```

---

## Advanced EVM Concepts
### Gas Refunds
When some operations free up previously used resources (e.g., self-destruct), a gas refund is provided.

### Self-Destruct
Contracts can delete themselves and free up storage, earning gas refunds.

### Precompiled Contracts
EVM includes built-in contracts (e.g., hashing functions) that are more efficient than regular bytecode.

---

## Opcode Cheatsheet
```
+-----------+-------------------------------+-------------+
| Opcode    | Description                   | Gas Cost    |
+-----------+-------------------------------+-------------+
| PUSH1     | Push a single byte to stack   | 3 gas       |
| POP       | Remove the top stack element  | 2 gas       |
| DUP1      | Duplicate the top stack item  | 3 gas       |
| SWAP1     | Swap the top two stack items  | 3 gas       |
| ADD       | Add top two stack elements    | 3 gas       |
| MUL       | Multiply top two stack items  | 5 gas       |
| DIV       | Integer division              | 5 gas       |
| SUB       | Subtract top two stack items  | 3 gas       |
| SLOAD     | Read from storage             | 2100 gas    |
| SSTORE    | Write to storage              | 20,000 gas  |
+-----------+-------------------------------+-------------+
```
---


## Understanding EVM Contract Address Generation, Bytecode Decompilation, and Memory/Storage Management

Let's dive deeper into how the EVM works with complex contracts, covering how contract addresses are generated, the decompilation of bytecode, and memory and storage management in the context of more intricate contract interactions.

### 1. Contract Address Generation

When a new contract is deployed to the Ethereum blockchain, the contract's address is determined using the following formula:

**`address = keccak256(rlp.encode([sender, nonce]))[12:]`**

Where:
- **`sender`**: The address of the account deploying the contract.
- **`nonce`**: The number of transactions sent from the sender's account (including contract creation).

This approach ensures that contract addresses are deterministic, meaning the address can be known in advance before the transaction is mined. The RLP encoding of the sender's address and nonce is hashed using Keccak-256, and the last 20 bytes of the hash are taken to form the new contract address.

#### Example:
If the deployer's address is `0x1234...5678`, and their current nonce is `5`, then:
```
address = keccak256(rlp.encode([0x1234...5678, 5]))[12:]
```

### 2. Bytecode Decompilation

Decompiling EVM bytecode back into a high-level language like Solidity is a non-trivial task because Solidity compilers optimize and transform the code, making it challenging to reconstruct the original source code precisely. Here’s an overview of the process:

- **Disassembly**: Converts bytecode into opcodes. This process is relatively straightforward because the EVM bytecode has a fixed mapping to opcodes.
  
  Example:
  ```
  Bytecode: 6080604052
  Opcodes:  PUSH1 0x80, PUSH1 0x40, MSTORE
  ```

- **Control Flow Analysis**: Identifies functions, loops, and conditional branches based on opcode sequences.

- **Symbolic Execution**: Simulates the execution of the bytecode to track state changes, function calls, and data flow.

- **Variable Identification**: Attempts to determine where variables are being stored, usually in memory or storage slots.

- **Reconstruction**: Using all the gathered information, tools like **Mythril**, **Ethersplay**, or **Porosity** try to rebuild an approximation of the original high-level code.

#### Example Bytecode Analysis:
Consider the following bytecode snippet for a contract function:
```
6080604052348015600f57600080fd5b506004361060285760003560e01c80636d4ce63c14602d575b600080fd5b60336035565b005b
```

Disassembly gives:
```
PUSH1 0x80
PUSH1 0x40
MSTORE
CALLVALUE
DUP1
ISZERO
PUSH2 0x0f
JUMPI
PUSH1 0x00
DUP1
REVERT
JUMPDEST
POP
...
```

Each opcode manipulates the EVM stack, memory, or storage, with conditional branching (`JUMPI`), data pushing (`PUSH`), and error handling (`REVERT`).

### 3. Memory and Storage in the EVM

#### 3.1 Memory
- **Volatile**: Memory is reset after each transaction. It is used for temporary data manipulation during contract execution.
- **Dynamic Size**: The size of memory can grow during execution, but gas costs increase linearly with memory size.
- **Usage**:
  - Passing parameters between functions.
  - Holding intermediate computation results.
  - ABI encoding for function calls.

#### 3.2 Storage
- **Persistent**: Storage is maintained between transactions and represents the contract's permanent state.
- **Slot-based**: Storage is organized in 32-byte slots. Each state variable is mapped to a specific slot, either directly or through hashing (for dynamic arrays and mappings).
- **Costly Operations**:
  - Writing to storage is expensive in terms of gas, especially if creating new storage or resetting a slot to zero.
  - Reading from storage is significantly cheaper but still more costly than reading from memory.

### 4. Example with a Complex Contract

Let's analyze a more complex contract that demonstrates various memory and storage interactions.

```solidity
pragma solidity ^0.8.0;

contract ComplexStorage {
    struct Data {
        uint256 id;
        string name;
        uint256[] values;
    }

    mapping(address => Data) private userData;
    uint256 public dataCount;

    // Adds new data to the mapping
    function addData(string memory name, uint256[] memory values) public {
        Data storage data = userData[msg.sender];
        data.id = dataCount++;
        data.name = name;
        data.values = values;
    }

    // Retrieves data based on the caller's address
    function getData() public view returns (Data memory) {
        return userData[msg.sender];
    }
}
```

#### Storage Assignment:
- **`userData` Mapping**: 
  - The storage location for each address in the mapping is computed using `keccak256(address . slot)`, where `slot` is the slot assigned to `userData` in the contract (e.g., slot 0).
- **`dataCount` Variable**: 
  - This is stored in slot 1.

#### Execution Flow:

1. **Function `addData` Execution:**
   - **Memory Usage**:
     - The `name` and `values` arrays are temporarily stored in memory.
     - Function parameters are ABI-encoded and loaded into memory for further processing.
   - **Storage Writes**:
     - The `dataCount` is incremented and stored in slot 1.
     - The mapping's data for the caller's address is stored in a dynamically computed slot based on the hash of the address and mapping slot.

2. **Function `getData` Execution:**
   - **Storage Reads**:
     - The function retrieves the caller's data from storage using the same slot calculation as above.
   - **Returning Data**:
     - The returned `Data` struct is copied into memory and ABI-encoded to be sent back to the caller.

### Visualizing Memory and Storage

#### Memory Layout (During Execution)
```
+---------------------------+
|  Stack                    |
+---------------------------+
|  Data for name (string)   |   <- Temporary storage for function parameter
+---------------------------+
|  Data for values (array)  |   <- Dynamic array manipulation
+---------------------------+
|  ABI-encoded return value |   <- Result from getData
+---------------------------+
```

#### Storage Layout
```
Slot 0: userData mapping base slot
    +-- keccak256(address . 0): Data struct for user (id, name, values)
Slot 1: dataCount (global counter)
```

### Contract Address Generation, Bytecode, and Storage Summary

- **Contract Addresses**: Generated deterministically based on the deployer's address and the current nonce.
- **Bytecode Decompilation**: Involves converting bytecode back to opcodes, analyzing control flow, and reconstructing variables and functions.
- **Memory and Storage**: The EVM uses memory for temporary data and storage for persistent state, with operations varying in gas costs depending on their nature (read vs. write, storage vs. memory).

Understanding these concepts provides a foundation for optimizing complex smart contracts for better performance and lower gas costs.


Let's walk through another simple example of a Solidity smart contract, its corresponding EVM bytecode, opcodes, and how it handles storage. This example will cover contract deployment and a function call to store and retrieve a value.

### Example Solidity Smart Contract

```solidity
// SimpleStorage.sol
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 storedData;

    // Function to set the storedData value
    function set(uint256 x) public {
        storedData = x;
    }

    // Function to get the storedData value
    function get() public view returns (uint256) {
        return storedData;
    }
}
```

### 1. Contract Compilation to Bytecode

When the Solidity code is compiled, it generates:
- **Initialization Bytecode**: Runs when the contract is deployed. This code is responsible for setting up the contract on the blockchain.
- **Runtime Bytecode**: The actual code that will be stored in the contract's address and used for function execution.

### 2. Initialization Bytecode Execution (Contract Creation Flow)

During deployment, the contract's constructor is executed to generate the runtime bytecode. For this contract, there is no custom constructor, so the initialization code mostly copies the runtime bytecode to the blockchain.

### 3. EVM Bytecode Example and Opcodes Explanation

#### Sample Bytecode for the Runtime (Simplified)
The compiled bytecode for the `SimpleStorage` contract may look something like this (simplified):

```
608060405234801561001057600080fd5b50610120806100206000396000f3fe
6080604052600436106100295760003560e01c80636057361d1461002e578063
6d4ce63c14610057575b600080fd5b61003661007b565b604051610043919061
00e0565b60405180910390f35b61005f610081565b60405161006c91906100e0
565b60405180910390f35b6000602081905290815260409020548156fea26469
6f6e582066756c6c7920636f6d70696c6564207769746820736f6c6964697479
2076312e302e30a265627a7a72315820d691bb60f28a0a0c00ec3dff8a02924f
8f8f5e9ef7b6a72408e256f389eaf064736f6c63430008090033
```

#### Explanation of Opcodes and Flow

The bytecode translates into a series of opcodes executed by the EVM:

- **`PUSH1`, `PUSH2`, etc.**: Push data onto the stack.
- **`MSTORE`**: Store a value in memory.
- **`CALL`**: Call another contract or function.
- **`RETURN`**: Return output from a function call.
- **`SSTORE`**: Store a value in contract storage.
- **`SLOAD`**: Load a value from contract storage.

#### Example Breakdown
Let's focus on a specific part of the contract execution to understand how the EVM uses opcodes to interact with storage.

1. **Function `set(uint256 x)` Execution**
   - The function selector for `set(uint256)` is derived from the first 4 bytes of the Keccak-256 hash of the function signature: `0x6057361d`.
   - The function execution flow in opcodes:
     ```
     PUSH1 0x00           ; Memory position for storage
     CALLDATALOAD         ; Load the argument x from calldata
     PUSH1 0x00           ; Position for the storage key (slot 0)
     SSTORE               ; Store the value in storage slot 0
     ```
   - The `SSTORE` operation updates the `storedData` in storage slot `0x00`.

2. **Storage Representation After Execution**
   ```
   Slot 0x00: 0x00000000000000000000000000000000000000000000000000000000000000XX
   ```
   Where `XX` is the hex representation of the stored value.

3. **Function `get()` Execution**
   - The function selector for `get()` is derived from the first 4 bytes of the Keccak-256 hash of the function signature: `0x6d4ce63c`.
   - The function execution flow in opcodes:
     ```
     PUSH1 0x00           ; Position for the storage key (slot 0)
     SLOAD                ; Load the value from storage
     ```
   - The value from storage slot `0x00` is returned.

### 4. Detailed Storage Assignment

The contract has one storage variable, `storedData`, mapped to storage slot `0x00`. The EVM uses the `SSTORE` and `SLOAD` opcodes to manage this storage:

- **Storage Write (`SSTORE`)**:
  - When calling the `set` function, the EVM writes to slot `0x00` using the value passed as an argument.
  - Gas costs for `SSTORE` are higher if writing a new non-zero value and lower when setting an existing slot to zero.

- **Storage Read (`SLOAD`)**:
  - When calling the `get` function, the EVM reads from slot `0x00` to retrieve the stored data.
  - Gas cost for reading storage is significantly lower than writing.

### 5. Full Execution Flow Summary

1. **Contract Deployment**
   - Initialization code executes, setting up the contract's runtime bytecode.
   - Contract's runtime code is stored at a new address on the blockchain.

2. **`set(uint256)` Execution**
   - `0x6057361d` is used as the function selector.
   - Argument `x` is loaded from calldata.
   - `SSTORE` stores the value of `x` in storage slot `0x00`.

3. **`get()` Execution**
   - `0x6d4ce63c` is used as the function selector.
   - `SLOAD` retrieves the value from storage slot `0x00`.

### ASCII Art for the Execution Flow

```
Contract Creation
    +-------------------------------+
    |   Initialization Bytecode     |
    +-------------------------------+
             |
             v
    +-------------------------------+
    |     Runtime Bytecode          | <-- Deployed at Contract Address
    +-------------------------------+

Function Call: set(x)
    +-------------------------------+
    |  PUSH1 0x00                   |  // Storage slot
    |  CALLDATALOAD                 |  // Load argument x
    |  PUSH1 0x00                   |  // Slot 0
    |  SSTORE                       |  // Store x in slot 0
    +-------------------------------+

Function Call: get()
    +-------------------------------+
    |  PUSH1 0x00                   |  // Slot 0
    |  SLOAD                        |  // Load value from slot 0
    |  RETURN                       |  // Return the value
    +-------------------------------+
```

### Summary

This example illustrates how a simple Solidity contract interacts with the EVM at a low level, showing the relationship between Solidity code, bytecode, opcodes, and storage. Understanding these elements is crucial for optimizing smart contract performance and gas efficiency.



## Buy Me a Coffee

If you find this EVM Cheatsheet helpful, you can support my work by sending some Ether to my Ethereum address:

**Ethereum Address:** `0xe75280C15B3E07A33F6c79cf3f088947E313ae8e`
