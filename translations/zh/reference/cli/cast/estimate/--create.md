# cast estimate --create

估算部署智能合约的 Gas 成本

```bash
$ cast estimate --create --help
```

```txt
用法： cast estimate --create [OPTIONS] <CODE> [SIG] [ARGS]...

参数：
  <CODE>
          The bytecode of contract

  [SIG]
          The signature of the constructor

  [ARGS]...
          Constructor arguments

选项：
      --value <VALUE>
          Ether to send in the transaction
          
          Either specified in wei, or as a string with a unit type:
          
          Examples: 1ether, 10gwei, 0.01ether

  -h, --help
          Print help (see a summary with '-h')
```