# Title
w3if-SEC-0002 - Delegating Multi-Signature Role Authorization

# Introduction
In a group environment, such as company, we need to have a team of people work on items, such as configuration of smart contracts etc.  Currently, we have an Owner Address and an Administrative Address (in the case of a Product Contract - a Product Manager).

However, when this person moves roles or is away, we need a delegate team to Administer the Contract.  

This IP extends w3if-SEC-0001 - in addressing the issue that many contracts will manage their own access control lists.  Therefore, when a person is removed, we need to find all contracts attached to their profile and remove it.

By centralizing the Role Authorization into a separate contract, we can update a single location and manage roles and permissions.

# Simple Summary   
The Management of a Smart Contract is external to a single person, an authorized group of Contract Admins.

They have roles defined within the Contract, which really are around the CRUD methods.

The Contract Admin will control additions and deletions from the Contract, along with the role (CRUD)

Admins can make new Admins

# Abstract


# Motivation
These insurance contracts will live a long time, most will be 1 year, but Product can live years.

We need to scale the ability of teams to manage the configuration

# Specification


# Caveats


# Rationale

## Privacy

## Community Consensus

## Backwards Compatibility


# Test Cases

# Implementations

# References
## Standards


## Issues


## Discussions


# Copyright
Copyright and related rights waived via CC0.

# Citation
