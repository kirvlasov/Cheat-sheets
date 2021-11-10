## S3
### Numbers

Value |Description
----- |-
20KiB | Maximum resource [policy size](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html). If you don't fit, [use ACL](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-policy-alternatives-guidelines.html#when-to-use-acl).
100   | Maximum number of ACL grants per object
100MiB| Recommended threshold to start using [multipart upload](https://docs.aws.amazon.com/AmazonS3/latest/userguide/qfacts.html)
5GiB  | Maximum single part size to upload (not recommended though)
160GiB| Maximum single upload size through the AWS Console
5TiB  | Maximum object size
10000  | Maximum number of parts in multipart upload
99.999999999% | Durability - probability of not losing your data per 1 year (excl. [Reduced Redundancy storage class](https://aws.amazon.com/s3/reduced-redundancy/))
99.99%| Availability by design - probability of successfully accessing your data at the particular moment (_Standard_, _Glacier_, _RRS_)
99.9% | Availability by design (_Standard-IA_, _Intelligent tiering_)
99.5% | Availability by design (_One Zone-IA_)
99.9% | Availability by [SLA](https://aws.amazon.com/s3/sla/) - threshold with real sanctions for AWS (_Standard_, _Glacier_, _RRS_)
99.0% | Availability by SLA (_Standard-IA_, _Intelligent tiering_, _One Zone-IA_)
10-100%| Credits returned to your account if uptime is [under SLA threshold](https://aws.amazon.com/legal/service-level-agreements/)
3     | Minimal number of [availability zones](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) to store your data (excl. One Zone-IA)
100   | Soft limit on bucket count
1000  | Hard limit on bucket count

- Deletion of versioned file is **creation** of _Delete marker_. This way, in some cases this marker acts as a matter of presence. E.g. recovering deleted file with enabled _MFA Delete_ requires MFA confirmation. Because you are trying to **remove** the Delete marker.
- Cross-region replication is limited to one hop. **A** -> **B**, **B** -> **C**, but not **A** -> **B** -> **C**!

## Gotchas
- Unexpected spends. (recommendations taken from [Reddit](https://www.reddit.com/r/aws/comments/qgr9jh/was_billed_60k_with_a_free_tier/hi9feb9/))
  - Billing Dashboard > Create Budget
  - Define your threshold (e.g. fixed monthly budget of $10) 
  - Set an Alert (e.g. when your actual costs exceed a specific amount of money) and let AWS notify you by email 
  - Extra safety:
    - Add an Alert Action of type "IAM Action" and apply the policy "AWSDenyAll" to all of the users you created. This way, no user in your AWS account (attention: except for the root - you can never limit the root without organizations!) can do anything that costs money.
    - May also include all roles as well, since user could assume roles) 
  - Also:
    - Never create access keys for the root user
    - Always use MFA for the root user
    - Almost never use the root user for tasks that do not require the root user
- S3 bucket deletion is absolutely irreversible (again, thanks to [Reddit](https://www.reddit.com/r/aws/comments/6ua8se/recovering_a_deleted_s3_bucket_is_there_any_way/))
  - Enable [MFA Delete](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html); here is good [hands-on article](https://blog.james.rcpt.to/2017/04/29/s3-mfa-delete/). But it blocks the use of Lifecycle Policy and complicates the legitimate deletions.
  - Use cross-region replication
  - Set up tailored policy, restricting bucket removal for non-root users
