# EC2 Instance Creation Workflow

This repository contains a GitHub Actions workflow to automate the creation of an AWS EC2 instance using parameters specified in an `ec2_values.json` file at the repository root.

## Overview

The workflow defined in [`.github/workflows/create-ec2.yml`](.github/workflows/create-ec2.yml) allows you to launch a new EC2 instance on AWS directly from your GitHub repository by triggering a workflow dispatch event.

## How It Works

- **Inputs:** The workflow reads instance parameters from `ec2_values.json` in your repo root.
- **Tools Used:** [aws-actions/configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials) for AWS authentication, AWS CLI v2, jq.
- **Process:** 
  1. Checks out the repository.
  2. Installs needed tools (AWS CLI, jq).
  3. Loads AWS credentials from repo secrets.
  4. Validates the presence of `ec2_values.json`.
  5. Parses instance parameters (AMI ID, instance type, security group(s), subnet, tags, user data, etc.).
  6. Launches an EC2 instance using these parameters.
  7. Waits for the instance to transition to "running".
  8. Outputs instance details: ID, public IP, private IP.

## Getting Started

1. **Clone this repository or add the workflow to your own.**

2. **Create an `ec2_values.json` file** at the root of the repo with the following example structure:
   ```json
   {
     "image_id": "ami-xxxxxxxx",
     "instance_type": "t2.micro",
     "key_name": "your-ssh-key",
     "security_group_ids": ["sg-xxxxxxxx"],
     "subnet_id": "subnet-xxxxxxxx",
     "tag_name": "github-created-ec2",
     "associate_public_ip": true,
     "user_data": "#!/bin/bash\necho Hello"
   }
   ```
   - Only `image_id` and `instance_type` are required. Others are optional.

3. **Add AWS credentials and region to your repository secrets:**
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_REGION`

4. **Run the workflow manually**
   - Go to the "Actions" tab in GitHub.
   - Select "Create EC2 from ec2_values.json".
   - Click "Run workflow".

## Outputs

After the workflow completes, it will display:

- `Instance`: The EC2 Instance ID created.
- `Public IP`: The public IP address, if assigned.
- `Private IP`: The private IP address.

## Security Notes

- Credentials are read from repository secrets, **never hard code sensitive data in your repository**.
- Make sure to follow best practices for IAM permissions. The provided AWS credentials should only allow EC2 creation and describe actions as needed.

## Troubleshooting & Customization

- **Missing File:** The workflow will fail if `ec2_values.json` is absent.
- **Required Parameters:** Ensure `image_id` and `instance_type` are present in the JSON.

To further customize the workflow, edit [`.github/workflows/create-ec2.yml`](.github/workflows/create-ec2.yml).

## License

MIT

---

**Author:** [@TShreyas93](https://github.com/TShreyas93)
