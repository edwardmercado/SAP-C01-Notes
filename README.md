# AWS Solutions Architect - Professional (SAP-C01) - Notes
---

### Security Token Service (STS)
- Generates __Temporary__ Credentials. 
- Similar to access keys but they __EXPIRE__ and do not belong to the identity.
- Limited Access.
- Use to access AWS Resources.
- Requested by an Identity (AWS or External).

### Revoking Temporary Credentials

What will you do if temporary credentials leaked and compromised? 

- You can delete the role but it will impact __ALL__ identity that assume that role. 
- You can change the __TRUST__ policy but it has no impact to the existing credentials that has been generated.
- Changing the __PERMISSIONS__ policy will impact __ALL__ credentials.

**Resolution:**  
Use __Revoke Sessions__ in the role,  this will add an *AWSRevokeOlderSessions* inline policy onto that IAM role which will __DENY__ sessions in a point in time and because of this the credentials that has been generated will be expired and only the identities included in the __trust policy__ can renew the role.

### IAM Permissions Boundary
Use to limit what permissions an identity can receive. 
**NOTE:** They do not grant access.

### Resource Access Manager (RAM)
- Use to **share** AWS resources between AWS accounts.
  - Products need to be supported by RAM.
- This can be shared with Principals (e.g. Accounts, Organization Units or ORG's).
- Shared resources can be accessed natively - as if they are in the same network.
- No charges for using the service - only the underlying services that it leverages.





    