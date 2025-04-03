
# Purpose

This page describes how to construct a RPC Request object. The page starts from the protocol grammar and then creates sequence diagrams.

The sequence diagrams and meant to be converted to code.

## Grammar

The ABNF Grammar.

```grammar
 ProcIDSwitch     =   %xFF %xFF 
 ProcName         =   US_VARCHAR 
 NameLenProcID    =   ProcName 
                      / 
                      (ProcIDSwitch ProcID) 
  
 fWithRecomp      =   BIT 
 fNoMetaData      =   BIT 
 fReuseMetaData   =   BIT 
 OptionFlags      =   fWithRecomp 
                      fNoMetaData 
                      fReuseMetaData 
                      13FRESERVEDBIT 
  
 fByRefValue      =   BIT 
 fDefaultValue    =   BIT 
 fEncrypted       =   BIT 
 StatusFlags      =   fByRefValue 
                      fDefaultValue 
                      1FRESERVEDBIT 
                      fEncrypted 
                      4FRESERVEDBIT 
  
 ParamMetaData    =   B_VARCHAR 
                      StatusFlags 
                      (TYPE_INFO / TVP_TYPE_INFO)    ; (TVP_TYPE_INFO introduced in TDS 7.3) 
 ParamLenData     =   TYPE_VARBYTE 
  
 EncryptionAlgo   =   BYTE               ; (introduced in TDS 7.4) 
  
 AlgoName         =   B_VARCHAR          ; (introduced in TDS 7.4) 
  
 EncryptionType   =   BYTE               ; (introduced in TDS 7.4) 
  
 NormVersion      =   BYTE               ; (introduced in TDS 7.4) 
  
 DatabaseId       =   ULONG              ; (introduced in TDS 7.4) 
  
 CekId            =   ULONG              ; (introduced in TDS 7.4) 
  
 CekVersion       =   ULONG              ; (introduced in TDS 7.4) 
  
 CekMDVersion     =   ULONGLONG          ; (introduced in TDS 7.4) 
  
 ParamCipherInfo  =   TYPE_INFO 
                      EncryptionAlgo 
                      [AlgoName] 
                      EncryptionType 
                      DatabaseId  
                      CekId 
                      CekVersion 
                      CekMDVersion 
                      NormVersion 
 ParameterData    =   ParamMetaData 
                      ParamLenData 
                      [ParamCipherInfo] 
  
 EnclavePackage   =   L_VARBYTE          ; (introduced in TDS 7.4) 
  
 BatchFlag        =   %x80 / %xFF        ; (changed to %xFF in TDS 7.2) 
 NoExecFlag       =   %xFE               ; (introduced in TDS 7.2) 
  
 RPCReqBatch      =   NameLenProcID 
                      OptionFlags 
                      *EnclavePackage 
                      *ParameterData 

 RPCRequest       =   ALL_HEADERS 
                      RPCReqBatch 
                      *((BatchFlag / NoExecFlag) RPCReqBatch) 
                      [BatchFlag / NoExecFlag] 
 
 RPCRequest       =   ALL_HEADERS 
                      RPCReqBatch 
                      *((BatchFlag / NoExecFlag) RPCReqBatch) 
                      [BatchFlag / NoExecFlag] 
```

## Sequence Diagrams

### RPCRequest

```mermaid
sequenceDiagram
  participant User
  participant System

  Note over User, System: RPCRequest begins
  User->>System: ALL_HEADERS
  User->>System: RPCReqBatch

  loop Zero or more times
    alt BatchFlag
      User->>System: BatchFlag
    else NoExecFlag
      User->>System: NoExecFlag
    end
    User->>System: RPCReqBatch
  end

  alt Optional BatchFlag or NoExecFlag
    User->>System: BatchFlag / NoExecFlag
  end

  Note over User, System: RPCRequest ends
```

### RPCReqBatch

```mermaid
sequenceDiagram
  participant User
  participant System

  Note over User, System: RPCRequest begins
  User->>System: ALL_HEADERS
  User->>System: RPCReqBatch

  loop Zero or more times
    alt BatchFlag
      User->>System: BatchFlag
    else NoExecFlag
      User->>System: NoExecFlag
    end
    User->>System: RPCReqBatch
  end

  alt Optional BatchFlag or NoExecFlag
    User->>System: BatchFlag / NoExecFlag
  end

  Note over User, System: RPCRequest ends
```

### NameLenProcID

```mermaid
sequenceDiagram
  participant User
  participant System

  alt ProcName
    User->>System: ProcName (US_VARCHAR)
  else ProcIDSwitch
    User->>System: ProcIDSwitch (%xFF %xFF)
    User->>System: ProcID
  end
```

### OptionFlags

```mermaid
sequenceDiagram
  participant User
  participant System

  User->>System: fWithRecomp (BIT)
  User->>System: fNoMetaData (BIT)
  User->>System: fReuseMetaData (BIT)
  User->>System: 13FRESERVEDBIT
```

### StatusFlags

```mermaid
sequenceDiagram
  participant User
  participant System

  User->>System: fByRefValue (BIT)
  User->>System: fDefaultValue (BIT)
  User->>System: 1FRESERVEDBIT
  User->>System: fEncrypted (BIT)
  User->>System: 4FRESERVEDBIT
```

### ParamMetaData

```mermaid
sequenceDiagram
  participant User
  participant System

  User->>System: B_VARCHAR
  User->>System: StatusFlags
  alt TYPE_INFO
    User->>System: TYPE_INFO
  else TVP_TYPE_INFO
    User->>System: TVP_TYPE_INFO
  end
```

### ParamCipherInfo

```mermaid
sequenceDiagram
  participant User
  participant System

  User->>System: TYPE_INFO
  User->>System: EncryptionAlgo (BYTE)
  opt AlgoName
    User->>System: AlgoName (B_VARCHAR)
  end
  User->>System: EncryptionType (BYTE)
  User->>System: DatabaseId (ULONG)
  User->>System: CekId (ULONG)
  User->>System: CekVersion (ULONG)
  User->>System: CekMDVersion (ULONGLONG)
  User->>System: NormVersion (BYTE)
```

### ParameterData

```mermaid
sequenceDiagram
  participant User
  participant System

  User->>System: ParamMetaData
  User->>System: ParamLenData (TYPE_VARBYTE)
  opt ParamCipherInfo
    User->>System: ParamCipherInfo
  end
```

### RPCReqBatch

```mermaid
sequenceDiagram
  participant User
  participant System

  User->>System: NameLenProcID
  User->>System: OptionFlags
  loop Zero or more times
    User->>System: EnclavePackage (L_VARBYTE)
  end
  loop Zero or more times
    User->>System: ParameterData
  end
```
