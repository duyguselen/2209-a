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
```bash
conda create -n reinvent-pipeline python=3.9
conda activate reinvent-pipeline
