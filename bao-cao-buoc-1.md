# Bao Cao Buoc 1 - Thuc Nghiem Cuc Bo va Theo Doi Thi Nghiem

## 1. Muc tieu

Muc tieu cua buoc 1 la huan luyen mo hinh `RandomForestClassifier` voi nhieu bo sieu tham so khac nhau, theo doi ket qua bang MLflow, so sanh cac lan chay va chon bo sieu tham so co ket qua tot nhat de su dung cho cac buoc tiep theo.

## 2. Chuan bi moi truong va du lieu

Moi truong duoc cai dat bang Python 3.11 va cac thu vien trong `requirements.txt`, bao gom `mlflow`, `scikit-learn`, `pandas`, `pyyaml` va `joblib`.

Du lieu duoc tao bang lenh:

```powershell
python generate_data.py
```

Ket qua tao du lieu:

- `train_phase1.csv`: 2998 mau
- `eval.csv`: 500 mau
- `train_phase2.csv`: 2998 mau

MLflow duoc cau hinh su dung SQLite cuc bo:

```env
MLFLOW_TRACKING_URI=sqlite:///mlflow.db
MLFLOW_ARTIFACT_ROOT=./mlartifacts
```

## 3. Qua trinh huan luyen

File `src/train.py` da duoc hoan thien de thuc hien cac cong viec sau:

- Doc du lieu huan luyen tu `data/train_phase1.csv`.
- Doc du lieu danh gia tu `data/eval.csv`.
- Tach dac trung va nhan tu cot `target`.
- Huan luyen mo hinh `RandomForestClassifier` voi tham so trong `params.yaml`.
- Tinh `accuracy` va `f1_score` tren tap danh gia.
- Ghi tham so, metric va model vao MLflow.
- Luu ket qua vao `outputs/metrics.json`.
- Luu mo hinh vao `models/model.pkl`.

## 4. Ket qua thi nghiem

Da thuc hien tong cong 6 lan chay thi nghiem tren MLflow. Cac ket qua chinh:

- Lan chay voi `n_estimators=100`, `max_depth=5`, `min_samples_split=2` dat accuracy `0.564`.
- Lan chay voi `n_estimators=100`, `max_depth=3`, `min_samples_split=2` dat accuracy `0.558`.
- Lan chay voi `n_estimators=200`, `max_depth=10`, `min_samples_split=5` dat accuracy `0.644`.
- Lan chay voi `n_estimators=300`, `max_depth=None`, `min_samples_split=2` dat accuracy `0.682`.
- Lan chay voi `n_estimators=500`, `max_depth=None`, `min_samples_split=5` dat accuracy `0.680`.

Bo sieu tham so tot nhat duoc chon:

```yaml
n_estimators: 300
max_depth: null
min_samples_split: 2
```

Ket qua cua bo tham so tot nhat:

- Accuracy: `0.682`
- F1-score: `0.6810790629331478`

## 5. Minh chung MLflow UI

Anh chup MLflow UI cho thay co 6 lan chay, da hien thi cac cot `accuracy`, `f1_score` va cac sieu tham so cua mo hinh.

![MLflow UI Ket Qua Thi Nghiem](file:///C:/Users/Admin/.cursor/projects/d-Documents-GitHub-Day21-2A202600705-NguyenMinhHieu/assets/c__Users_Admin_AppData_Roaming_Cursor_User_workspaceStorage_74e96d3045ed4afb0c9a922cad825eca_images_image-c1ca53fe-afb6-447f-9cbb-c951cda9f732.png)

## 6. Ket luan

Sau khi so sanh cac lan chay tren MLflow, bo tham so `n_estimators=300`, `max_depth=None`, `min_samples_split=2` cho ket qua tot nhat voi accuracy `0.682`. File `params.yaml` da duoc cap nhat theo bo tham so nay. Cac file can thiet cho buoc tiep theo cung da duoc tao, bao gom `outputs/metrics.json` va `models/model.pkl`.
