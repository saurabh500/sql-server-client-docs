# Purpose

This page describes how to construct a transaction manager Request object. The page starts from the protocol grammar and then creates sequence diagrams.

The sequence diagrams are meant to be converted to code.

## Grammar

```ABNF
TransMgrReq      =   ALL_Headers 
                    RequestType 
                    [RequestPayload]

```

## RequestType

### Possible Values

- **0 = TM_GET_DTC_ADDRESS**  
  Returns DTC network address as a result set with a single column, single-row binary value.

- **1 = TM_PROPAGATE_XACT**  
  Imports DTC transaction into the server and returns a local transaction descriptor as a varbinary result set.

- **5 = TM_BEGIN_XACT**  
  Begins a transaction and returns the descriptor in an ENVCHANGE type 8.

- **6 = TM_PROMOTE_XACT**  
  Converts an active local transaction into a distributed transaction and returns an opaque buffer in an ENVCHANGE type 15.

- **7 = TM_COMMIT_XACT**  
  Commits a transaction. Depending on the payload of the request, it can additionally request that another local transaction be started.

- **8 = TM_ROLLBACK_XACT**  
  Rolls back a transaction. Depending on the payload of the request, it can indicate that after the rollback, a local transaction is to be started.

- **9 = TM_SAVE_XACT**  
  Sets a savepoint within the active transaction. This request MUST specify a nonempty name for the savepoint.


## Request Payload


### Request Payloads for Different Request Types

- **RequestType TM_GET_DTC_ADDRESS**  
  - **RequestPayload**: `US_VARBYTE`  
    The RequestPayload SHOULD be a zero-length US_VARBYTE.

- **RequestType TM_PROPAGATE_XACT**  
  - **RequestPayload**: `US_VARBYTE`  
    Data contains an opaque buffer used by the server to enlist in a DTC transaction (for more information, see [MSDN-ITrans]).

- **RequestType TM_BEGIN_XACT**  
  - **ISOLATION_LEVEL**: `BYTE`  
  - **BEGIN_XACT_NAME**: `B_VARBYTE`  
  - **RequestPayload**:  
    ```
    ISOLATION_LEVEL  
    BEGIN_XACT_NAME
    ```
    This request begins a new transaction or increments trancount if already in a transaction. If `BEGIN_XACT_NAME` is nonempty, a transaction is started with the specified name. See the definition for isolation level at the end of this table.

- **RequestType TM_PROMOTE_XACT**  
  - **RequestPayload**: No payload.  
    This message promotes the transaction of the current request (specified in the Transaction Descriptor header, section 2.2.5.3.2). The current transaction MUST be part of the specified header. Note that `TM_PROMOTE_XACT` is supported only for transactions initiated via `TM_BEGIN_XACT`, or via piggyback operation on `TM_COMMIT/TM_ROLLBACK`. An error is returned if `TM_PROMOTE_XACT` is invoked for a TSQL initiated transaction.

- **RequestType TM_COMMIT_XACT**  
  - **fBeginXact**: `BIT`  
  - **XACT_FLAGS**:  
    ```
    fBeginXact  
    7FRESERVEDBIT
    ```
  - **ISOLATION_LEVEL**: `BYTE`  
  - **XACT_NAME**: `B_VARBYTE`  
  - **BEGIN_XACT_NAME**: `B_VARBYTE`  
  - **RequestPayload**:  
    ```
    XACT_NAME  
    XACT_FLAGS  
    [ISOLATION_LEVEL  
    BEGIN_XACT_NAME]
    ```
    Without additional flags specified, this command is semantically equivalent to issuing a TSQL COMMIT statement. The flags in `XACT_FLAGS` are represented in least significant bit order. If `fBeginXact` is 1, then a new local transaction is started after the commit operation is done. If `fBeginXact` is 1, then `ISOLATION_LEVEL` can specify the isolation level to use to start the new transaction, according to the definition at the end of this table. If `fBeginXact` is 0, then `ISOLATION_LEVEL` SHOULD NOT be present. Specifying `ISOLATION_LEVEL` allows the isolation level to remain in effect for the session, once the transaction ends. If `fBeginXact` is 0, `BEGIN_XACT_NAME` SHOULD NOT be present. If `fBeginXact` is 1, `BEGIN_XACT_NAME` can be nonempty. If `fBeginXact` is 1, a new transaction MUST be started. If `BEGIN_XACT_NAME` is nonempty, the new transaction MUST be given the specified name. See the definition for isolation level at the end of this table.

- **RequestType TM_ROLLBACK_XACT**  
  - **fBeginXact**: `BIT`  
  - **XACT_FLAGS**:  
    ```
    fBeginXact  
    7FRESERVEDBIT
    ```
  - **ISOLATION_LEVEL**: `BYTE`  
  - **XACT_NAME**: `B_VARBYTE`  
  - **BEGIN_XACT_NAME**: `B_VARBYTE`  
  - **RequestPayload**:  
    ```
    XACT_NAME  
    XACT_FLAGS  
    [ISOLATION_LEVEL  
    BEGIN_XACT_NAME]
    ```
    The flags in `XACT_FLAGS` are represented in least significant bit order. If `XACT_NAME` is nonempty, this request rolls back the named transaction. This implies that if `XACT_NAME` specifies a savepoint name, the rollback only goes back until the specified savepoint. Without additional flags specified, this command is semantically equivalent to issuing a TSQL ROLLBACK statement under the current transaction. If `fBeginXact` is 1, then a new local transaction is started after the commit operation is done. If `fBeginXact` is 1, then `ISOLATION_LEVEL` can specify the isolation level to use to start the new transaction, according to the definition at the end of this table. If `fBeginXact` is 0, then `ISOLATION_LEVEL` SHOULD NOT be present. Specifying `ISOLATION_LEVEL` allows the isolation level to remain in effect for the session, once the transaction ends. If `fBeginXact` is 0, `BEGIN_XACT_NAME` SHOULD NOT be present. If `fBeginXact` is 1, `BEGIN_XACT_NAME` can be nonempty. If `fBeginXact` is 1, a new transaction MUST be started. If `BEGIN_XACT_NAME` is nonempty, the new transaction MUST be given the specified name. If `fBeginXact` is 1, and the ROLLBACK only rolled back to a savepoint, the Begin_Xact operation is ignored and trancount remains unchanged. See the definition for isolation level at the end of this table.

- **RequestType TM_SAVE_XACT**  
  - **XACT_SAVEPOINT_NAME**: `B_VARBYTE`  
  - **RequestPayload**: `XACT_SAVEPOINT_NAME`  
    A nonempty name MUST be specified as part of this request. Otherwise, an error is raised.

## Tabular representation

| RequestType          | Possible Values                                                                 | Request Payload                                                                 |
|----------------------|---------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| TM_GET_DTC_ADDRESS   | 0 = Returns DTC network address as a result set with a single column, single-row binary value. | `US_VARBYTE`: The RequestPayload SHOULD be a zero-length US_VARBYTE.            |
| TM_PROPAGATE_XACT    | 1 = Imports DTC transaction into the server and returns a local transaction descriptor as a varbinary result set. | `US_VARBYTE`: Data contains an opaque buffer used by the server to enlist in a DTC transaction. |
| TM_BEGIN_XACT        | 5 = Begins a transaction and returns the descriptor in an ENVCHANGE type 8.     | `ISOLATION_LEVEL`, `BEGIN_XACT_NAME`: Begins a new transaction or increments trancount if already in a transaction. |
| TM_PROMOTE_XACT      | 6 = Converts an active local transaction into a distributed transaction and returns an opaque buffer in an ENVCHANGE type 15. | No payload: Promotes the transaction of the current request.                    |
| TM_COMMIT_XACT       | 7 = Commits a transaction. Depending on the payload of the request, it can additionally request that another local transaction be started. | `XACT_NAME`, `XACT_FLAGS`, `[ISOLATION_LEVEL, BEGIN_XACT_NAME]`: Semantically equivalent to issuing a TSQL COMMIT statement. |
| TM_ROLLBACK_XACT     | 8 = Rolls back a transaction. Depending on the payload of the request, it can indicate that after the rollback, a local transaction is to be started. | `XACT_NAME`, `XACT_FLAGS`, `[ISOLATION_LEVEL, BEGIN_XACT_NAME]`: Semantically equivalent to issuing a TSQL ROLLBACK statement. |
| TM_SAVE_XACT         | 9 = Sets a savepoint within the active transaction. This request MUST specify a nonempty name for the savepoint. | `XACT_SAVEPOINT_NAME`: A nonempty name MUST be specified as part of this request. |

