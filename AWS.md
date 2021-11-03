## S3
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
