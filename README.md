# dddappp: A Domain-Driven DAPP low-code development Platform

English | [中文](README_CN.md)

[ToC]

## What we want to do

A **true** Low-code development platform(LCDP) that allows developers to build extremely complex decentralized applications(Dapps) efficiently with experiences that are very close to developing "traditional" applications.

A low-code development platform that helps developers build decentralized applications that maximize the use of blockchains as technical infrastructure.

We know things can't be done overnight. In the initial phase, the platform will prioritize support for building Dapps running on Move public blockchains.

In the longer term, the platform does not restrict developers to use any programming language to build Dapps, allowing them to focus on the implementation of the "business logic". Thus, developers of "traditional" applications can more easily enjoy the dividends of blockchain development; while "traditional" enterprise software and Web 2.0 Internet applications, which are already highly complex, can be migrated onto blockchains at low cost and become decentralized Web 3.0 applications.

### What is a "true" low-code development platform

"What is this 'true' low-code development platform you speak of?"

Here we think it is necessary to clarify the difference between "low-code" and "no-code". No-code refers more to a category of "end-user" oriented tools through which users can quickly assemble certain applications; for example, some Internet form tools, website building tools, etc. The low-code platform, on the other hand, we think is for professional developers.

We believe that a true low-code development platform should have the following key features.

* **Model driven** development. These models should ideally be "domain models" that are as far from "technology" and as close to "business" as possible. Because "communication" is the biggest cost in complex software development - even if the low-code platform is for developers and the "no-code" is for end users(business people).

* **Visual Development**. Visual modeling tools are an important part of the visual development toolbox for low-code platforms. However, it should be noted that if the modeling tool can save the model as a well-readable plain text, then the specification of this text is actually a DSL(domain-specific language), and such a DSL can actually be written manually by the developer(without the help of a visual modeling tool).

* Support the use of **expression languages**. An expression language should be simpler and easier to write business logic in than the general-purpose language(GPL). It should be functional programming style; it should be as declarative as possible - that is, for expressing "what is wanted" rather than "how it is done".

* **Systematic software engineering support**. The low-code platform should integrate best practices in the areas of software engineering including development, debug, testing, version control, DevOps, etc.

* **Applications are open to integration and are extensible**. Applications developed by low-code platforms should have open APIs as well as the ability to use external APIs and be extensible using general-purpose languages.

In our opinion, a "true" low-code platform has yet to be born in the decentralized world. It is all up to us to deliver.

## Value proposition to the Move blockchain ecosystem

We love the revolutionary innovation of "resource-oriented programming" in the Move language. We believe that the combinability of resources can greatly accelerate the pace of innovation in the Move ecosystem, making it more likely that killer applications will emerge that have never been seen on other public blockchains.

But nevertheless, the overall development efficiency of a slightly complex Dapp today, even if the on-chain part(smart contracts) uses the Move language, is hardly satisfactory.

On the other hand, the value of low-code development platforms for developing "traditional" applications has been well proven and widely recognized. Many developers of "traditional" applications have already established deep insights into various domains and have no lack of software development experiences, and they are eager to utilize their talent and intelligence in the decentralized world as soon as possible.

In particular, we would like to highlight the enormous imagination that can be raised by the combination of low-code platforms and DAOs. DAOs, as "legal persons" existing in the blockchain world, are bound to require more "business activities" to be on-chain. The growth of DAOs will amplify the demands for blockchain "business applications". There is an urgent need to reduce the development cost of decentralized versions of "business applications" and improve development efficiency. Traditional low-code platforms are already particularly well suited for rapid development of traditional "enterprise applications", and we believe this will be the case in the Web3 era.

## Current state of low-code development platforms

When we look at traditional low-code development platforms for enterprise applications in the Web 2.0 era according to the above criteria for "true" low-code, there are relative leaders.

![Magic Quadrant for Traditional LCDPs](TraditionalLCDPsMagicQuadrant.png)

So, by the time of Web 3.0, the existing Dapp low-code platforms(if they exist at all), are there any very capable ones?

Unfortunately, by the rigorous "true" low-code criteria described above, we have not yet seen the existence of such a Dapp low-code platform.

"Is it so hard? Why don't they do it?"

### The “model-driven” of enterprise application LCDPs

Traditional enterprise application development platforms(including low-code platforms) are basically "driven" by the E-R(Entity-Relationship) models and/or relational models(the models used in SQL databases).

For example, see how OutSystems does it?

!["Model Driven" Of OutSystems](ModelDrivenOfOutSystems.png)

OutSystems uses both E-R models and relational(data) models; some enterprise application development platforms use only one or the other.

There is a fairly direct correspondence between the concepts used by the E-R models and the relational models, so their modeling results(the generated codes) can easily run on the traditional enterprise software technology infrastructures - SQL databases. But it's hard for them to run on brand new technology infrastructures such as blockchains - where the dominant smart contract platforms and "decentralized ledgers" are constructed too far from traditional SQL databases.

Clearly, the development of a "true" low-code platform requires long-term accumulation of skills and experience, and the technology routes used in traditional enterprise application low-code platforms make them difficult to be ported to the entirely new space of Dapp development.

As for the existing Dapp "low-code" platforms, "They don't have those features, but then what does it matter?"

### The importance of core values

The development of a true low-code - especially adhering to model-driven - is a hard way to go. The problem with existing Dapp “low-code” platforms is that they always try to take shortcuts.

> Never forget the original intention, the only way to get to the end.

A platform's potential is determined by its "core values". The core features expected of a professional low-code platform, such as model-driven, expression language, have values that are difficult to replace with other solutions that try to "bypass" them.

For example, "configurable smart contract templates" certainly have the value of improving the efficiency of developers copying, pasting, and modifying "readymade codes" - if indeed there are "readymade codes". If a developer wants to make some innovative applications and there are no readymade "smart contract templates" available, then it won't be helpful; when multiple chains need to be supported, it is also a big burden for platform developers to write and maintain such a library of "smart contract templates" in different languages(Solidity, Move, etc.) for different chains. Moreover, "smart contracts" are often only the on-chain part of an application, and decentralized applications often require off-chain parts as well.

There is also the "expression language", and although it may be difficult to implement this feature perfectly(the implementation priority can be lower) - for example, it requires developing compilers to compile the codes written in this expression language into instructions that can be executed by the virtual machines(EVM, MoveVM, etc.) of each chain - with it, developers can use only this "expression language" to write business logic, and the developed application can run directly on different chains. There is no need to learn and use each chain's programming language, which greatly saves the cost of application development and porting.

## Technology architecture overview

"Model-driven development" is the core feature of low-code development platforms.

As mentioned earlier, the traditional models used by low-code platforms for enterprise applications are relational and/or ER models; We choose the DDD([Domain-Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design)) style domain models, which makes our platform so unique and powerful.

### The key is the modeling paradigm

The DDD domain models are OO(object-oriented) models in a relatively high level of abstraction.

![Mappings Between Models](MappingsBetweenModels.jpeg)

Mapping from the higher abstraction level DDD domain models to the lower abstraction level implementation object models(OO is the dominant paradigm for programming languages), relational models(the dominant paradigm for database models), etc. is relatively easy and can often be done by automated tools.

Mapping low-level models to each other is relatively difficult and often requires developers to step in and even write a lot of implementation codes.
Developers familiar with the ORM(Object-Relational Mapping) frameworks can easily understand this.

### Domain models as the core

The idea is that there should be a domain modeling language that can be used to accurately describe key concepts in the domain; the domain models described by this language can be used as the basis for communication across the application development team(both technical and domain experts) and should be conveniently mapped to the code implementation of the application.

This language should be a DSL that can be adopted by visualization tools as well as easy for humans to read and write.

With the domain models described by this DSL as the core, we make a toolchain to generate the implementation codes for various parts of applications from models and then run the applications on various technical infrastructures.

![Domain Models As the Core](DomainModelsAsTheCore.png)

### Domain-Driven Design Modeling Language(DDDML)

The question is whether we can find such a language as described above?

Fortunately we have found it - DDDML(Domain-Driven Design Modeling Language); more precisely, we invented it.

Eric Evans, founder of Domain-Driven Design, has said that he has always believed that DSL is the next big step in the evolution of domain-driven design.

DDDML takes this big solid step forward. You will see this in what follows.

## Who we are

"Theoretically you are all right, but why can you do it?"

Because, "using DDD-style domain models to drive development" is really the thing we've done in the Web 2.0 era!

![E-R Model/Relational Model Driven Vs. Domain Model Driven](ERDrivenVsDDDModelDriven.png)

Even when implementing a low-code platform for developing traditional enterprise applications, using DDD-style domain models to drive development is a very bold and innovative move. But the path is through, we have had a lot of valuable experience accumulated. We even published a thick monograph to share the lessons learned with developers.

Here it is necessary to highlight the architect of our project: Jiefeng Yang. He has more than 20 years of software development experience, and is a well-deserved DDD expert, the creator of DDDML, and the author of the technical book "[Deep into DDD: Driving Complex Software Development by DSL](https://item.jd.com/12834017.html)". He is also one of the developers of, or has provided key technical support for, several important ecological fundamental projects on Starcoin, the first Move public blockchain(some ecological projects on Starcoin are developed by anonymous teams). 

It is in the book "Deep into DDD" that we detail the design of DDDML, the native DSL for DDD, and its related development toolchain, and how to use them to solve various aches in complex software development processes. This well-received bestseller was published in April 2021 and reprinted in September of the same year.

![The Book "Deep into DDD"](TheBookDeepIntoDDD.png)

That is, we have completed the construction of a complete theoretical system of key technologies that we have successfully put into practice; more than that, we have done a preliminary proof of concept on how to apply these experiences to the development of Dapps - as we will demonstrate in a demo system below.


## Deliverables

This is certainly an ambitious project, but it doesn't make too much sense to discuss longer-term plans; we started by setting two small milestones for the project.

### Milestone I

We will create a command line tool or a set of tools. At a minimum, the tool will help developers generate on-chain Move contracts (the on-chain part of a Dapp), off-chain services (written in Java or Go), and even more from domain models, such as Client JavaScript SDKs, scaffolding codes of web front-end applications with user interfaces, etc.

It is expected that this milestone can be reached by two persons working for three months after the project starts.

### Milestone II

We will move the functionality of the command line tools created in Milestone I to the cloud, where developers can simply open their browsers and use them. We expect to provide a simple Web IDE for developers to write domain models, generate Dapp applications "with one click", write business logic codes, and deploy the applications to a test environment in the cloud.

It is expected that after reaching the first milestone, two persons working for three months can reach this second milestone.

