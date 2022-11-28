Unnecessary caching waste gas

```
CIP-001/src/Turnstile.sol:
  49      modifier onlyUnregistered() {
  50:         address smartContract = msg.sender;  //@audit unnecessary caching
  51  
  52          if (isRegistered(smartContract)) revert AlreadyRegistered();
```

msg.sender is used only once, and caching it into a new variable is gas wasteful