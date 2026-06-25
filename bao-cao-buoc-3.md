# Bao Cao Buoc 3 - Huan Luyen Lien Tuc Khi Co Du Lieu Moi

## 1. Muc tieu

Muc tieu cua buoc 3 la mo phong quy trinh them du lieu moi, cap nhat phien ban du lieu bang DVC va de GitHub Actions tu dong huan luyen lai, danh gia lai va trien khai lai model ma khong can thao tac deploy thu cong.

## 2. Cap Nhat Du Lieu

Script `add_new_data.py` duoc dung de ghep `train_phase2.csv` vao `train_phase1.csv`.

Ket qua sau khi cap nhat:

```text
Cap nhat du lieu: 2998 -> 5996 mau
```

Sau do file `data/train_phase1.csv` duoc cap nhat lai bang DVC:

```powershell
dvc add data/train_phase1.csv
dvc push
```

Commit kich hoat pipeline:

```text
data: refresh train data pointer for continuous training
```

Commit nay chi thay doi file con tro DVC:

```text
data/train_phase1.csv.dvc
```

## 3. Pipeline Tu Dong

Sau khi push commit du lieu len GitHub, workflow `MLOps Pipeline` duoc kich hoat tu dong boi su kien `push`.

Run URL:

```text
https://github.com/minhhieu29/Day21-2A202600705-NguyenMinhHieu/actions/runs/28158039507
```

Ket qua cac jobs:

- `Unit Test`: success
- `Train`: success
- `Eval`: success
- `Deploy`: success

Minh chung GitHub Actions:

![GitHub Actions Continuous Training Success](file:///C:/Users/Admin/.cursor/projects/d-Documents-GitHub-Day21-2A202600705-NguyenMinhHieu/assets/c__Users_Admin_AppData_Roaming_Cursor_User_workspaceStorage_74e96d3045ed4afb0c9a922cad825eca_images_image-e9bb5f64-cb4f-4ec8-b339-469925e8c0d8.png)

Dieu nay chung minh pipeline da phan ung voi thay doi du lieu DVC va tu dong chay lai toan bo quy trinh CI/CD.

## 4. So Sanh Ket Qua

| Chi so | Buoc 2 | Buoc 3 |
|---|---:|---:|
| accuracy | 0.762 | 0.750 |
| f1_score | 0.760981396354435 | 0.7493264289196405 |

Ket qua Buoc 3 thap hon nhe so voi Buoc 2, nhung van dat nguong `accuracy >= 0.70`, nen job `Eval` cho phep tiep tuc va job `Deploy` da trien khai model moi thanh cong.

## 5. Xac Nhan API Sau Deploy

Endpoint health check sau khi pipeline Buoc 3 hoan thanh:

```powershell
curl http://13.214.159.124:8000/health
```

Ket qua:

```json
{"status":"ok"}
```

## 6. Ket Luan

Buoc 3 da hoan thanh: khi du lieu moi duoc cap nhat va file `.dvc` duoc push len GitHub, pipeline tu dong chay lai cac buoc test, train, eval va deploy. Server FastAPI tren EC2 tiep tuc hoat dong binh thuong sau khi deploy model moi.
