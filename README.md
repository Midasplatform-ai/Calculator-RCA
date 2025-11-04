# 🚗 Calculator RCA - Risc Ridicat (n8n Workflows)

Calculator pentru **Prima Recomandată (Pr)** conform reglementărilor BAAR pentru asigurați cu risc ridicat în România.

## 📋 Conținut

- [Formula de Calcul](#formula-de-calcul)
- [Opțiuni Disponibile](#opțiuni-disponibile)
- [Instalare și Configurare](#instalare-și-configurare)
- [Utilizare](#utilizare)
- [Fișiere Incluse](#fișiere-incluse)
- [Cerințe Tehnice](#cerințe-tehnice)
- [Notițe Importante](#notițe-importante)

---

## 📐 Formula de Calcul

**Pentru toate solicitările începând cu 01.07.2025:**

```
Pr = {TR × N + [(PO1 + PO2 + PO3) ÷ 3] × 64%} ÷ 2
```

**Unde:**
- **Pr** = Prima recomandată (rezultat final)
- **TR** = Tariful de referință (cu coeficient Bonus-Malus aplicat)
- **N** = 1.36 (factor constant)
- **PO1, PO2, PO3** = Primele nete de cheltuieli (cele mai mici 3 valori din ofertele RCA)

**Exemplu de calcul:**
```
TR = 2,156 RON
PO1 = 1,160.27 RON
PO2 = 1,178.53 RON
PO3 = 1,226.73 RON

Pas 1: Media = (1160.27 + 1178.53 + 1226.73) ÷ 3 = 1,188.51 RON
Pas 2: 64% din medie = 1,188.51 × 0.64 = 760.65 RON
Pas 3: TR × N = 2,156 × 1.36 = 2,932.16 RON
Pas 4: Suma = 2,932.16 + 760.65 = 3,692.81 RON
Pas 5: Pr = 3,692.81 ÷ 2 = 1,846.41 RON
```

---

## 🎯 Opțiuni Disponibile

### **Opțiunea 1: Intrare Manuală (Simplă)**

✅ Recomandată pentru utilizare rapidă de către brokeri

**Caracteristici:**
- 4 câmpuri de input: TR, PO1, PO2, PO3
- Calcul instant al primei recomandate
- Validare completă a datelor
- Afișare pas cu pas a calculului

**Fișiere:**
- `rca-calculator-manual.json` - Workflow n8n
- `rca-form-manual.html` - Formular web

**Mod de lucru:**
1. Brokerul consultă tabelul ASF pentru a găsi TR
2. Brokerul extrage PO1, PO2, PO3 din PDF-ul cu oferte
3. Introduc valorile manual în formular
4. Sistemul calculează și afișează Pr

---

### **Opțiunea 2: Upload PDF Automat (Avansat)**

🤖 Extracție automată de date din PDF

**Caracteristici:**
- Upload PDF cu oferta RCA
- Extracție automată: Tip persoană, Județ, Putere motor (KW)
- Extracție automată: PO1, PO2, PO3 din secțiunea "Oferta 12 luni fara decontare directa"
- Căutare automată TR din tabelul ASF (integrat în cod)
- Calcul complet automat

**Fișiere:**
- `rca-calculator-automated.json` - Workflow n8n
- `rca-form-automated.html` - Formular web cu upload

**Mod de lucru:**
1. Brokerul încarcă PDF-ul cu oferte
2. Sistemul extrage toate datele necesare
3. Sistemul găsește TR automat din tabel
4. Sistemul calculează și afișează Pr cu toate datele extrase

---

## 🛠️ Instalare și Configurare

### **Pasul 1: Importă Workflows în n8n**

1. Deschide n8n (http://localhost:5678)
2. Click pe **"+"** → **"Import from File"**
3. Selectează `rca-calculator-manual.json` sau `rca-calculator-automated.json`
4. Click **"Import"**
5. Activează workflow-ul (buton **"Active"** în dreapta sus)

### **Pasul 2: Obține URL-ul Webhook**

1. În workflow, click pe nodul **"Webhook"**
2. Copiază **"Production URL"** (ex: `http://localhost:5678/webhook/rca-calculator-manual`)
3. Acest URL va fi folosit în formularul HTML

### **Pasul 3: Configurează Formularul HTML**

**Pentru Opțiunea 1 (Manual):**

Deschide `rca-form-manual.html` și editează linia 347:

```javascript
// SCHIMBĂ CU URL-UL TĂU WEBHOOK
const WEBHOOK_URL = 'http://localhost:5678/webhook/rca-calculator-manual';
```

**Pentru Opțiunea 2 (Automat):**

Deschide `rca-form-automated.html` și editează linia 388:

```javascript
// SCHIMBĂ CU URL-UL TĂU WEBHOOK
const WEBHOOK_URL = 'http://localhost:5678/webhook/rca-calculator-automated';
```

### **Pasul 4: Populează Tabelul TR (Doar pentru Opțiunea 2)**

⚠️ **IMPORTANT**: Workflow-ul automat conține un tabel TR simplificat.

**Fișier de modificat:** `rca-calculator-automated.json`
**Nod:** "Lookup TR" → Function Code

Găsește secțiunea `TR_TABLE` și completează cu datele complete din:
📄 `Tarife-RCA-de-referinta-ASF-DECEMBRIE-2024.pdf`

**Structura tabelului:**

```javascript
const TR_TABLE = {
  'autoturism_juridica': {
    'bucuresti': [
      { kw_min: 0, kw_max: 55, tr: 2156 },
      { kw_min: 55, kw_max: 85, tr: 2487 },
      // ... adaugă toate intrările
    ],
    'cluj': [...],
    'default': [...]
  },
  'autoturism_fizica': {
    'bucuresti': [...],
    // ... continuă pentru toate județele
  }
  // ... adaugă toate tipurile de vehicule
};
```

---

## 📖 Utilizare

### **Opțiunea 1 - Manual Input**

1. Deschide `rca-form-manual.html` în browser
2. Completează cele 4 câmpuri:
   - **TR**: Găsit în tabelul ASF conform vehicul/persoană/locație
   - **PO1**: Prima TOTAL din prima ofertă (secțiunea 12 luni)
   - **PO2**: Prima TOTAL din a doua ofertă
   - **PO3**: Prima TOTAL din a treia ofertă
3. Click **"Calculează Prima Recomandată"**
4. Vezi rezultatul cu detalii complete

### **Opțiunea 2 - Automated Upload**

1. Deschide `rca-form-automated.html` în browser
2. Trage PDF-ul cu oferte în zona de upload SAU click pentru selectare
3. Click **"Procesează PDF și Calculează"**
4. Sistemul va:
   - Extrage datele automat
   - Calcula TR
   - Afișa toate datele extrase
   - Calcula și afișa Pr

---

## 📁 Fișiere Incluse

```
asigurare-RCA/
├── README.md                                      # Documentație completă (acest fișier)
├── Tarife-RCA-de-referinta-ASF-DECEMBRIE-2024.pdf # Tabel oficial ASF cu tarifele TR
├── Oferta.pdf                                     # Exemplu de PDF cu oferte RCA
├── P01-2-3.jpg                                    # Screenshot exemplu secțiune oferte
│
├── rca-calculator-manual.json                     # Workflow n8n - Intrare manuală
├── rca-form-manual.html                           # Formular HTML - Intrare manuală
│
├── rca-calculator-automated.json                  # Workflow n8n - Upload automat
└── rca-form-automated.html                        # Formular HTML - Upload automat
```

---

## 💻 Cerințe Tehnice

### **Pentru n8n:**

- **n8n** versiune 1.0+ (instalat și rulând)
- **Node.js** versiune 18+
- Plugin **Extract from File** (pentru Opțiunea 2)
- Port 5678 deschis (sau port personalizat)

### **Pentru Formulare HTML:**

- Orice browser modern (Chrome, Firefox, Safari, Edge)
- JavaScript activat
- Conexiune la serverul n8n (local sau remote)

### **Pentru Opțiunea 2 (Automat):**

- n8n cu plugin **PDF Text Extraction** instalat
- Sau integrare cu servicii OCR externe (ex: Mindee, AWS Textract)

---

## ⚠️ Notițe Importante

### **1. Tabelul TR trebuie actualizat lunar**

Tabelul de referință ASF se actualizează lunar. Verifică și actualizează:
- În Opțiunea 2: Modifică constanta `TR_TABLE` din codul nodului "Lookup TR"
- Descarcă noul tabel de pe: https://asfromania.ro

### **2. Validarea Ofertelor**

Conform BAAR, ofertele trebuie să îndeplinească:
- ✅ Perioada: 12 luni obligatoriu
- ✅ Secțiunea: "Oferta 12 luni fara decontare directa"
- ✅ Valoare: TOTAL (primă netă de cheltuieli de distribuție)
- ✅ Minimum 3 oferte de la asigurători diferiți
- ✅ Cod de ofertă valid prezent

### **3. Incadrarea în Risc Ridicat**

Un asigurat este considerat risc ridicat dacă:
```
Toate ofertele > (TR × 1.36 × Coeficient Bonus-Malus)
```

Formula calculează Prima Recomandată DOAR după incadrarea în această categorie.

### **4. Securitate și GDPR**

⚠️ Datele prelucrate conțin informații personale (nume, județ, vehicul).

**Recomandări:**
- Folosește HTTPS pentru formularele publice
- Nu stoca datele mai mult decât necesar
- Implementează logging pentru audit
- Conformează-te cu GDPR (consimțământ, ștergere date)

### **5. Limitări Opțiunea 2 (Automat)**

Extracția automată depinde de:
- ✅ Formatul PDF (text selectabil, nu scanat)
- ✅ Structura constantă a PDF-ului (layout similar)
- ✅ Calitatea OCR (dacă PDF-ul este scanat)

Dacă extracția eșuează → folosește Opțiunea 1 (Manual)

---

## 🔧 Depanare (Troubleshooting)

### **Problema: "Eroare de conexiune"**

**Soluție:**
1. Verifică că n8n rulează: http://localhost:5678
2. Verifică că workflow-ul este **activ** (buton verde)
3. Verifică URL-ul webhook în fișierul HTML

### **Problema: "Nu s-au putut extrage datele" (Opțiunea 2)**

**Soluție:**
1. Verifică că PDF-ul conține text selectabil (nu doar imagini)
2. Verifică că secțiunea "Oferta 12 luni fara decontare directa" există
3. Testează cu PDF-ul exemplu (`Oferta.pdf`)
4. Folosește Opțiunea 1 ca alternativă

### **Problema: "TR incorect calculat" (Opțiunea 2)**

**Soluție:**
1. Verifică că ai populat complet `TR_TABLE` cu toate județele
2. Verifică că numele județului din PDF se potrivește cu cel din tabel
3. Adaugă normalizare pentru variații (ex: "Bucuresti Sectorul 2" → "bucuresti")

---

## 📞 Suport și Contact

Pentru întrebări tehnice sau probleme:
- Documentație BAAR: https://www.baar.ro
- Documentație n8n: https://docs.n8n.io
- GitHub Issues: [adaugă link-ul repository-ului tău]

---

## 📜 Licență și Conformitate

Acest calculator implementează formula oficială BAAR conform:
- **Legea nr. 132/2017** (cu modificări ulterioare)
- **Norma A.S.F. nr. 20/2017** (cu modificări ulterioare)
- **Avizul A.S.F. nr. 121/19.04.2024**

**Ultima actualizare:** Noiembrie 2024
**Versiune formulă:** Valabilă de la 01.07.2025

---

## 🎓 Exemple de Utilizare

### **Exemplu 1: Persoană Juridică, București**

**Date de intrare:**
- Tip persoană: Juridică
- Județ: București Sectorul 2
- Putere motor: 55 KW
- Vehicul: Autoturism
- PO1: 1,160.27 RON
- PO2: 1,178.53 RON
- PO3: 1,226.73 RON

**TR găsit:** 2,156 RON (din tabel ASF)

**Rezultat:**
- Prima Recomandată: **1,846.41 RON** pentru 12 luni

### **Exemplu 2: Persoană Fizică, Cluj**

**Date de intrare:**
- Tip persoană: Fizică
- Județ: Cluj-Napoca
- Putere motor: 85 KW
- Vehicul: Autoturism
- PO1: 1,850.00 RON
- PO2: 1,920.00 RON
- PO3: 1,975.00 RON

**TR găsit:** 2,001 RON (din tabel ASF)

**Rezultat:**
- Prima Recomandată: **2,042.84 RON** pentru 12 luni

---

**🚀 Succes în utilizare!**
