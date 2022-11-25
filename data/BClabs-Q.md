1. ERC721 symbol should be abbreviation (usually 3-4 characters long)

2. To keep the feeRecipient mapping clean, you could check that the msg.sender is really a contract (using assembly extcodesize)