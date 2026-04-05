# 🌬️ Moara de Vânt — Animație Java Swing Multi-Thread

**Lucrare individuală** · Programarea Concurentă și Distribuită · CR-221FR  
**Student:** Ciobanu Stanislav · **Conducător:** asist. univ. Rotaru Lilia

---

## Descriere

Aplicație grafică Java Swing care animează o moară de vânt folosind **trei thread-uri concurente** ce comunică între ele prin mecanismul `Exchanger<Color>`. Proiectul demonstrează sincronizarea între thread-uri, detectarea evenimentelor unghiulare și propagarea stării (culoare) dintr-un thread în altul fără blocare explicită cu `synchronized`.

Ideea scenei: morile de vânt reflectă lumina solară când o pală trece prin poziția „ora 12". Culoarea reflexiei este preluată direct de la soare prin `Exchanger` — soarele alternează între galben și portocaliu la fiecare 2 rotații complete, imitând trecerea dimineții în zi.

---

## Demo

### Pale în stare normală (gri)

![Pale gri](screen1_pale_gri.png)

Palele se rotesc continuu. Soarele este galben. Palele au culoarea implicită gri.

---

### Reflexie activă — pala preia culoarea soarelui

![Pală galbenă](screen2_pala_galbena.png)

La trecerea prin „ora 12", pala detectată prin thread-ul `reflexie` inițiază un `exchanger.exchange(null)` și primește culoarea curentă a soarelui (galben). Culoarea este păstrată 500ms, apoi pala revine la gri.

---

### Reflexie cu soare galben — unghi diferit

![Pală galbenă 2](screen3_pala_galbena2.png)

Același mecanism, capturat la un unghi de rotație diferit. Demonstrează că reflexia funcționează corect pentru fiecare sub-pală independent.

---

## Arhitectura thread-urilor

```
┌─────────────────────────────────────────────────────────────┐
│                     Thread: rotire                          │
│  • Incrementează unghiul cu 2° la fiecare 50ms             │
│  • Schimbă culoarea soarelui (YELLOW↔ORANGE) la 2 rotații  │
│  • Apelează repaint()                                       │
└──────────────────────────┬──────────────────────────────────┘
                           │  unghi (volatile)
┌──────────────────────────▼──────────────────────────────────┐
│                     Thread: reflexie                        │
│  • Monitorizează fiecare sub-pală la 20ms                  │
│  • Detectează trecerea prin „ora 12" (unghi ~0°)           │
│  • Pornește un sub-thread → exchanger.exchange(null)        │
└──────────────────────────┬──────────────────────────────────┘
                           │  Exchanger<Color>
┌──────────────────────────▼──────────────────────────────────┐
│                  Thread: threadExchange                     │
│  • Răspunde instant cu culoarea curentă a soarelui         │
│  • exchanger.exchange(culoareSoare) în buclă               │
└─────────────────────────────────────────────────────────────┘
```

---

## Cum funcționează `Exchanger`

`Exchanger<Color>` este un punct de sincronizare între două thread-uri: primul care ajunge la `exchange()` se blochează și **așteaptă** al doilea. Când ambele ajung, **fac schimb de valori** și continuă execuția.

```
Thread reflexie (sub-pală)          Thread threadExchange
        │                                   │
        │  exchanger.exchange(null)  ◄──►   │  exchanger.exchange(culoareSoare)
        │                                   │
  primește Color (galben/portocaliu)   primește null
  → aplică pe pală 500ms
  → revine la Color.GRAY
```

---

## Structura clasei

```
MoaraDeVant (extends JPanel)
│
├── unghi          — unghiul curent de rotație (double)
├── pale = 2       — numărul de pale principale
├── subPale = 4    — pale * 2 (segmente desenate)
├── exchanger      — Exchanger<Color>
├── culoareSoare   — volatile Color (YELLOW / ORANGE)
└── culoarePale[]  — culoarea fiecărei sub-pale
│
├── Thread: rotire        — rotație + schimbare culoare soare
├── Thread: reflexie      — detecție „ora 12" + exchange
└── Thread: threadExchange — furnizor de culoare
│
├── paintComponent()      — entry point Swing
├── deseneazaFundal()     — cer, soare, iarbă, corp moară
└── deseneazaMoara()      — hub + pale cu AffineTransform
```

---

## Publicarea pe GitHub

### Inițializare repozitoriu și push

![Git terminal](screen4_git_terminal.png)

```bash
git init
git add .
git commit -m "Lucrare individuala PCD"
git branch -M main
git remote add origin https://github.com/shiprinski95/LucrIndivPCD.git
git push -u origin main
```

---

### Repozitoriu pe GitHub

![GitHub repo](screen5_github.png)

---

## Pornire rapidă

```bash
javac LucrIndiv.java
java MoaraDeVant
```

> Cerințe: Java 8+ · Nicio dependență externă

---

## Concepte demonstrate

| Concept | Utilizare în proiect |
|---|---|
| `Exchanger<T>` | Schimb de culoare între thread-ul reflexiei și cel al soarelui |
| `volatile` | Vizibilitate garantată a `unghi` și `culoareSoare` între thread-uri |
| Thread daemon | Thread-urile se opresc automat la închiderea ferestrei |
| `AffineTransform` | Rotații independente pentru fiecare pală în Swing |
| `repaint()` | Redesenare asincronă din thread non-EDT |

---

> **Universitatea Tehnică a Moldovei** · FCIM · Programarea Concurentă și Distribuită · 2025
