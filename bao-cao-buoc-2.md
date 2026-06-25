# Bao Cao Buoc 2 - Pipeline CI/CD Tu Dong

## 1. Muc tieu

Muc tieu cua buoc 2 la xay dung pipeline CI/CD tu dong de kiem thu, huan luyen, danh gia va trien khai mo hinh neu accuracy dat nguong yeu cau.

Do tai khoan GCP chua kich hoat billing, bai lam duoc chuyen sang AWS:

- Google Cloud Storage duoc thay bang Amazon S3.
- Compute Engine VM duoc thay bang Amazon EC2.
- `google-cloud-storage` duoc thay bang `boto3`.
- DVC remote duoc cau hinh voi `s3://`.

## 2. Tai Nguyen AWS

Da tao cac tai nguyen sau:

- S3 bucket: `mlops-hieu-day21-024539789132`
- EC2 public IP: `13.214.159.124`
- EC2 user: `ubuntu`
- Region: `ap-southeast-1`

Du lieu DVC da duoc push len S3 tai prefix:

```text
s3://mlops-hieu-day21-024539789132/dvc/
```

Model moi nhat duoc upload len:

```text
s3://mlops-hieu-day21-024539789132/models/latest/model.pkl
```

## 3. Cac Thanh Phan Da Xay Dung

File `src/train.py` duoc cap nhat de:

- Doc du lieu huan luyen va danh gia.
- Su dung them `train_phase2.csv` khi chay huan luyen mac dinh.
- Huan luyen `RandomForestClassifier`.
- Ghi metric vao MLflow.
- Luu `outputs/metrics.json`.
- Luu `models/model.pkl`.

File `src/serve.py` duoc tao de:

- Tai model tu S3 khi server khoi dong.
- Cung cap endpoint `GET /health`.
- Cung cap endpoint `POST /predict`.

File `.github/workflows/mlops.yml` gom 4 jobs:

- `Unit Test`: chay unit tests.
- `Train`: pull data bang DVC, train model va upload model len S3.
- `Eval`: kiem tra accuracy dat nguong `>= 0.70`.
- `Deploy`: SSH vao EC2, restart service va kiem tra `/health`.

## 4. Ket Qua Pipeline

Pipeline da chay thanh cong tren GitHub Actions:

- `Unit Test`: success
- `Train`: success
- `Eval`: success
- `Deploy`: success

Run URL:

```text
https://github.com/minhhieu29/Day21-2A202600705-NguyenMinhHieu/actions/runs/28157028351
```

Minh chung GitHub Actions:

![GitHub Actions Pipeline Success](file:///C:/Users/Admin/.cursor/projects/d-Documents-GitHub-Day21-2A202600705-NguyenMinhHieu/assets/c__Users_Admin_AppData_Roaming_Cursor_User_workspaceStorage_74e96d3045ed4afb0c9a922cad825eca_images_image-9b65c3a4-9879-40d2-bfb7-e5e458b854f5.png)

## 5. Kiem Tra API Sau Deploy

Endpoint health check:

```powershell
curl http://13.214.159.124:8000/health
```

Ket qua:

```json
{"status":"ok"}
```

Endpoint predict:

```powershell
$body = '{"features": [7.4, 0.70, 0.00, 1.9, 0.076, 11.0, 34.0, 0.9978, 3.51, 0.56, 9.4, 0]}'
Invoke-RestMethod -Uri 'http://13.214.159.124:8000/predict' -Method Post -ContentType 'application/json' -Body $body
```

Ket qua quan sat:

```json
{"prediction": 2, "label": "cao"}
```

## 6. Ket Luan

Pipeline CI/CD da hoan thanh day du 4 buoc: test, train, eval va deploy. Mo hinh duoc luu tren S3, server FastAPI chay tren EC2 va endpoint `/health` tra ve trang thai hop le. Anh GitHub Actions cho thay ca 4 jobs deu thanh cong, dap ung yeu cau cua Buoc 2.
