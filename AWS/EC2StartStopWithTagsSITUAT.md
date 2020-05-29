{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ec2:StartInstances",
                "ec2:StopInstances"
            ],
            "Resource": "arn:aws:ec2:us-east-1:203862109330:instance/*",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/ServerCategory": [
                        "Client-UAT",
                        "Client-SIT",
                        "Internal-DeveloperEnv"
                    ]
                }
            }
        }
    ]
}
