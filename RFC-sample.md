---

eip: ERC-1400
title: Security Token Standards
author: Adam Dossa (@adamdossa), Pablo Ruiz (@pabloruiz55), Fabian Vogelsteller (@frozeman), Stephane Gosselin (@thegostep)
discussions-to: #1411
status: Draft
type: Standards Track
category: ERC
created: 2018-09-09
require: ERC-1410 (#1410), ERC-1594 (#1594), ERC-1644 (#1644), ERC-1643 (#1643), ERC-20 (#20), ERC-1066 (#1066)

---

## Simple Summary

Represents a library of standards for security tokens on Ethereum.

In aggregate provides a suite of standard interfaces for issuing / redeeming security tokens, managing their ownership and transfer restrictions and providing transparency to token holders on how different subsets of their token balance behave with respect to transfer restrictions, rights and obligations.

## Abstract

Standards should be backwards compatible with ERC-20 (#20) and easily extended to be compatible with ERC-777 (#777).

ERC-1410 (#1410): differentiated ownership / transparent restrictions
ERC-1594 (#1594): on-chain restriction checking with error signalling, off-chain data injection for transfer restrictions and issuance / redemption semantics
ERC-1643 (#1643): document / legend management
ERC-1644 (#1644): controller operations (force transfer)

## Motivation

Accelerate the issuance and management of securities on the Ethereum blockchain by specifying standard interfaces through which security tokens can be operated on and interrogated by all relevant parties.

Taken together, these security token standards provide document management, error signalling, gate keeper (operator) access control, off-chain data injection, issuance / redemption semantics and expose partially fungible subsets of a token holders balance.

## Requirements

Moving the issuance, trading and lifecycle events of a security onto a public ledger requires having a standard way of modelling securities, their ownership and their properties on-chain.

The following requirements have been compiled following discussions with parties across the Security Token ecosystem.

- MUST have a standard interface to query if a transfer would be successful and return a reason for failure.
- MUST be able to perform forced transfer for legal action or fund recovery.
- MUST emit standard events for issuance and redemption.
- MUST be able to attach metadata to a subset of a token holder's balance such as special shareholder rights or data for transfer restrictions.
- MUST be able to modify metadata at time of transfer based on off-chain data, on-chain data and the parameters of the transfer.
- MUST support querying and subscribing to updates on any relevant documentation for the security.
- MAY require signed data to be passed into a transfer transaction in order to validate it on-chain.
- SHOULD NOT restrict the range of asset classes across jurisdictions which can be represented.
- MUST be ERC-20 compatible.
- COULD be ERC-777 compatible.

## Rationale

### ERC-1594: Core Security Token Standard

Transfers of securities can fail for a variety of reasons in contrast to utility tokens which generally only require the sender to have a sufficient balance.

These conditions could be related to metadata of the securities being transferred (i.e. whether they are subject to a lock-up period), the identity of the sender and receiver of the securities (i.e. whether they have been through a KYC process, whether they are accredited or an affiliate of the issuer) or for reasons unrelated to the specific transfer but instead set at the token level (i.e. the token contract enforces a maximum number of investors or a cap on the percentage held by any single investor).

For ERC-20 tokens, the `balanceOf` and `allowance` functions provide a way to check that a transfer is likely to succeed before executing the transfer, which can be executed both on and off-chain.

For tokens representing securities the standard introduces a function `canTransfer` / `canTransferByPartition` which provides a more general purpose way to achieve this when the reasons for failure are more complex; and a function of the whole transfer (i.e. includes any data sent with the transfer and the receiver of the securities).

In order to provide a richer result than just true or false, a byte return code is returned. This allows us to give a reason for why the transfer failed, or at least which category of reason the failure was in. The ability to query documents and the expected success of a transfer is included in Security Token section.

In order to support off-chain data inputs to transfer functions, transfer functions are extended to `transferWithData` / `transferFromWithData` which can optionally take an additional `bytes _data` parameter.

### ERC-1410: Partially Fungible Tokens

There are many types of securities which, although they represent the same underlying asset, need to have differentiating data tied to them.

This additional metadata implicitly renders these securities non-fungible, but in practice this data is usually applied to a subset of the security rather than an individual security. The ability to partition a token holder's balance into partitions, each with separate metadata is addressed in the Partially Fungible Token section.

For example a token holder's balance may be split in two: those tokens issued during the primary issuance, and those received through secondary trading.

Security token contracts can reference this metadata in order to apply additional logic to determine whether or not a transfer is valid, and determine the metadata that should be associated with the tokens once transferred into the receiver's balance.

Alternatively a security token can use this mechanism simply to be able to transparently display to investors how different subsets of their tokens will behave with respect to transfer restrictions. In this case, the balances could be determined programatically.

### ERC-1643: Document Management Standard

Security tokens usually have documentation associated with them. This could be an offering document, legend details and so on.

Being able to set / remove and retrieve these documents, and having events associated with these actions allows investors to stay up to date with documentation on their investments.

This standard does not provide any way for an investor to signal on-chain that they have read, or agree, with any of these documents.

### ERC-1644: Controller Token Operation Standard

Since security tokens are subject to regulatory and legal oversight (the details of which will vary depending on jurisdiction, regulatory framework and underlying asset) in many instances the issuer (or a party delegated to by the issuer acting as a controller, e.g. a regulator or transfer agent) will need to retain the ability to force transfer tokens between addresses.

These controller transfers should be transparent (emit events that flag this as a forced transfer) and the token contract itself should be explicit as to whether or not this is possible.

Examples of where this may be needed is to reverse fraudulent transactions, resolve lost private keys and responding to a court order.

## Specification

This standard does not specify any additional functions, but references ERC-1410 (#1410), ERC-1594 (#1594), ERC-1643 (#1643) and ERC-1655 (#1644) as an underlying library of security token standards, each covering a different aspect of security token functionality.

In order to combine these two standards, the additional constraints are specified.

### operatorTransferByPartition

If the token is controllable (`isControllable` returns `TRUE`) then the controller may use `operatorTransferByPartition` without being explicitly authorised by the token holder.

In this instance, the `operatorTransferByPartition` MUST also emit a ControllerTransfer event.

Correspondingly, if `isControllable` returns `FALSE` then the controller cannot call `operatorTransferByPartition` unless explicitly authorised by the token holder.

### operatorRedeemByPartition

If the token is controllable (`isControllable` returns `TRUE`) then the controller may use `operatorRedeemByPartition` without being explicitly authorised by the token holder.

In this instance, the `operatorRedeemByPartition` MUST also emit a ControllerRedemption event.

Correspondingly, if `isControllable` returns `FALSE` then the controller cannot call `operatorRedeemByPartition` unless explicitly authorised by the token holder.

### Default Partitions

In order for `transfer` and `transferWithData` to operate on partially fungible tokens, there needs to be some notion of default partitions that these functions apply to. The details for how these are determined (e.g. either a fixed list, dynamically, or using `partitionsOf`) is left as an implementation detail rather than defined as part of the standard.

When transferring tokens as part of a `transfer` or `transferWithData` operation, these transfers should respect the invariant of partially fungible tokens, namely that the sum of the balances across all partitions should equal to the total balance of a token holder.

## Interface

``` solidity
/// @title IERC1400 Security Token Standard
/// @dev See https://github.com/SecurityTokenStandard/EIP-Spec

interface IERC1400 is IERC20 {

  // Document Management
  function getDocument(bytes32 _name) external view returns (string, bytes32);
  function setDocument(bytes32 _name, string _uri, bytes32 _documentHash) external;

  // Token Information
  function balanceOfByPartition(bytes32 _partition, address _tokenHolder) external view returns (uint256);
  function partitionsOf(address _tokenHolder) external view returns (bytes32[]);

  // Transfers
  function transferWithData(address _to, uint256 _value, bytes _data) external;
  function transferFromWithData(address _from, address _to, uint256 _value, bytes _data) external;

  // Partition Token Transfers
  function transferByPartition(bytes32 _partition, address _to, uint256 _value, bytes _data) external returns (bytes32);
  function operatorTransferByPartition(bytes32 _partition, address _from, address _to, uint256 _value, bytes _data, bytes _operatorData) external returns (bytes32);

  // Controller Operation
  function isControllable() external view returns (bool);
  function controllerTransfer(address _from, address _to, uint256 _value, bytes _data, bytes _operatorData) external;
  function controllerRedeem(address _tokenHolder, uint256 _value, bytes _data, bytes _operatorData) external;

  // Operator Management
  function authorizeOperator(address _operator) external;
  function revokeOperator(address _operator) external;
  function authorizeOperatorByPartition(bytes32 _partition, address _operator) external;
  function revokeOperatorByPartition(bytes32 _partition, address _operator) external;

  // Operator Information
  function isOperator(address _operator, address _tokenHolder) external view returns (bool);
  function isOperatorForPartition(bytes32 _partition, address _operator, address _tokenHolder) external view returns (bool);

  // Token Issuance
  function isIssuable() external view returns (bool);
  function issue(address _tokenHolder, uint256 _value, bytes _data) external;
  function issueByPartition(bytes32 _partition, address _tokenHolder, uint256 _value, bytes _data) external;

  // Token Redemption
  function redeem(uint256 _value, bytes _data) external;
  function redeemFrom(address _tokenHolder, uint256 _value, bytes _data) external;
  function redeemByPartition(bytes32 _partition, uint256 _value, bytes _data) external;
  function operatorRedeemByPartition(bytes32 _partition, address _tokenHolder, uint256 _value, bytes _operatorData) external;

  // Transfer Validity
  function canTransfer(address _to, uint256 _value, bytes _data) external view returns (byte, bytes32);
  function canTransferFrom(address _from, address _to, uint256 _value, bytes _data) external view returns (byte, bytes32);
  function canTransferByPartition(address _from, address _to, bytes32 _partition, uint256 _value, bytes _data) external view returns (byte, bytes32, bytes32);    

  // Controller Events
  event ControllerTransfer(
      address _controller,
      address indexed _from,
      address indexed _to,
      uint256 _value,
      bytes _data,
      bytes _operatorData
  );

  event ControllerRedemption(
      address _controller,
      address indexed _tokenHolder,
      uint256 _value,
      bytes _data,
      bytes _operatorData
  );

  // Document Events
  event Document(bytes32 indexed _name, string _uri, bytes32 _documentHash);

  // Transfer Events
  event TransferByPartition(
      bytes32 indexed _fromPartition,
      address _operator,
      address indexed _from,
      address indexed _to,
      uint256 _value,
      bytes _data,
      bytes _operatorData
  );

  event ChangedPartition(
      bytes32 indexed _fromPartition,
      bytes32 indexed _toPartition,
      uint256 _value
  );

  // Operator Events
  event AuthorizedOperator(address indexed _operator, address indexed _tokenHolder);
  event RevokedOperator(address indexed _operator, address indexed _tokenHolder);
  event AuthorizedOperatorByPartition(bytes32 indexed _partition, address indexed _operator, address indexed _tokenHolder);
  event RevokedOperatorByPartition(bytes32 indexed _partition, address indexed _operator, address indexed _tokenHolder);

  // Issuance / Redemption Events
  event Issued(address indexed _operator, address indexed _to, uint256 _value, bytes _data);
  event Redeemed(address indexed _operator, address indexed _from, uint256 _value, bytes _data);
  event IssuedByPartition(bytes32 indexed _partition, address indexed _operator, address indexed _to, uint256 _value, bytes _data, bytes _operatorData);
  event RedeemedByPartition(bytes32 indexed _partition, address indexed _operator, address indexed _from, uint256 _value, bytes _operatorData);


}
```

### Notes

#### On-chain vs. Off-chain Transfer Restrictions

The rules determining if a security token can be sent may be self-executing (e.g. a rule which limits the maximum number of investors in the security) or require off-chain inputs (e.g. an explicit broker approval for the trade). To facilitate the latter, the `transferByPartition`, `transferWithData`, `transferFromWithData`, `canTransferByPartition` and `canTransfer` functions accept an additional `bytes _data` parameter which can be signed by an approved party and used to validate a transfer.

The specification for this data is outside the scope of this standard and would be implementation specific.

#### Identity

Under many jurisdictions, whether a party is able to receive and send security tokens depends on the characteristics of the party's identity. For example, most jurisdictions require some level of KYC / AML process before a party is eligible to purchase or sell a particular security. Additionally, a party may be categorized into an investor qualification category (e.g. accredited investor, qualified purchaser), and their citizenship may also inform restrictions associated with their securities.

There are various identity standards (e.g. ERC-725 (#725), Civic, uPort) which can be used to capture the party's identity data, as well as other approaches which are centrally managed (e.g. maintaining a whitelist of addresses that have been approved from a KYC perspective). These identity standards have in common to key off an Ethereum address (which could be a party's wallet, or an identity contract), and as such the `canTransfer` function can use the address of both the sender and receiver of the security token as a proxy for identity in deciding if eligibility requirements are met.

Beyond this, the standard does not mandate any particular approach to identity.

#### Reason Codes

To improve the token holder experience, `canTransfer` MUST return a reason byte code on success or failure based on the EIP-1066 application-specific status codes specified below. An implementation can also return arbitrary data as a `bytes32` to provide additional information not captured by the reason code.

| Code   | Reason                                                        |
| ------ | ------------------------------------------------------------- |
| `0x50` | 	transfer failure                                             |
| `0x51` | 	transfer success                                             |
| `0x52` | 	insufficient balance                                         |
| `0x53` | 	insufficient allowance                                       |
| `0x54` | 	transfers halted (contract paused)                           |
| `0x55` | 	funds locked (lockup period)                                 |
| `0x56` | 	invalid sender                                               |
| `0x57` | 	invalid receiver                                             |
| `0x58` | 	invalid operator (transfer agent)                            |
| `0x59` |                                                               |
| `0x5a` |                                                               |
| `0x5b` |                                                               |
| `0x5a` |                                                               |
| `0x5b` |                                                               |
| `0x5c` |                                                               |
| `0x5d` |                                                               |
| `0x5e` |                                                               |
| `0x5f` | 		token meta or info

These codes are being discussed at:  
https://ethereum-magicians.org/t/erc-1066-ethereum-status-codes-esc/283/24

## References
- [EIP 1410: Partially Fungible Token Standard](https://github.com/ethereum/EIPs/issues/1410)
- [EIP 1594: Core Security Token Standard](https://github.com/ethereum/EIPs/issues/1594)
- [EIP 1643: Document Management Standard](https://github.com/ethereum/EIPs/issues/1643)
- [EIP 1644: Controller Token Operation Standard](https://github.com/ethereum/EIPs/issues/1644)
- [EIP Draft](https://github.com/SecurityTokenStandard/EIP-Spec)

_Copied from original issue_: https://github.com/ethereum/EIPs/issues/1400