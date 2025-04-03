# Published page

The contents of this repository are published as a github page at the URL 
[Here](https://saurabh500.github.io/sql-server-client-docs/)

# Purpose

This site serves as a documentation hub for my understanding of the SQL Server drivers and the protocol.

It is designed to be a living resource where information about the current SQL Server drivers is continuously updated and expanded.

The repository is structured to support GitHub Pages, providing an easily accessible and navigable website for developers and users to explore some of the inner working of SQL Server driver implementation.

This repo will focus mostly on TDS protocol. There are some behaviors that SQL server clients implement which are not document in the protocol, e.g. How the DNS resolution should work in various scenarios.

This content is not endorsed by Microsoft. The details mentioned here are sourced from the TDS protocol, and any open source client driver that supports SQL server.


# Contributions are welcome

There is no formal contribution guide. But Open a PR, state what you are adding and why? 

# WIP

I am working on writing up how SQL RPC works, some of the fragments are here.

Refer to the [RPC execution documentation](./areas/datatypes/rpc.md).
