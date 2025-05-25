# Gemini Cheat Sheet






<br><br>
<br><br>
___
<br><br>
<br><br>


# Embeddings


<details><summary>Click to expand..</summary>


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


