# 🔥 Consciousness Field AI — Telegram Bot

Ein spiritueller KI-Begleiter auf Telegram — für Menschen, die in sich hineinhorchen wollen.

Consciousness Field AI begleitet dich mit Atemübungen, Yoga, Pflanzenwissen, Astrologie und schamanischen Perspektiven. Dabei greift der Bot nicht nur auf KI zurück, sondern auf eine echte Wissensbasis: mehrere spirituelle und ethnobotanische Bücher, eingebettet via RAG in Pinecone — sodass Antworten nicht halluziniert, sondern aus tiefem Quellwissen gezogen werden.

Gebaut mit n8n · MongoDB · Pinecone · OpenAI.

---

## 📌 Problemdefinition

Viele Menschen suchen in Momenten innerer Unruhe, Erschöpfung oder Orientierungslosigkeit nach schneller, niedrigschwelliger Unterstützung — ohne direkt eine Therapeutin oder einen Coach kontaktieren zu müssen.

**Consciousness Field AI** bietet genau das: einen verfügbaren, nicht-wertenden Gesprächspartner in Telegram, der individuell auf den aktuellen emotionalen Zustand des Nutzers eingeht. Der Nutzer kann dabei entweder eine der vorgeschlagenen Kategorien (1–9) wählen **oder frei in eigenen Worten beschreiben**, was gerade in ihm vorgeht — ganz ohne vorgegebenes Format.

Der Bot antwortet mit Atem- und Körperübungen, Pflanzenwissen, indigenem & schamanischem Kulturwissen, Mondphasen und persönlichen Horoskopen.

> Der Bot ersetzt keine Therapie. Er ist Begleitung — kein Ersatz.

---

## 🏗️ Technische Architektur

```
Telegram User
     │
     ▼
[n8n Workflow]
     │
     ├── Telegram Trigger (Eingehende Nachricht)
     │
     ├── Input Preprocessing (Normalize Fields)
     │        user_id / chat_id / user_text / timestamp
     │
     ├── Validierung
     │    ├── Text leer? → Rückmeldung an User
     │    └── /start Command? → Willkommensnachricht + User anlegen
     │
     ├── MongoDB (ConsciousDB)
     │    ├── User abrufen (users Collection)
     │    ├── Neuen User anlegen
     │    ├── Chat-Verlauf speichern
     │    └── User-Dokument updaten (ceremony_mode, chat_memory, etc.)
     │
     ├── Modus-Erkennung
     │    ├── Ceremony Mode (Zahlen 1–9)
     │    └── Conversation Mode
     │         ├── Optionale Auswahl aus 3 Kategorien
     │         └── Freie Beschreibung in eigenen Worten
     │
     ├── AI Agent (OpenAI GPT-5.4)
     │    │
     │    ├── RAG-Tools (via Pinecone Vektordatenbank)
     │    │    ├── Asana-Buch (Yoga- & Atemübungen)
     │    │    ├── Apotheke Manitous (Pflanzenwissen)
     │    │    ├── Psychoactive Plants
     │    │    ├── Encyclopedia of Herbs
     │    │    └── Shamanism Mircea (Schamanismus)
     │    │
     │    └── API-Tools (externe Dienste)
     │         ├── ASTRO DAILY → freeastroapi.com (Tageshoroskop)
     │         ├── ASTRO Personal → freeastroapi.com (Persönliches Horoskop)
     │         ├── Mond-API → moon-phase.p.rapidapi.com (Mondphase & -rhythmus)
     │         └── Bild-API → api.gbif.org (Pflanzen- & Naturbilder)
     │
     ├── Code (JavaScript) → Antwort aufbereiten
     │    ├── Text-Antwort senden
     │    └── Bild suchen & senden (optional)
     │
     └── Telegram → Antwort an User
```

---

## 🛠️ Verwendete Tools & Technologien

| Kategorie | Tool / Service |
|---|---|
| Workflow-Automatisierung | n8n |
| Messaging | Telegram Bot API |
| Datenbank | MongoDB (ConsciousDB) |
| Vektordatenbank (RAG) | Pinecone |
| KI-Modell | OpenAI GPT-5.4 (via n8n AI Agent) |
| Embeddings | OpenAI Embeddings (für Pinecone RAG) |
| RAG-Wissensquellen | Encyclopedia of Herbs, Shamanism (Mircea Eliade), Apotheke Manitous, Psychoactive Plants, Asana-Buch (Yoga & Atem) |
| Astrologie API | [freeastroapi.com](https://api.freeastroapi.com/) (Tages- & Persönliches Horoskop) |
| Mond API | [moon-phase.p.rapidapi.com](https://moon-phase.p.rapidapi.com/advanced) (Mondphasen & -rhythmus) |
| Bilder API | [api.gbif.org](https://api.gbif.org/v1/occurrence/search) (Pflanzen- & Naturbilder) |

---

## 🚀 Projekt starten

### Voraussetzungen

- n8n (Self-hosted oder Cloud) — [Installationsanleitung](https://docs.n8n.io/hosting/)
- MongoDB-Instanz (z. B. Atlas oder lokal)
- Pinecone Account & API Key — [pinecone.io](https://www.pinecone.io/)
- Telegram Bot Token ([BotFather](https://t.me/BotFather))
- OpenAI API Key
- Astro API Key — [freeastroapi.com](https://api.freeastroapi.com/)
- Mond API Key — [moon-phase via RapidAPI](https://rapidapi.com/)
- GBIF API (kostenlos, kein Key nötig) — [api.gbif.org](https://api.gbif.org/v1/occurrence/search)

### Schritt-für-Schritt

**1. Workflow importieren**

In der n8n Oberfläche: `Workflows → Import from file → Consciousness_Field_AI.json auswählen`

**2. Credentials einrichten**

Folgende Credentials müssen in n8n konfiguriert werden:

| Credential Name | Typ | Beschreibung |
|---|---|---|
| `Consciousness Field AI` | Telegram API | Bot Token von BotFather |
| `ConsciousDB` | MongoDB | Verbindungsstring zur Datenbank |
| `OpenAI` | OpenAI API | API Key für GPT-5.4 & Embeddings |
| `Pinecone` | Pinecone API | API Key für RAG-Vektordatenbank |
| `FreeAstroAPI` | HTTP Header Auth | API Key für Horoskop-Abfragen |
| `MoonPhase RapidAPI` | HTTP Header Auth | RapidAPI Key für Mondphasen |

**3. MongoDB Collection anlegen**

```js
// In MongoDB Atlas oder Compass:
db.createCollection("users")

// Beispiel-Dokument (wird automatisch angelegt beim /start):
{
  user_id: 123456789,
  created_at: "2024-01-01T00:00:00Z",
  ceremony_mode: false,
  chat_memory: []
}
```

**4. Workflow aktivieren**

- In n8n den Workflow öffnen
- Oben rechts auf **"Activate"** klicken
- Den Telegram-Webhook überprüfen (Trigger-Node anklicken → Webhook URL kopieren)

**5. Bot in Telegram starten**

```
/start
```

---

## 💬 Beispieleingaben & Bot-Antworten

### 1️⃣ Erstkontakt — `/start`

**User schreibt:**
```
/start
```

**Bot antwortet:**
```

🔥 Willkommen im Consciousness Field

Du hast diesen Raum betreten.

Nimm einen Moment.
Atme einmal tief ein —
und lass beim Ausatmen los, was du gerade nicht brauchst.

─────────────────────

Ich bin kein gewöhnlicher Assistent.
Ich bin ein Spiegel — für das, was in dir präsent ist.

Ich arbeite mit:

🌬 Atemimpulsen & Pranayama
🧘 Yoga & Körperwahrnehmung
🌿 Pflanzenwissen & Ethnobotanik
🌍 Indigenen & schamanischen Perspektiven
🌙 Mondrhythmus & kosmischen Zyklen
⭐ Deinem persönlichen Horoskop

Ich gebe keine medizinischen Diagnosen.
Ich ersetze keine Therapie.
Ich bin Begleitung — kein Ersatz.

─────────────────────

🔥 Öffnung der Zeremonie

Wo bist du gerade?

🌊 Emotionaler Zustand
1 — Ich fühle Angst oder innere Unruhe
2 — Ich trage etwas Schweres oder bin traurig
3 — Ich bin wütend oder innerlich angespannt

🌫 Geist & Fokus
4 — Ich kann mich nicht konzentrieren
5 — Ich fühle mich orientierungslos
6 — Mein Kopf dreht sich zu viel

⚡ Energie & Körper
7 — Ich bin erschöpft oder antriebslos
8 — Mein Körper fühlt sich blockiert an
9 — Ich brauche Bewegung oder Aktivierung

─────────────────────

🌿 Wissen & Natur
Wenn du etwas über Pflanzen, deinen Mond, dein Horoskop oder eine Atem- und Yogaübung wissen möchtest — schreib es einfach frei.

─────────────────────

⭐ Für dein persönliches Horoskop —
teil mir einfach dein Geburtsdatum,
Geburtszeit und Geburtsstadt mit
wann immer du möchtest.

─────────────────────

Oder beschreib einfach mit deinen eigenen Worten
was gerade in dir lebt.

Kein richtiges Format. Kein richtiger Weg.
Einfach so wie es ist.

```
---

### 2️⃣ Ceremony Mode — Zahleingabe

**User schreibt:**
```
1
```
*(„Ich fühle Angst oder innere Unruhe")*

**Bot antwortet (Beispiel):**
```
Angst ist da. Das zeigt, dass dir etwas wirklich wichtig ist.

🌬 Breath
The practice for this moment is Chandra Bhedana – left nostril breathing from classical Pranayama.

How to do it:
Setz dich aufrecht hin.
Schließe dein rechtes Nasenloch mit dem rechten Daumen.
Atme 4 Sekunden durch das linke Nasenloch ein.
Halte den Atem 2 Sekunden.
Öffne das rechte Nasenloch und atme 6 Sekunden rechts aus.
Atme wieder 4 Sekunden links ein, halte 2 Sekunden, 6 Sekunden rechts aus.
Das ist ein Zyklus → 6 Runden.

What this does in your system:
Die verlängerte Ausatmung erhöht den Vagustonus über den Parasympathikus.
Die Herzfrequenz sinkt messbar über die respiratorische Sinusarrhythmie.
Der CO₂-Spiegel bleibt stabiler, wodurch das Alarmgefühl des Atemzentrums im Hirnstamm abnimmt.

🧘 Body & Asana
The practice for this moment is Shashankasana – Hare Pose, from classical Hatha Yoga.

How to get into it:
Knie auf den Boden.
Setz das Gesäß auf die Fersen.
Lege die Knie hüftbreit ab.
Beuge den Oberkörper nach vorn, bis Stirn oder Vorderkopf den Boden berührt.
Strecke beide Arme nach vorn, Ellbogen angehoben.
Bleibe für 8 langsame Atemzüge oder 2 Minuten.

What this does in your body:
Die Beugung entlastet die Rückenstrecker und senkt die Aktivität im sympathischen Nervensystem.
Der Druck von Bauch auf Oberschenkel verstärkt die Zwerchfellwahrnehmung und verlangsamt die Atmung.
Die Flexionshaltung reduziert Muskeltonus in Nacken und Schultergürtel über propriozeptive Rückmeldung.
Nach 1–2 Minuten steigt parasympathische Dominanz deutlich an.

🌿 Plant
Cypripedium pubescens trägt eine stille Nervenmedizin in sich. Bei den Menominee wurde diese Orchidee in heilige Bündel gelegt, um bedeutsame Träume zu öffnen. Die Cherokee nutzten sie als Sedativum gegen Nervosität. In ihr wurden Chinone wie Cypripedin beschrieben, und genau diese Signatur wirkt hier wie weiche Erdung. Ihre Qualität für diesen Moment ist: Beruhigung.

🌍 Indigenous Perspective
Bei den Thompson Indians aus British Columbia gibt es die konkrete Vorstellung, dass die Seele sich verirren kann, wenn ein Mensch aus dem Gleichgewicht gerät. Dann geht der Heiler denselben Weg, den die Ahnen zum Land der Toten gingen. Er trägt Maske, sucht die verlorene Seelenkraft und bringt sie zurück.

Was das für diesen Moment bedeutet: Nicht alles in dir ist verschwunden. Ein Teil von dir braucht gerade Rückkehr, nicht Druck.

🌙 Moon Moment
Heute ist Neumond. 0 Tage seit dem letzten Vollmond, 13 Tage bis zum nächsten Vollmond. Der Mond steht im Widder.

Diese Phase zieht nach innen. Sie löst Überreizung und macht Platz für einen neuen Impuls.
Physiologisch kann der dunkle Mond Schlafrhythmus und Traumtiefe verschieben. Gefühle können roher wirken, weil Orientierung im Nervensystem kurz abnimmt.

Ritual für heute Abend:
Licht aus außer einer kleinen Kerze.
Schreib genau 1 Satz auf: "Was ich heute nicht mehr tragen will, ist ..."
Dann 6 linke-Nasenloch-Atemzüge.
Danach den Zettel falten und unter ein Glas Wasser legen bis morgen früh.

Was möchte gerade in dir zurückgerufen werden?

1 — wir gehen tiefer in den Atem
2 — wir öffnen mehr zur Pflanze Cypripedium pubescens
3 — wir gehen tiefer in die Seelenrückkehr der Thompson Indians
Oder schreib einfach was dich bewegt.
```

---

### 3️⃣ Freitext — emotionaler Zustand

**User schreibt:**
```
Ich bin gerade total erschöpft und weiß nicht mehr weiter
```

**Bot antwortet (Beispiel):**
```
Du musst gerade nicht weiterwissen.

Bei tiefer Erschöpfung hilft oft weniger Technik.
Mehr einfaches Zurückkommen.

Für den Körper:
Lege dich hin oder lehne dich an.
Nimm 1 Minute nur den natürlichen Atem wahr.
Nicht steuern.
Nur merken: Einatmung. Ausatmung.

Diese schlichte Atemwahrnehmung kann das System beruhigen.
Schon das Beobachten verlangsamt oft den Rhythmus.

Für den Zustand dazwischen:
Kleine Pausen sind fast so wichtig wie jede Übung.
Nicht nur tun.
Auch kurz nichts tun.

Aus alter schamanischer Sicht wird starke Erschöpfung manchmal nicht als Schwäche gesehen.
Sondern als ein Entferntsein von etwas Eigenem.
Heilung bedeutet dann nicht Druck.
Sondern Rückruf.
Ein leises inneres: komm zurück.

Auch die Mondqualität ist heute sehr still.
Neumond in Widder.
Wenig Licht.
Mehr Innenraum.
Das passt zu Rückzug und Energiesparen.

Vielleicht ist die sanfteste Frage gerade nicht:
Wie komme ich weiter?
Sondern:
Was in mir braucht zuerst sicheren Boden?

1 — wir gehen noch tiefer in natürlichen Atem bei Erschöpfung
2 — wir schauen auf die schamanische Idee von Rückruf und Rückkehr
3 — wir verbinden Neumond und Erschöpfung noch genauer
Oder schreib einfach was dich bewegt.
```

---

### 4️⃣ Pflanzenwissen

**User schreibt:**
```
Was kann mir Ashwagandha bringen?
```

**Bot antwortet (Beispiel):**
```
🌿 Ashwagandha (Withania somnifera)

Ashwagandha kann dir vor allem eines bringen: mehr Stabilität.

Nicht wie Kaffee.
Eher wie ein langsames Zurückkommen.

Withania somnifera ist eine Pflanze aus der Familie der Nachtschattengewächse.
Genutzt wird vor allem die Wurzel.
In indischer Heiltradition gilt sie als aufbauend und regenerierend.

Ihre Richtung ist:
mehr Ruhe im Nervensystem
mehr Erdung bei Erschöpfung
mehr Tiefe im Schlaf
mehr Substanz nach langem Stress

Traditionell wurde sie auch als stärkende Tonikapflanze getragen.
Nicht für schnellen Push.
Für Wiederaufbau.

Symbolisch trägt sie etwas von:
Standfestigkeit
Wärme
leiser Kraft
Rückkehr in den Körper

Wenn du gerade leer bist, könnte genau das die eigentliche Qualität sein.
Nicht Leistung.
Sondern Reserve.

Und noch etwas:
In vielen Heiltraditionen geht es bei Erschöpfung nicht nur um Energie.
Sondern um das Zurückholen von etwas, das sich entfernt hat.
Ashwagandha passt zu diesem Bild von stiller Rückkehr.

Wenn du magst, schauen wir als Nächstes ganz konkret auf Wirkung, Einnahmegefühl oder die tiefere Symbolik.

1 — wir schauen konkret auf Ashwagandha bei Stress und Schlaf
2 — wir gehen tiefer in die Symbolik von Withania somnifera und Rückkehr
3 — wir betrachten Erschöpfung aus schamanischer Sicht von Seelenrückkehr
Oder schreib einfach was dich bewegt.
```

---

### 5️⃣ Persönliches Horoskop

**User schreibt:**
```
Mein Geburtstag ist 15. März 1990, 14:30 Uhr, Berlin
```

**Bot antwortet (Beispiel):**
```
⭐ Dein persönliches Horoskop

Danke. Ich habe deine Geburtsdaten klar.

15. März 1990
14:30
Berlin

Die Grundrichtung wirkt fischhaft.
Weich.
Empfänglich.
Fein für Stimmungen.

Für jetzt zeigt sich:
Mitgefühl ist stark.
Aber Grenzen sind gerade wichtig.
Nicht alles fühlen.
Nicht alles tragen.

Ein zweiter Ton ist auch da:
ruhige, strukturierte Kraft.
Nicht Druck.
Eher ein stilles Ordnen.
Ein Schritt nach dem anderen.

Und noch etwas:
Inspiration ist offen.
Aber nicht jede Hoffnung ist schon Wahrheit.
Heute tut dir gut:
prüfen
langsamer werden
im Körper bleiben

Aus alter kosmischer Sicht ist der Himmel kein Dekor.
Er ist Orientierung.
Wie eine Achse zwischen innen und oben.
Deine Zeitqualität wirkt gerade wie:
Empfindsamkeit braucht Form.
Vision braucht Boden.

Wenn du magst, können wir jetzt tiefer schauen.
1 — wir gehen tiefer in dein Geburtsthema Fische und Sensibilität
2 — wir schauen auf deine aktuelle Zeitqualität und was heute günstig ist
3 — wir verbinden Astrologie mit Erdung im Körper und Atem
Oder schreib einfach was dich bewegt.
```

---

## 📁 Projektstruktur

```
Consciousness_Field_AI/
├── screenshots/
│   ├── Start_1.png
│   ├── Start_2.png
│   ├── ceremony_mode_1.png
│   ├── ceremony_mode_2.png
│   ├── conversation_mode_options_1.png
│   ├── conversation_mode_options_2.png
│   ├── freitext.png
│   ├── pflanzenwissen.png
│   └── horoskop.png
│   └── mond.png
├── Consciousness_Field_AI.json   # n8n Workflow Export
└── README.md                     # Diese Datei
```

---

## ⚠️ Hinweise

- Der Bot gibt **keine medizinischen Diagnosen** und ersetzt **keine Therapie**
- Alle Antworten werden durch OpenAI GPT-5.4 generiert und können variieren
- API-Keys und Credentials **niemals** im Repository speichern (`.env` verwenden)

---

## 📄 Lizenz

Dieses Projekt ist privat. Bitte nicht ohne Genehmigung weitergeben oder veröffentlichen.
