# AWS Solutions Architect - Professional (SAP-C01) - Notes

### Security Token Service (STS)
- Generates __Temporary__ Credentials. 
- Similar to access keys but they __EXPIRE__ and do not belong to the identity.
- Limited Access.
- Use to access AWS Resources.
- Requested by an Identity (AWS or External).

### Revoking Temporary Credentials


- What if temporary credentials leaked? 
    - You can delete the role but it will impact __ALL__ identity that assume that role. 
    - You can change the __TRUST__ policy but it has no impact to the existing credentials that has been generated.
    - Changing the __PERMISSIONS__ policy will impact __ALL__ credentials.


- To resolve this issue, you need to add a conditional inline policy which will __DENY__ sessions in a point in time. 
    - 
