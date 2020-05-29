{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:GetLifecycleConfiguration",
                "s3:GetObjectVersionTagging",
                "s3:ListBucketVersions",
                "s3:ReplicateTags",
                "s3:RestoreObject",
                "s3:ListBucket",
                "s3:ReplicateObject",
                "s3:GetObjectAcl",
                "s3:PutBucketTagging",
                "s3:GetObjectVersionAcl",
                "s3:GetObjectTagging",
                "s3:PutObjectTagging",
                "s3:PutBucketVersioning",
                "s3:PutObjectAcl",
                "s3:GetObjectRetention",
                "s3:PutObjectVersionTagging",
                "s3:PutObjectLegalHold",
                "s3:PutBucketCORS",
                "s3:GetObjectLegalHold",
                "s3:PutObject",
                "s3:GetObject",
                "s3:DescribeJob",
                "s3:PutObjectRetention",
                "s3:PutObjectVersionAcl",
                "s3:GetObjectVersionForReplication",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::dvara-data-backups",
                "arn:aws:s3:ap-south-1:203862109330:job/*",
                "arn:aws:s3:::dvara-data-backups/*"
            ]
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:ListAllMyBuckets",
                "s3:ListJobs",
                "s3:CreateJob"
            ],
            "Resource": "*"
        }
    ]
}
