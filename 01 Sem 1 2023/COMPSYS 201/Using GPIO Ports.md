### Settings and Clearing registers
>[!info] Setting N-th bit of Registers
The register is updated using bitwise OR. All bits are 0 except for the bit to be updated which is 1
>

>[!info] Clearing N-th bit of Registers
The register is updated using bitwise AND. All bits are 1 except for the bit to be updated which is 0
>

### Checking Registers
>[!info] Checking if the n-th bit is 1
We check the result using the bitwise AND. all bits are 0 except the bit which we are checking. 
If the result is non-zero then the n-th bit is 1

>[!info] Checking if n-th bit is zero
We check using bitwise AND on the **negate** of the register. Same process as checking for 1


