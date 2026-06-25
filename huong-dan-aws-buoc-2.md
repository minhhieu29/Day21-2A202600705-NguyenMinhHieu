# Huong Dan Buoc 2 Bang AWS

File nay thay the phan GCP trong `tasks/buoc-2.md` bang AWS.

## 1. Chuan Bi AWS

Can co tai khoan AWS da kich hoat billing. Cai AWS CLI:

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

Kiem tra:

```powershell
aws --version
```

Cau hinh credentials:

```powershell
aws configure
```

Nhap:

- `AWS Access Key ID`
- `AWS Secret Access Key`
- Region, vi du: `ap-southeast-1`
- Output format: `json`

## 2. Tao S3 Bucket

Dat ten bucket duy nhat:

```powershell
$BUCKET="mlops-hieu-2026"
$REGION="ap-southeast-1"
```

Tao bucket:

```powershell
aws s3 mb s3://$BUCKET --region $REGION
```

## 3. Cau Hinh DVC Remote S3

Trong project:

```powershell
.\.venv\Scripts\activate
dvc init
dvc remote add -d myremote s3://$BUCKET/dvc
dvc add data/train_phase1.csv
dvc add data/eval.csv
dvc add data/train_phase2.csv
dvc push
```

Sau buoc nay, commit cac file `.dvc`, `.dvc/config` va `.gitignore`.

## 4. Tao EC2 Instance

Tao EC2 Ubuntu 22.04 tren AWS Console:

- AMI: Ubuntu Server 22.04 LTS
- Instance type: `t2.micro` hoac `t3.micro`
- Security Group: mo cong `22` va `8000`
- Tao hoac chon key pair SSH

Luu lai:

- Public IPv4 address
- Username mac dinh cua Ubuntu: `ubuntu`
- File private key `.pem`

## 5. Cau Hinh EC2

SSH vao EC2:

```powershell
ssh -i path\to\your-key.pem ubuntu@EC2_PUBLIC_IP
```

Trong EC2:

```bash
sudo apt update
sudo apt install -y python3-pip
pip3 install fastapi uvicorn scikit-learn joblib boto3
mkdir -p ~/models ~/src
```

Copy `src/serve.py` len EC2 tu may local:

```powershell
scp -i path\to\your-key.pem src/serve.py ubuntu@EC2_PUBLIC_IP:~/src/serve.py
```

## 6. Tao IAM User Cho GitHub Actions Va EC2

Trong AWS Console, tao IAM User co quyen toi thieu cho S3 bucket. Policy mau:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::YOUR_BUCKET_NAME",
        "arn:aws:s3:::YOUR_BUCKET_NAME/*"
      ]
    }
  ]
}
```

Tao access key cho user nay. Luu lai:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`

## 7. Systemd Service Tren EC2

SSH vao EC2 va tao service:

```bash
sudo tee /etc/systemd/system/mlops-serve.service > /dev/null <<EOF
[Unit]
Description=MLOps Model Inference Server
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu
Environment="CLOUD_BUCKET=YOUR_BUCKET_NAME"
Environment="AWS_ACCESS_KEY_ID=YOUR_AWS_ACCESS_KEY_ID"
Environment="AWS_SECRET_ACCESS_KEY=YOUR_AWS_SECRET_ACCESS_KEY"
Environment="AWS_DEFAULT_REGION=ap-southeast-1"
ExecStart=/usr/bin/python3 /home/ubuntu/src/serve.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable mlops-serve
```

Chua can start service neu model chua duoc pipeline upload len S3.

## 8. GitHub Secrets

Vao GitHub repo > Settings > Secrets and variables > Actions > New repository secret.

Them cac secrets:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION`, vi du `ap-southeast-1`
- `CLOUD_BUCKET`
- `VM_HOST`: public IP cua EC2
- `VM_USER`: `ubuntu`
- `VM_SSH_KEY`: noi dung private key dung SSH vao EC2

## 9. Commit Va Push

```powershell
git add .
git commit -m "feat: switch mlops pipeline to AWS"
git push origin main
```

Mo tab Actions tren GitHub de xem pipeline.

## 10. Test API

Sau khi pipeline thanh cong:

```powershell
curl http://EC2_PUBLIC_IP:8000/health
```

Ket qua dung:

```json
{"status":"ok"}
```

Test predict:

```powershell
curl -X POST http://EC2_PUBLIC_IP:8000/predict -H "Content-Type: application/json" -d "{\"features\": [7.4, 0.70, 0.00, 1.9, 0.076, 11.0, 34.0, 0.9978, 3.51, 0.56, 9.4, 0]}"
```
