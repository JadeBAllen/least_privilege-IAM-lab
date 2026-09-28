# least_privilege-IAM-lab
#Least Privilege Policy correction in AWS

## The Objective
A user was mistakenly given full admin access when they only needed to read files in a single S3 bucket. I identified the overprivileged user, removed admin access, and moved them into group with a scoped policy, applying the principle of Least Privilege.

## Setup
**jade-admin**: my admin account. Is protected with MFA. (IAM USER, root user is not used)
**S3-ReadOnlyLab** (group): one policy attached, 'S3-ReadOnlyOneBucket'
**dev-user**: the correctly scoped member if S3-ReadOnly-Lab
**overprivileged-user**: mistakenly given 'AdminstratorAccess' (purposely)

## The Policy
'S3-ReadOnly-OneBucket' is a JSON policy that allows only:
- 's3:ListAllMyBuskets': see all bucket names in the console
- 's3:ListBucket' list files in one bucket
- 's3:GetObject': download files from that bucket
Everything else, such as uploading, deleting, or using other services, is blocked by AWS's implicit deny
policy: [policy.json] [Policy script.pdf](https://github.com/user-attachments/files/32727391/Policy.script.pdf)



## Testing (as dev-user)
| View and download helloworld.txt | Allowed | [helloworld dev access.pdf](https://github.com/user-attachments/files/32726847/helloworld.dev.access.pdf)
| Delete a file | Access Denied | [Dev Denied.pdf](https://github.com/user-attachments/files/32726853/Dev.Denied.pdf)
| Upload a file | Access Denied | [Upload Denied.pdf](https://github.com/user-attachments/files/32726859/Upload.Denied.pdf)


## Remediation
1. Reviewed overprivileged-user's Last Accessed tab: admin rights, but almost no services were used [Admin access proof.pdf](https://github.com/user-attachments/files/32727122/Admin.access.proof.pdf)

2. Removed 'AdministratorAccess'[Detele Admin Access.pdf](https://github.com/user-attachments/files/32726990/Detele.Admin.Access.pdf)
3. Added the user to 'S3-ReadOnly-Lab'
4. Verified the user now has only 'S3-ReadOnly-OneBucket', attached through the group [S3 group.pdf](https://github.com/user-attachments/files/32727267/S3.group.pdf)


## Lessons Learned
-Assigning permissions through groups makes access easier to manage and review
-AWS denies anything not explicitly allowed (implicit deny)
-Least privilege has to be tested, not just configured
-if the policy's syntax is off the user (dev-user) will not be able to download or read documents within a bucket. An error a page will appear. I ensured that there were no errors in the syntax before applying to the group persmissions.
