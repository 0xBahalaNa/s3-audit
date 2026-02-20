# S3 Audit

A Python tool that audits all S3 buckets in your AWS account for security compliance.

## Overview

This lesson builds two scripts:
1. **s3_audit.py** - Audits buckets for encryption and public access block compliance
2. **deploy_test_buckets.py** - Creates test buckets with various configurations for testing

## Requirements

- Python 3.x
- `boto3` library
- AWS CLI configured with credentials (`aws configure`)

### Install dependencies
```bash
pip install boto3
```

## Usage

### Run the audit
```bash
python s3_audit.py
```

**Sample output:**
```
Found 4 buckets.

Checking bucket: my-secure-bucket
    [PASS] Encryption: AES256
    [PASS] Public Access Block: Enabled
Checking bucket: my-partial-bucket
    [PASS] Encryption: AES256
    [WARN] Public Access Block: Partially configured
Checking bucket: my-insecure-bucket
    [PASS] Encryption: AES256
    [FAIL] Public Access Block: Not configured

========================================
Summary: 1 of 3 buckets fully compliant.
```

### Deploy test buckets (optional)
```bash
python deploy_test_buckets.py
```

Creates 3 buckets with different configurations to test the audit script:
| Bucket | Public Block | Expected Result |
|--------|--------------|-----------------|
| `grce-audit-compliant-<account_id>` | Full | PASS |
| `grce-audit-no-block-<account_id>` | None | FAIL |
| `grce-audit-partial-<account_id>` | Partial | WARN |

## Compliance Checks

### 1. Encryption
Checks if server-side encryption (SSE) is enabled on the bucket.

- **PASS** - Encryption configured (AES256 or aws:kms)
- **FAIL** - No encryption configured

### 2. Public Access Block
Checks if all four public access block settings are enabled:
- `BlockPublicAcls`
- `IgnorePublicAcls`
- `BlockPublicPolicy`
- `RestrictPublicBuckets`

- **PASS** - All 4 settings enabled
- **WARN** - Some settings enabled (partial)
- **FAIL** - No settings configured

## Cleanup

Delete test buckets when done to avoid charges:
```bash
aws s3 rb s3://grce-audit-compliant-<your_account_id>
aws s3 rb s3://grce-audit-no-block-<your_account_id>
aws s3 rb s3://grce-audit-partial-<your_account_id>
```

## Key Concepts Learned

| Concept | Description |
|---------|-------------|
| `boto3.client()` | Create AWS service clients |
| `s3.list_buckets()` | Retrieve all buckets in account |
| `s3.get_bucket_encryption()` | Check encryption settings |
| `s3.get_public_access_block()` | Check public access settings |
| `try/except` | Handle AWS API errors gracefully |
| `all()` | Check if all conditions are True |
| `sts.get_caller_identity()` | Get account info dynamically |

## GRC Engineering Application

This tool supports:
- **SOC 2** - CC6.1 (Logical Access Controls)
- **CIS AWS Benchmark** - 2.1.1 (S3 Bucket Encryption), 2.1.5 (Block Public Access)
- **NIST 800-53** - SC-28 (Protection of Information at Rest)

## Future Enhancements

- Export results to CSV/JSON
- Add timestamp to output
- Check bucket versioning
- Check bucket logging
- Filter buckets by tag
- Email alerts for non-compliant buckets
