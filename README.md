# AWS Solutions Architect - Professional (SAP-C01) - Notes

## Security Token Service (STS)
- Generates __Temporary__ Credentials. 
- Similar to access keys but they __EXPIRE__ and do not belong to the identity.
- Limited Access.
- Use to access AWS Resources.
- Requested by an Identity (AWS or External).

## Revoking Temporary Credentials

What will you do if temporary credentials leaked and compromised? 

- You can delete the role but it will impact __ALL__ identity that assume that role. 
- You can change the __TRUST__ policy but it has no impact to the existing credentials that has been generated.
- Changing the __PERMISSIONS__ policy will impact __ALL__ credentials.

**Resolution:**  
Use __Revoke Sessions__ in the role,  this will add an *AWSRevokeOlderSessions* inline policy onto that IAM role which will __DENY__ sessions in a point in time and because of this the credentials that has been generated will be expired and only the identities included in the __trust policy__ can renew the role.

### IAM Permissions Boundary
Use to limit what permissions an identity can receive. 
**NOTE:** They do not grant access.

## Resource Access Manager and Availability Zone IDs

![AWS RAM](https://raw.githubusercontent.com/edwardmercado/SAP-C01-Notes/main/images/01%20-%20Advance%20Permissions%20%26%20Accounts/AWS%20RAM.PNG)

### Resource Access Manager (RAM)
- Use to **share** AWS resources between AWS accounts.
  - Products need to be supported by RAM.
- This can be shared with Principals (e.g. Accounts, Organization Units or ORG's).
- Shared resources can be accessed natively - as if they are in the same network.
- No charges for using the service - only the underlying services that it leverages.

### Availability Zone IDs (AZ IDs)
- This are unique **identifiers** for a given Availability Zone.
- AWS rotates physical facilities that are used by Availability Zones and this are not consistent in each AWS account. For example `us-east-1a` in an AWS account is not the same as other AWS account `us-east-1a`.
    - This may cause issue when sharing resources to other AWS account.
 - To resolve this, AWS used **AZ IDs** as an identifier for a specific Availability Zone and this is utilize by AWS RAM.

#### AWS RAM and AZ IDs side by side
- Owner account creates a share. Utilizes AZ IDs to identify on which resources will be shared.
    - Owner will retain full ownership of the resources. 
- Defines a principal to whom to share.
    - **IF** participant is inside an AWS Organization with Sharing **enabled**, resouces will be automatically shared.
    - **IF** participant is **NOT** inside an AWS Organization OR AWS Organization Sharing is **disabled**, principal need to accept the invite.

**NOTES:**
- Shared resouces using AWS RAM cannot be modified or deleted by the account to whom you shared your resources.
    - Shared principals can interact with the resources but cannot edit or modify them.
- AWS Resources that has been created by the owner account or each member account are cannot be seen by other accounts except to the account where they are created.
- You **cannot** share VPC using AWS RAM but you can share the resources inside the VPC (e.g. Subnets, etc.). See list [here](https://docs.aws.amazon.com/ram/latest/userguide/shareable.html).

## AWS Service Quotas
- Each service has a default per region quota.
    - Some service quota can be per account.
- Most service quotas can be increased as needed.
- Some cannot be increased due to architectural impacts.
- High quota increase == more processing and time needed.

**NOTES:**
- AWS Service Quotas can be also used to request to increase service quotas in each service.
    - This also has Quota request template for **AWS Organization** to request a **bulk** increase for your AWS Organization member accounts.
- You can build an CloudWatch Alarm based on a particular service quota.

## Security Assertion Markup Language (SAML) 2.0 Identity Federation
- SAML 2.0 is an open standard for many Identity Providers (e.g MS ADFS, etc.)
- Mostly used for Enterprise Identity Provider - SAML 2.0 Compatible.
- This is used when you **already have** an existing identity management team.
- Single source of truth - can hanlde more than 5000 users.
- Leverages IAM Role and AWS Temporary Credentials (STS) with 12 hour of validity by default.

### SAML 2.0 Identity Federation using API
![SAML 2.0 - API](https://github.com/edwardmercado/SAP-C01-Notes/blob/main/images/02%20-%20Advance%20Identity%20&%20Federation/SAML%202.0%20Identity%20Federation%20-%20API.PNG?raw=true)

### SAML 2.0 Identity Federation using Console
![SAML 2.0 - API](https://github.com/edwardmercado/SAP-C01-Notes/blob/main/images/02%20-%20Advance%20Identity%20&%20Federation/SAML%202.0%20Identity%20Federation%20-%20Console.PNG?raw=true)

**NOTES:**
- For API, you need to establish **TRUST** between your SAML 2.0 Compatible Identity Provider and IAM
- For Console, you need to establish **TRUST** between your SAML 2.0 Compatible Identity Provider and the SAML/SSO Endpoint.
- The Client Identity Provider Portal where you authenticate identifies the **roles** which are available for the authenticating user.
- Uses *STS:AssumeRoleWithSAML* that exchanges SAML assertion with temporary credentials that will be use to access an AWS service or the AWS console.

## AWS Single Sign-On (SSO)
- Managed SSO access.
    - Can be use for AWS accounts and External applications.
- Flexible Identity Source, you can use:
    - AWS SSO built-in Identity Source
    - AWS Managed Microsoft AD
    - On-premises Microsoft AD (using 2-way trust (AWS Managed Microsoft AD) or AD connector)
    - External Identity Provider (SAML 2.0)
- Preferred by AWS vs Traditional *workforce* identity federation.

![SSO](https://github.com/edwardmercado/SAP-C01-Notes/blob/main/images/02%20-%20Advance%20Identity%20&%20Federation/AWS%20SSO.PNG?raw=true)

**NOTES:**
- Requirement of AWS SSO is you need to have AWS Organization enabled.
- **Permission Sets** - are way to define role-based permissions that can be assigned to an user or a group over a certain AWS account.
    - You can use existing or custom permission sets.

## AWS Cognito
- Handles authentication, authorization and user management.
    - use for Web and Mobile applications.
- You can login directly to Cognito or other identity providers. 
- Handles the exchange for AWS credentials.
- Access AWS resources using credentials.
- Handle Data Synchornization, Identity Merging, Managed Login UI.
    - Data Synchornization is useful when you access an application using multiple devices as it sync's the data on all of those devices.

### Cognito User Pool
- Use for **authentication**, users can sign in through the user pool or federate through a third-party identity provider (IdP).
- Authenticate using federated identities (e.g. FB, Google, etc.) and a successful authentication can return a **JSON Web Token** (JWT).
    - JWT will be send to Identity Pool and the Identity Pool swaps these JWT with temporary AWS credentials via *AssumeRole*.
        - These credentials will be use to access AWS resources.

### Cognito Identity Pool
- Use for **authorization**, you can use identity pools to create unique identities for users and give them access to other AWS services.
- Can be used on both authenticated and unauthenticated identities.
    - These identities will have separate role each.
        - These roles are created during Identity Pool creation.
        - These roles can be modified later or after creation.

## Amazon Workspaces
- Desktop-As-A-Service - primarily use for Home-working/Office.
- Similar to Citrix / Remote Desktop - but it's hosted by AWS.
- Consistent desktop from anywhere - apps and state.
- Can provision Windows and Linux desktop comes with various sizes.
- Monthly or Hourly Pricing (+ base infra costs).
- Uses Directory Service (Simple AD, Managed AD, AD Connector) for authentication and user management.
    - The Directory Service is a requirement when using Workspaces.
- Windows Workspaces can access AWS FSx and EC2 Windows Resources.
    - It can also access on-premises resources over VPN or Dirrect Connect (DX)
- Provides EBS-based volumes (can be encrypted at rest).

![Workspaces](https://raw.githubusercontent.com/edwardmercado/SAP-C01-Notes/main/images/02%20-%20Advance%20Identity%20%26%20Federation/AmazonWorkspaces.png)

**NOTES:**
- Workspaces and Directory Services run in *AWS Managed VPC* (controlled by AWS - not customer managed) and use **Elastic Network Interfaces (ENIs)** that will be injected into customer-managed VPCs.
- Workspaces are not highly-available - uses ENI that only occupies a single subnet or AZ.
- Workspaces has 2 running modes:
    - **AlwaysOn** - billed monthly. Always running Workspaces.
    - **AutoStop** - billed hourly. Start Workspaces when you login then stops when no longer being used.

## AWS Directory Services

### Managed Microsoft AD
- Built using Microsoft Active Direcotry 2012 R2
- Managed using Standard Active Directory Tools.
- Supports Group policy and Single Sign-On (SSO).
- **Supports Schema Extension** - MS AD Aware Apps.
    - Such as SharePoint, SQL, Distributed File System (DFS).
- Has 2 Sizes:
    - Standard - can store up to 30,000 Objects.
    - Enterprise - can store up to 500,000 Objects.
- Used for AD Authentication or Authorization of products and services within AWS.
- Highly available by default - provision 2 Domain Controllers that expands to 2 or more AZs.
- Includes monitoring, recovery, replication, snapshots and maintenance that is configurable and managed by AWS.
    - Includes automatic patching and maintenance.
- **Supports one-way and two-way** external and forest with on-premise active directory
- Can **operate independently**. It can work through network link failure to any connected on-premises systems.
- **Supports RADIUS-based MFA**
    - Can also be integrated with on-premises RADIUS server.
- Best choice if you need to establish **TRUST** relationship between AWS and you On-premises directories.
- The on-premises directory and Microsoft Managed Directory are 2 separate directories where you can establish either **one-way** or **two-way** trust.





 




    