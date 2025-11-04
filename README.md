# 2209-a

# REINVENT4 + Docking Ligand Design Pipeline

Bu pipeline, belirli bir protein bağlanma cevine yönelik **yeni ligand molekülleri tasarlar**:

1. **REINVENT4** ile QED'e göre önceden eğitilmiş bir modelle molekül üretir  
2. **ADMET**, **QED**, ve **Sentetik Erişilebilirlik (SA)** skorları ile filtreler  
3. **AutoDock Vina** ile docking yapar  
4. Kombinlenmiş bir skora göre **en iyi 3 farklı** ligandı seçer

---

## 🔧 Gereksinimler

- Python 3.8+
- RDKit
- PyTorch
- OpenBabel (`obabel`)
- AutoDock Vina
- REINVENT4 modeli

---

## 📦 Kurulum

### 1. Ortamı oluşturun (isteğe bağlı)

---

```bash
conda create -n reinvent-pipeline python=3.9
conda activate reinvent-pipeline
```


### 2. Bağımlılıkları kurun

---

```bash
pip install -r requirements.txt
```
---

### 3. REINVENT4 prior modelini indirin

---

```bash
wget https://zenodo.org/records/10870647/files/reinvent.prior -O reinvent.prior
```
---

### 4. AutoDock Vina'yı kurun
https://vina.scripps.edu
vina komutu sistem yolunda olmalı (veya --vina ile yolu verin)

### 5. OpenBabel kurulumu
```bash

# Ubuntu/Debian
sudo apt-get install openbabel

# macOS (Homebrew)
brew install open-babel
```

### ▶️ Kullanım
```bash


python full_reinvent_pipeline.py \
  --pdb pocket.pdb \
  --vina /usr/local/bin/vina \
  --model reinvent.prior \
  --num_ligands 200 \
  --top_k 2
Çıktılar:
top2.sdf: Seçilen 2 ligand (3D koordinatlarla)
top2_smiles.txt: SMILES + skorlar
top2_prepared/: Ara dosyalar (PDBQT, log, vs.)

```
