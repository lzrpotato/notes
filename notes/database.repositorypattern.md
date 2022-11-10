---
id: glteug4lbh4msez8vvsqcwk
title: Repository Pattern
desc: ''
updated: 1668103802849
created: 1668102999290
---

## Repository Pattern
As per Eric Evans' book *Domain-Driven Design*, "repository is a mechanism for encapsulating storage, retrieval, and search behavior, which emulates a collection of objects."

## Comparing Repository Pattern with DAO
Their differences [link](https://www.baeldung.com/java-dao-vs-repository):
- DAO is an abstraction of data persistence. However, a repository is an abstraction of a collection of objects
- DAO is a lower-level conecpt, closer to the storage systems. However, repository is a higher-level concept, closer to the Domain objects.
- DAO works as a data mapping/access layer, hiding ugly queries. However, a repository is a layer between domains and data access layers, hiding the complexity of collating data and preparing a domain object 
- DAO can't be implemeted using a repository. However, a reoisutirt can use a DAo for accessing underlying storage.