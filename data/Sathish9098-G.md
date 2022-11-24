##   [G1]  _tokenIdTracker STATE VARIBALE SHOULD BE CACHED WITH STACK VARIBALE . STACK VARIABLE GAS FEE IS VERY LOW COMPARE TO ACCESSING ON CHAIN DATA 

>  FILE :  2022-11-canto/CIP-001/src/Turnstile.sol

          91:  tokenId = _tokenIdTracker.current();

          93:  _tokenIdTracker.increment();

  ## 

## [G2]   WHEN EVER USE  || OPERATOR DON'T WANT TO CHECK MULTIPLE CONDITIONS. IF ANY ONE CONDITION IS TRUE THEN OVER ALL CONDITION CHECKS BECOME TRUE EVEN OTHER CONDITIONS FALSE. SO DON'T WANT TO CHECK ALL CONDITIONS. FOR EVERY CONDITION CHECKS NEED TO PAY GAS FEE. 

> > FILE :  2022-11-canto/CIP-001/src/Turnstile.sol

     134:   if (earnedFees == 0 || _amount == 0) revert NothingToWithdraw();

##

##[G3]  ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW

>  FILE :  2022-11-canto/CIP-001/src/Turnstile.sol

   137:   balances[_tokenId] = earnedFees - _amount;

  ##

## [G4]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

>  FILE :  2022-11-canto/CIP-001/src/Turnstile.sol

     151:   balances[_tokenId] += msg.value;
     
   

     