## 1. Prerequisites

- You own **mypurchaseddomain.com** in Route 53 hosted zone.
- Root AWS account (for simplicity) with IAM profile on laptop.

## 2. Networking

1. **VPC**: use default or create `summer‑split‑vpc` with 1 public subnet.
2. **Security Groups**:
   - `web‑sg`: inbound 80/443 (0.0.0.0/0), outbound ALL.
   - `db‑sg`: inbound MySQL 3306 from `web‑sg` only.

## 3. EC2 Setup

| Setting | Value                          |
| ------- | ------------------------------ |
| AMI     | Amazon Linux 2023 (ARM)        |
| Type    | t4g.micro (free tier‑eligible) |
| Storage | 20 GiB gp3                     |

Steps:

```bash
aws ec2 run-instances \
  --instance-type t4g.micro \
  --key-name my-ssh-key \
  --security-group-ids web-sg \
  --user-data file://bootstrap.sh
```

`bootstrap.sh` installs Node 20, Docker, docker‑compose, nginx, PM2.

### DNS

- Create **A Record** `@` pointing to EC2 Elastic IP.
- Create **CNAME** `www` → `@`.

### TLS

- AWS Certificate Manager → Request public cert `*.mypurchaseddomain.com`; validate via Route 53.
- Nginx terminates TLS with cert files pulled via ACM & `aws‑cli` cron.

## 4. Database (RDS MySQL)

```bash
aws rds create-db-instance \
  --db-instance-identifier summer-db \
  --db-instance-class db.t3.micro \
  --engine mysql \
  --master-username admin \
  --allocated-storage 20 \
  --vpc-security-group-ids db-sg
```

- Enable `publicly-accessible=false`.
- Snapshot schedule: daily.
- Use **Amazon RDS Query Editor** v2 for admin editing (click *Databases ▸ Query Editor*).

## 5. Parameter Store Secrets

```
aws ssm put-parameter --name "/summer/db/password" --value "P@ssw0rd!" --type SecureString
```

Node reads via `aws-sdk` at runtime.

## 6. CI/CD Access

- In **GitHub Settings ▸ Secrets**, add:
  - `EC2_HOST`, `EC2_USER`, `EC2_KEY` (SSH private key).
  - `SSM_DB_PASSWORD` (if needed at build).
- Add EC2 IAM role allowing `ssm:GetParameter`.

## 7. Backups & Monitoring

- Enable automated RDS snapshots (7 days).
- CloudWatch agent on EC2 pushes logs (`/var/log/nginx`, PM2).
- Budget alarm at 10 €.

## 8. Optional Terraform Snippet (later)

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.al2023.id
  instance_type = "t4g.micro"
  tags = { Name = "summer-web" }
}
```
