# Gemini Cheat Sheet






<br><br>
<br><br>
___
<br><br>
<br><br>


# Embeddings



<details><summary>Click to expand..</summary>


# Google Gemini & Text Embedding Modelle – Übersicht


<details><summary>Click to expand..</summary>


## 1. `gemini-embedding-exp-03-07` (Neuestes & Experimentell)

*   **Performance:** Erreicht **State-of-the-Art (SOTA)** Performance in vielen Schlüsselbereichen, einschließlich **Code, Mehrsprachigkeit und Retrieval**.
*   **Status:** Experimentelles Modell.
*   **Rate Limits:** **Eingeschränkter** als andere Modelle aufgrund des experimentellen Status.
*   **Modellcode (Gemini API):** `gemini-embedding-exp-03-07`
*   **Datentypen:**
    *   Input: Text
    *   Output: Text-Embeddings
*   **Token Limits:**
    *   Input: **8.192 Tokens**
*   **Output-Dimensionen:** **Elastisch**, unterstützt: **3072, 1536 oder 768** Dimensionen.
    *   *Hinweis: Die Möglichkeit, die Dimension zu wählen, ist ein wichtiger Vorteil für Flexibilität (Performance vs. Kosten/Größe).*
*   **`taskType` Support:** (Obwohl nicht explizit hier erwähnt, ist dies das Modell, das typischerweise `taskType` unterstützt, wie in vorherigen Informationen gesehen – dies sollte überprüft werden, aber die SOTA-Retrieval-Performance deutet darauf hin).
*   **Letztes Update:** März 2025 *(Anmerkung: Dies scheint ein Tippfehler zu sein, da das aktuelle Datum noch nicht März 2025 ist. Wahrscheinlich März 2024 oder ein Platzhalter. In Cheatsheets immer das tatsächliche Update-Datum vermerken, falls bekannt.)*
*   **Empfehlung:** Für höchste Performance und wenn spezifische Optimierungen (wie Code, Mehrsprachigkeit, wählbare Dimensionen) benötigt werden. Vorsicht bei den Rate Limits für produktive, hochfrequente Anwendungen.

## 2. `text-embedding-004` (Aktuelles Standard Text-Embedding)

*   **Performance:** Erreicht eine **starke Retrieval-Performance** und übertrifft bestehende Modelle mit vergleichbaren Dimensionen auf Standard MTEB Embedding-Benchmarks.
*   **Status:** Stabiles, empfohlenes Text-Embedding-Modell.
*   **Modellcode (Gemini API):** `models/text-embedding-004`
*   **Datentypen:**
    *   Input: Text
    *   Output: Text-Embeddings
*   **Token Limits:**
    *   Input: **2.048 Tokens**
*   **Output-Dimensionen:** **768** Dimensionen (fix).
*   **Rate Limits:** 1.500 Anfragen pro Minute.
*   **Einstellbare Sicherheitseinstellungen:** Nicht unterstützt.
*   **`taskType` Support:** Wahrscheinlich nicht, da es als allgemeines Text-Embedding-Modell positioniert ist und die `taskType`-Funktionalität eher bei den `gemini-embedding-*` Modellen liegt.
*   **Letztes Update:** April 2024.
*   **Empfehlung:** Gutes Allround-Modell für Text-Embeddings mit starker Retrieval-Performance, wenn keine variablen Dimensionen oder die spezifischen Optimierungen von `gemini-embedding-exp-03-07` benötigt werden. Derzeit das empfohlene Modell, wenn man ein Projekt neu startet und ein "normales" Text-Embedding braucht.

## 3. `embedding-001` (Legacy / Älter)

*   **Wichtiger Hinweis:** **`text-embedding-004` ist die neuere Version.** Für neue Projekte sollte `text-embedding-004` verwendet werden.
*   **Performance:** Optimiert für die Erstellung von Embeddings mit 768 Dimensionen. Gilt als weniger performant als `text-embedding-004` und `gemini-embedding-exp-03-07`.
*   **Status:** Älteres Modell.
*   **Modellcode (Gemini API):** `models/embedding-001`
*   **Datentypen:**
    *   Input: Text
    *   Output: Text-Embeddings
*   **Token Limits:**
    *   Input: **2.048 Tokens**
*   **Output-Dimensionen:** **768** Dimensionen (fix).
*   **Rate Limits:** 1.500 Anfragen pro Minute.
*   **Einstellbare Sicherheitseinstellungen:** Nicht unterstützt.
*   **`taskType` Support:** Nein.
*   **Letztes Update:** Dezember 2023.
*   **Empfehlung:** **Nicht für neue Projekte empfohlen.** Hauptsächlich für Abwärtskompatibilität bestehender Anwendungen.

---

**Zentrale Unterschiede auf einen Blick:**

| Feature                        | `gemini-embedding-exp-03-07` | `text-embedding-004`            | `embedding-001` (Legacy)       |
| :----------------------------- | :--------------------------- | :------------------------------ | :----------------------------- |
| **Performance-Fokus**        | SOTA (Code, Multilingual, Retrieval) | Starke Retrieval (MTEB)         | Basis-Embeddings               |
| **Status**                     | Experimentell                | Empfohlen für neue Text-Projekte | Veraltet für neue Projekte     |
| **Input Token Limit**          | **8.192**                    | 2.048                           | 2.048                          |
| **Output Dimensionen**         | **Elastisch (3072, 1536, 768)** | 768 (fix)                       | 768 (fix)                      |
| **Rate Limits**                | Eingeschränkter              | 1.500 RPM                       | 1.500 RPM                      |
| **Wahrscheinlicher `taskType` Support** | **Ja**                       | Eher Nein                       | Nein                           |
| **Empfehlung für Neues**     | Für Spitzenleistung/Flexibilität | **Ja (Standard Text)**        | Nein                           |

Dieses Cheatsheet sollte dir helfen, schnell das passende Google Embedding-Modell für deine Anforderungen zu finden.

</details>






<br><br>
<br><br>









## Embeddings generieren

Mit der Methode `embedContent` des GoogleGenAI SDKs können Text-Embeddings erstellt werden.

**Voraussetzungen:**
*   Installiere das SDK: `npm install @google/genai`
*   Besorge einen `GEMINI_API_KEY`.

### Standard-Embedding erstellen (Node.js)

```javascript
import { GoogleGenAI } from "@google/genai";

async function generateEmbedding() {
  // API-Key sollte sicher verwaltet werden, z.B. über Umgebungsvariablen
  const ai = new GoogleGenAI({ apiKey: "DEIN_GEMINI_API_KEY" });

  try {
    const response = await ai.models.embedContent({
      model: 'gemini-embedding-exp-03-07', // Oder ein anderes unterstütztes Modell
      contents: 'Was ist der Sinn des Lebens?', // Der zu embeddende Text
    });

    // response.embeddings enthält das generierte Embedding-Objekt oder den Vektor
    // Die genaue Struktur hängt von der API-Antwort ab, aber .embeddings ist der Zugriffspunkt.
    console.log("Embedding-Daten:", response.embeddings);
    // Um den reinen Vektor zu erhalten, könnte es z.B. response.embeddings.values sein,
    // je nach der genauen Struktur des zurückgegebenen Objekts.
    // Das bereitgestellte Beispiel loggt direkt response.embeddings.
  } catch (error) {
    console.error("Fehler beim Generieren des Embeddings:", error);
  }
}

generateEmbedding();
```

### Batch-Embeddings erstellen

Du kannst Embeddings für mehrere Text-Chunks gleichzeitig erstellen, indem du ein Array von Strings an `contents` übergibst:

```javascript
// ... (Setup wie oben)
const response = await ai.models.embedContent({
  model: 'gemini-embedding-exp-03-07',
  contents: [
    'Text-Chunk 1 für Embedding.',
    'Ein weiterer Text-Chunk.',
    'Und noch einer.'
  ],
  // Ggf. mit taskType, siehe unten
});
// response.embeddings wird dann wahrscheinlich ein Array von Embedding-Objekten/Vektoren sein.
console.log(response.embeddings);
```

## Task Types (Aufgabentypen)

Für Systeme wie Retrieval Augmented Generation (RAG) ist es wichtig, dass die Embeddings für die spezifische Aufgabe optimiert sind. Fragen und Antworten sind semantisch oft nicht direkt ähnlich (z.B. "Warum ist der Himmel blau?" vs. "Lichtstreuung...").

**Task Types** ermöglichen es, optimierte Embeddings für spezifische Aufgaben zu generieren, was Performanz verbessern und Kosten/Zeit sparen kann.

### Embedding mit Task Type erstellen (Node.js)

```javascript
import { GoogleGenAI } from "@google/genai";

async function generateEmbeddingWithTaskType() {
  const ai = new GoogleGenAI({ apiKey: "DEIN_GEMINI_API_KEY" });

  try {
    const response = await ai.models.embedContent({
      model: 'gemini-embedding-exp-03-07',
      contents: 'Was ist der Sinn des Lebens?',
      config: {
        taskType: "SEMANTIC_SIMILARITY", // Wähle den passenden Task Type
      }
    });

    console.log("Embedding-Daten mit Task Type:", response.embeddings);
  } catch (error) {
    console.error("Fehler beim Generieren des Embeddings mit Task Type:", error);
  }
}

generateEmbeddingWithTaskType();
```

### Unterstützte Task Types:

| Task Type                 | Beschreibung                                                                                                                               |
| :------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------- |
| `SEMANTIC_SIMILARITY`     | Optimiert für die Bewertung von Textähnlichkeit.                                                                                           |
| `CLASSIFICATION`          | Optimiert für die Klassifizierung von Texten gemäß vordefinierter Labels.                                                                  |
| `CLUSTERING`              | Optimiert für das Clustering von Texten basierend auf ihren Ähnlichkeiten.                                                                  |
| `RETRIEVAL_DOCUMENT`      | Optimiert für das Embedding von Dokumenten, die für die Suche/Informationsbeschaffung indexiert werden sollen.                             |
| `RETRIEVAL_QUERY`         | Optimiert für das Embedding von Suchanfragen, um Dokumente zu finden.                                                                      |
| `QUESTION_ANSWERING`      | Optimiert für Frage-Antwort-Szenarien (oft in Verbindung mit `RETRIEVAL_DOCUMENT` und `RETRIEVAL_QUERY`).                                    |
| `FACT_VERIFICATION`       | Optimiert für die Überprüfung von Fakten.                                                                                                  |
| `CODE_RETRIEVAL_QUERY`    | Optimiert für die Suche nach Code-Blöcken basierend auf einer natürlichsprachlichen Anfrage. Embeddings der Code-Blöcke mit `RETRIEVAL_DOCUMENT`. |

## Anwendungsfälle

*   **Informationsbeschaffung**: Semantisch ähnliche Texte zu einem Input finden.
*   **Clustering**: Verborgene Trends durch Vergleich von Embedding-Gruppen identifizieren.
*   **Vektor-Datenbank**: Speicherung von Embeddings für Produktionsanwendungen.
*   **Klassifikation**: Training eines Modells mit Embeddings zur Dokumentenkategorisierung.

## Embedding-Modelle (Gemini API)

Die Gemini API bietet folgende Modelle zur Erstellung von Text-Embeddings:

*   `gemini-embedding-exp-03-07`
*   `text-embedding-004`
*   `embedding-001` (möglicherweise ein älteres Modell, `gemini-embedding-*` und `text-embedding-004` sind neuer)

Wähle das Modell, das am besten zu deinen Anforderungen an Kosten, Qualität und unterstützte Features (wie Task Types) passt.
  
</details>


