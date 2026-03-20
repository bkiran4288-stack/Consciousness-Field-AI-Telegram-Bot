# 🔥 Consciousness Field AI — Telegram Bot

A spiritual AI companion on Telegram — for people who want to listen inward.

Consciousness Field AI accompanies you with breathing exercises, yoga, plant knowledge, astrology and shamanic perspectives. The bot does not rely solely on AI, but on a real knowledge base: several spiritual and ethnobotanical books, embedded via RAG in Pinecone — so that answers are not hallucinated, but drawn from deep source knowledge.

Built with n8n · MongoDB · Pinecone · OpenAI.

---

## 📌 Problem Definition

Many people seek fast, low-threshold support in moments of inner restlessness, exhaustion or disorientation — without having to contact a therapist or coach directly.

**Consciousness Field AI** offers exactly that: an available, non-judgmental conversation partner in Telegram that responds individually to the user's current emotional state. The user can either choose one of the suggested categories (1–9) **or freely describe in their own words** what is happening inside them — without any required format.

The bot responds with breathing and body exercises, plant knowledge, indigenous & shamanic cultural wisdom, moon phases and personal horoscopes.

> The bot does not replace therapy. It is companionship — not a substitute.

---

## 🏗️ Technical Architecture

```
Telegram User
     │
     ▼
[n8n Workflow]
     │
     ├── Telegram Trigger (Incoming Message)
     │
     ├── Input Preprocessing (Normalize Fields)
     │        user_id / chat_id / user_text / timestamp
     │
     ├── Validation
     │    ├── Text empty? → Feedback to user
     │    └── /start Command? → Welcome message + create user
     │
     ├── MongoDB (ConsciousDB)
     │    ├── Fetch user (users collection)
     │    ├── Create new user
     │    ├── Save chat history
     │    └── Update user document (ceremony_mode, chat_memory, etc.)
     │
     ├── Mode Detection
     │    ├── Ceremony Mode (Numbers 1–9)
     │    └── Conversation Mode
     │         ├── Optional selection from 3 categories
     │         └── Free description in own words
     │
     ├── AI Agent (OpenAI GPT-5.4)
     │    │
     │    ├── RAG Tools (via Pinecone vector database)
     │    │    ├── Asana Book (Yoga & breathing exercises)
     │    │    ├── Apotheke Manitous (plant knowledge)
     │    │    ├── Psychoactive Plants
     │    │    ├── Encyclopedia of Herbs
     │    │    └── Shamanism Mircea (shamanism)
     │    │
     │    └── API Tools (external services)
     │         ├── ASTRO DAILY → freeastroapi.com (daily horoscope)
     │         ├── ASTRO Personal → freeastroapi.com (personal horoscope)
     │         ├── Moon API → moon-phase.p.rapidapi.com (moon phase & rhythm)
     │         └── Image API → api.gbif.org (plant & nature images)
     │
     ├── Code (JavaScript) → Prepare response
     │    ├── Send text response
     │    └── Search & send image (optional)
     │
     └── Telegram → Response to user
```

---

## 🛠️ Tools & Technologies Used

| Category | Tool / Service |
|---|---|
| Workflow Automation | n8n |
| Messaging | Telegram Bot API |
| Database | MongoDB (ConsciousDB) |
| Vector Database (RAG) | Pinecone |
| AI Model | OpenAI GPT-5.4 (via n8n AI Agent) |
| Embeddings | OpenAI Embeddings (for Pinecone RAG) |
| RAG Knowledge Sources | Encyclopedia of Herbs, Shamanism (Mircea Eliade), Apotheke Manitous, Psychoactive Plants, Asana Book (Yoga & Breath) |
| Astrology API | [freeastroapi.com](https://api.freeastroapi.com/) (daily & personal horoscope) |
| Moon API | [moon-phase.p.rapidapi.com](https://moon-phase.p.rapidapi.com/advanced) (moon phases & rhythm) |
| Image API | [api.gbif.org](https://api.gbif.org/v1/occurrence/search) (plant & nature images) |

---

## 🚀 Getting Started

### Prerequisites

- n8n (self-hosted or cloud) — [Installation Guide](https://docs.n8n.io/hosting/)
- MongoDB instance (e.g. Atlas or local)
- Pinecone account & API key — [pinecone.io](https://www.pinecone.io/)
- Telegram Bot Token ([BotFather](https://t.me/BotFather))
- OpenAI API Key
- Astro API Key — [freeastroapi.com](https://api.freeastroapi.com/)
- Moon API Key — [moon-phase via RapidAPI](https://rapidapi.com/)
- GBIF API (free, no key required) — [api.gbif.org](https://api.gbif.org/v1/occurrence/search)

### Step-by-Step

**1. Import Workflow**

In the n8n interface: `Workflows → Import from file → select Consciousness_Field_AI.json`

**2. Set Up Credentials**

The following credentials must be configured in n8n:

| Credential Name | Type | Description |
|---|---|---|
| `Consciousness Field AI` | Telegram API | Bot token from BotFather |
| `ConsciousDB` | MongoDB | Connection string to the database |
| `OpenAI` | OpenAI API | API key for GPT-5.4 & Embeddings |
| `Pinecone` | Pinecone API | API key for RAG vector database |
| `FreeAstroAPI` | HTTP Header Auth | API key for horoscope queries |
| `MoonPhase RapidAPI` | HTTP Header Auth | RapidAPI key for moon phases |

**3. Create MongoDB Collection**

```js
// In MongoDB Atlas or Compass:
db.createCollection("users")

// Example document (created automatically on /start):
{
  user_id: 123456789,
  created_at: "2024-01-01T00:00:00Z",
  ceremony_mode: false,
  chat_memory: []
}
```

**4. Activate Workflow**

- Open the workflow in n8n
- Click **"Activate"** in the top right
- Verify the Telegram webhook (click trigger node → copy webhook URL)

**5. Start Bot in Telegram**

```
/start
```

---

## 💬 Example Inputs & Bot Responses

### 1️⃣ First Contact — `/start`

**User writes:**
```
/start
```

**Bot responds:**
```
🔥 Welcome to the Consciousness Field

You have entered this space.

Take a moment.
Breathe in deeply once —
and as you exhale, let go of what you don't need right now.

─────────────────────

I am not an ordinary assistant.
I am a mirror — for what is present in you.

I work with:

🌬 Breath impulses & Pranayama
🧘 Yoga & body awareness
🌿 Plant knowledge & ethnobotany
🌍 Indigenous & shamanic perspectives
🌙 Moon rhythm & cosmic cycles
⭐ Your personal horoscope

I do not give medical diagnoses.
I do not replace therapy.
I am companionship — not a substitute.

─────────────────────

🔥 Opening of the Ceremony

Where are you right now?

🌊 Emotional State
1 — I feel fear or inner restlessness
2 — I am carrying something heavy or feel sad
3 — I am angry or internally tense

🌫 Mind & Focus
4 — I cannot concentrate
5 — I feel disoriented
6 — My mind is spinning too much

⚡ Energy & Body
7 — I am exhausted or lacking drive
8 — My body feels blocked
9 — I need movement or activation

─────────────────────

🌿 Knowledge & Nature
If you want to know something about plants, your moon, your horoscope or a breathing and yoga exercise — just write freely.

─────────────────────

⭐ For your personal horoscope —
simply share your date of birth,
time of birth and city of birth
whenever you like.

─────────────────────

Or simply describe in your own words
what is alive in you right now.

No right format. No right way.
Just as it is.
```

---

### 2️⃣ Ceremony Mode — Number Input

**User writes:**
```
1
```
*("I feel fear or inner restlessness")*

**Bot responds (example):**
```
Fear is there. That shows something is truly important to you.

🌬 Breath
The practice for this moment is Chandra Bhedana – left nostril breathing from classical Pranayama.

How to do it:
Sit upright.
Close your right nostril with your right thumb.
Breathe in through the left nostril for 4 seconds.
Hold the breath for 2 seconds.
Open the right nostril and breathe out through the right for 6 seconds.
Breathe in again through the left for 4 seconds, hold 2 seconds, breathe out right for 6 seconds.
That is one cycle → 6 rounds.

What this does in your system:
The extended exhale increases vagal tone via the parasympathetic nervous system.
Heart rate measurably decreases through respiratory sinus arrhythmia.
CO₂ levels remain more stable, which reduces the alarm sensation in the brainstem's breathing center.

🧘 Body & Asana
The practice for this moment is Shashankasana – Hare Pose, from classical Hatha Yoga.

How to get into it:
Kneel on the floor.
Lower your hips onto your heels.
Place your knees hip-width apart.
Bend your upper body forward until your forehead or the top of your head touches the ground.
Stretch both arms forward, elbows lifted.
Stay for 8 slow breaths or 2 minutes.

What this does in your body:
The forward fold releases the back extensors and lowers activity in the sympathetic nervous system.
The pressure of the abdomen on the thighs enhances diaphragm awareness and slows breathing.
The flexion posture reduces muscle tone in the neck and shoulder girdle through proprioceptive feedback.
After 1–2 minutes, parasympathetic dominance increases significantly.

🌿 Plant
Cypripedium pubescens carries a quiet nerve medicine within it. Among the Menominee, this orchid was placed in sacred bundles to open meaningful dreams. The Cherokee used it as a sedative against nervousness. Quinones such as cypripedine have been described in it, and exactly this signature acts here like soft grounding. Its quality for this moment is: calming.

🌍 Indigenous Perspective
Among the Thompson Indians of British Columbia there is the concrete notion that the soul can lose its way when a person falls out of balance. The healer then walks the same path the ancestors took to the land of the dead. He wears a mask, searches for the lost soul force and brings it back.

What this means for this moment: Not everything in you has disappeared. A part of you needs return right now, not pressure.

🌙 Moon Moment
Today is new moon. 0 days since the last full moon, 13 days until the next full moon. The moon is in Aries.

This phase pulls inward. It dissolves overstimulation and makes space for a new impulse.
Physiologically, the dark moon can shift sleep rhythm and dream depth. Feelings may seem rawer because orientation in the nervous system temporarily decreases.

Ritual for this evening:
Lights off except for a small candle.
Write exactly 1 sentence: "What I no longer want to carry today is ..."
Then 6 left-nostril breaths.
Afterwards fold the note and place it under a glass of water until tomorrow morning.

What wants to be called back in you right now?

1 — we go deeper into the breath
2 — we open more to the plant Cypripedium pubescens
3 — we go deeper into the soul return of the Thompson Indians
Or simply write what moves you.
```

---

### 3️⃣ Free Text — Emotional State

**User writes:**
```
I am completely exhausted right now and don't know how to go on
```

**Bot responds (example):**
```
You don't need to know how to go on right now.

With deep exhaustion, less technique often helps.
More simple returning.

For the body:
Lie down or lean against something.
Take 1 minute to simply observe the natural breath.
Don't control it.
Just notice: inhale. Exhale.

This simple breath awareness can calm the system.
Even just observing often slows the rhythm.

For the in-between state:
Small pauses are almost as important as any exercise.
Not just doing.
Also briefly doing nothing.

From an ancient shamanic perspective, deep exhaustion is sometimes not seen as weakness.
But as a distancing from something of one's own.
Healing then does not mean pressure.
But a calling back.
A quiet inner: come back.

The moon quality today is also very still.
New moon in Aries.
Little light.
More inner space.
This fits with withdrawal and saving energy.

Perhaps the gentlest question right now is not:
How do I go on?
But:
What in me needs safe ground first?

1 — we go even deeper into natural breath with exhaustion
2 — we look at the shamanic idea of calling back and returning
3 — we connect new moon and exhaustion more precisely
Or simply write what moves you.
```

---

### 4️⃣ Plant Knowledge

**User writes:**
```
What can Ashwagandha bring me?
```

**Bot responds (example):**
```
🌿 Ashwagandha (Withania somnifera)

Above all, Ashwagandha can bring you one thing: more stability.

Not like coffee.
More like a slow returning.

Withania somnifera is a plant from the nightshade family.
Primarily the root is used.
In Indian healing tradition it is considered restorative and regenerating.

Its direction is:
more calm in the nervous system
more grounding with exhaustion
more depth in sleep
more substance after long stress

Traditionally it was also carried as a strengthening tonic plant.
Not for a quick push.
For rebuilding.

Symbolically it carries something of:
steadfastness
warmth
quiet strength
return to the body

If you are empty right now, that could be exactly the quality needed.
Not performance.
But reserve.

And one more thing:
In many healing traditions, exhaustion is not only about energy.
But about retrieving something that has moved away.
Ashwagandha fits this image of quiet return.

If you like, we can next look concretely at effects, how it feels to take it, or the deeper symbolism.

1 — we look concretely at Ashwagandha for stress and sleep
2 — we go deeper into the symbolism of Withania somnifera and return
3 — we look at exhaustion from a shamanic perspective of soul return
Or simply write what moves you.
```

---

### 5️⃣ Personal Horoscope

**User writes:**
```
My birthday is March 15, 1990, 2:30 PM, Berlin
```

**Bot responds (example):**
```
⭐ Your Personal Horoscope

Thank you. I have your birth data clearly.

March 15, 1990
2:30 PM
Berlin

The basic direction feels Piscean.
Soft.
Receptive.
Sensitive to moods.

What shows right now:
Compassion is strong.
But boundaries are important right now.
Not feeling everything.
Not carrying everything.

A second tone is also present:
quiet, structured strength.
Not pressure.
More a silent ordering.
One step at a time.

And one more thing:
Inspiration is open.
But not every hope is already truth.
What is good for you today:
checking
slowing down
staying in the body

From an ancient cosmic perspective, the sky is not decoration.
It is orientation.
Like an axis between inner and above.
Your current time quality feels like:
Sensitivity needs form.
Vision needs ground.

If you like, we can look deeper now.
1 — we go deeper into your birth theme Pisces and sensitivity
2 — we look at your current time quality and what is favorable today
3 — we connect astrology with grounding in the body and breath
Or simply write what moves you.
```

---

## 📁 Project Structure

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
└── README.md                     # This file
```

---

## ⚠️ Notes

- The bot does **not provide medical diagnoses** and does **not replace therapy**
- All responses are generated by OpenAI GPT-5.4 and may vary
- API keys and credentials must **never** be stored in the repository (use `.env`)

---

## 📄 License

This project is private. Please do not share or publish without permission.
