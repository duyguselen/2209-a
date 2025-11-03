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
profile
Qwen3-Max
12:26 pm
Harika! İşte GitHub’a yüklemeye hazır haliyle tüm dosyalar:

✅ 1. Düzeltilmiş Python Kodu (full_reinvent_pipeline.py)
python


185
186
187
188
189
190
191
192
193
194
195
196
197
198
199
200
201
202
203
204
205
206
207
208
209
210
211
212
213
214
215
216
217
218
219
220
221
222
223
224
225
226
227
228
229
230
231
232
233
234
235
236
237
238
239
240
241
242
243
244
245
⌄
⌄
⌄
⌄
⌄
⌄
⌄
⌄
⌄
⌄
⌄
⌄
⌄
# full_reinvent_pipeline.py
    fps = [AllChem.GetMorganFingerprintAsBitVect(l['mol'], 2, 2048) for l in valid_ligands]
    selected = [0]
    while len(selected) < top_k and len(selected) < len(valid_ligands):
        best_idx, best_min_dist = None, -1
        for i, fp in enumerate(fps):
            if i in selected:
                continue
            sims = [TanimotoSimilarity(fp, fps[s]) for s in selected]
            min_dist = 1.0 - max(sims)
            if min_dist > best_min_dist:
                best_min_dist = min_dist
                best_idx = i
        if best_idx is None:
            break
        selected.append(best_idx)
    return [valid_ligands[i] for i in selected]

# -------------------------
# 6. Asıl pipeline
# -------------------------
def main():
    args = parse_args()
    os.makedirs(args.out_dir, exist_ok=True)

    pocket = analyze_pocket(args.pdb)
    if pocket is None:
        sys.exit(1)

    ligands = generate_ligands_reinvent(args.model, args.num_ligands)
    if not ligands:
        print("No ligands generated!")
        return

    for l in ligands:
        l['qed'] = QED.qed(l['mol'])
        l['admet'] = admet_score(l['mol'])
        l['sa'] = sa_score(l['mol'])
        l['dock_score'] = dock_ligand(
            l['mol'], pocket, 
            l['smiles'].replace("/", "_").replace("\\", "_"), 
            args.pdb, args.vina, args.out_dir
        )
        calculate_combined(l)

    topk = select_topk(ligands, args.top_k)

    out_txt = "top3_smiles.txt"
    out_sdf = "top3.sdf"
    writer = rdmolfiles.SDWriter(out_sdf)
    with open(out_txt, "w") as f:
        for l in topk:
            writer.write(l['mol'])
            f.write(f"{l['smiles']}\tCombined={l['combined']:.4f}\tDock={l['dock_score']:.2f}\tQED={l['qed']:.3f}\tADMET={l['admet']:.3f}\tSA={l['sa']}\n")
    writer.close()

    print("Top ligands saved:", out_txt, out_sdf)
    for l in topk:
        print(f"{l['smiles']}\n  Combined={l['combined']:.4f}, Dock={l['dock_score']:.2f}, QED={l['qed']:.3f}, ADMET={l['admet']:.3f}, SA={l['sa']}")

if __name__ == "__main__":
    main()
📄 2. README.md
markdown


1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
⌄
⌄
⌄
⌄
⌄
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

2. Bağımlılıkları kurun
```bash
1
pip install -r requirements.txt

3. REINVENT4 prior modelini indirin
```bash
1
wget https://zenodo.org/records/10870647/files/reinvent.prior -O reinvent.prior

4. AutoDock Vina'yı kurun
https://vina.scripps.edu
vina komutu sistem yolunda olmalı (veya --vina ile yolu verin)

5. OpenBabel kurulumu
```bash
1
# Ubuntu/Debian
sudo apt-get install openbabel

# macOS (Homebrew)
brew install open-babel


▶️ Kullanım
```bash


1
python full_reinvent_pipeline.py \
  --pdb pocket.pdb \
  --vina /usr/local/bin/vina \
  --model reinvent.prior \
  --num_ligands 200 \
  --top_k 3
Çıktılar:
top3.sdf: Seçilen 3 ligand (3D koordinatlarla)
top3_smiles.txt: SMILES + skorlar
top3_prepared/: Ara dosyalar (PDBQT, log, vs.)
