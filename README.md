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
