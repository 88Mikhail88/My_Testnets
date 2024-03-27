### BUG REPORT from release ```namada-v0.32.1```

#### Full node cant start without priv_validator_state.json file and crashes, even it never had validator on it, so to resolve the issue it is necessary to create the file with default settings. 

```bash
{
  "height": "0",
  "round": 0,
  "step": 0
}
```

#### Logs report:

```bash
The application panicked (crashed).
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]: Message:  Tendermint failed to initialize with Output {
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]:     status: ExitStatus(
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]:         unix_wait_status(
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]:             256,
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]:         ),
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]:     ),
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]:     stdout: "I[2024-03-27|17:44:29.895] deprecated usage found in configuration file usage=\"[fastsync] table detected. This section has been renamed to [blocksync]. The values in this deprecated section will be disregarded.\"\nI[2024-03-27|17:44:29.895] deprecated usage found in configuration file usage=\"fast_sync key detected. This key has been renamed to block_sync. The value of this deprecated key will be disregarded.\"\nopen /root/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data/priv_validator_state.json: no such file or directory\n",
Mar 27 17:44:29 Ubuntu-2204-jammy-amd64-base namada[2237960]:     stderr: "",
```
