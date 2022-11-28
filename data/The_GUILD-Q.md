
#Issue:  Gas Optimization

#Context: Register | L97, Assign | L114

#Description: 
The method of initializing the struct values with its parameters at L97 & L114 consumes much gas on every function call. 

#Recommendation:
 I suggest that the procedural way of initializing a struct variable will be adopted. It is proven by practice that this method saves 90 units of gas at every function call and this method is beneficial to every user calling the function. Reference. 
