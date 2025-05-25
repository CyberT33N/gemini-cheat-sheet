# Gemini Cheat Sheet






<br><br>
<br><br>
___
<br><br>
<br><br>


# Embeddings



<details><summary>Click to expand..</summary>


# Google Gemini & Text Embedding Modelle – Übersicht
- https://ai.google.dev/gemini-api/docs/models#embedding


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

<details><summary>Click to expand..</summary>

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


</details>





## Embeddings kürzen (`outputDimensionality`)

<details><summary>Click to expand..</summary>

Einige neuere Modelle (wie `text-embedding-004` und `gemini-embedding-exp-03-07`) unterstützen die Reduzierung der Output-Dimensionen.

```javascript
async function getTruncatedEmbedding(textToEmbed, targetDimension, modelName = "models/text-embedding-004") {
  const genAI = new GoogleGenAI({ apiKey: PLACEHOLDER_API_KEY });
  try {
    // Standard-Embedding (volle Dimension)
    const resultFull = await genAI.models.embedContent({
      model: modelName,
      contents: textToEmbed,
    });
    const embeddingFull = resultFull.embeddings.values;

    // Gekürztes Embedding
    const resultTruncated = await genAI.models.embedContent({
      model: modelName,
      contents: textToEmbed,
      config: {
        outputDimensionality: targetDimension,
      },
    });
    const embeddingTruncated = resultTruncated.embeddings.values;

    console.log(`Text: "${textToEmbed}"`);
    console.log(`  Volle Dimension (${modelName}): ${embeddingFull.length}`);
    console.log(`  Gekürzte Dimension: ${embeddingTruncated.length} (Ziel: ${targetDimension})`);
    // console.log(`  Gekürztes Embedding: [${embeddingTruncated.slice(0, 4).join(', ')}, ... TRIMMED]`);

    return { embeddingFull, embeddingTruncated };
  } catch (error)
    console.error("Fehler beim Kürzen des Embeddings:", error);
    return null;
  }
}

// Beispielaufruf:
// getTruncatedEmbedding("Hallo Welt", 10); // Kürzt auf 10 Dimensionen
// getTruncatedEmbedding("Testtext für Dimensionen", 256, "models/gemini-embedding-exp-03-07"); // Mit Gemini Modell
```
*Hinweis: Das `embedding-001` Modell unterstützt `outputDimensionality` nicht.*

</details>



### Batch-Embeddings erstellen

<details><summary>Click to expand..</summary>

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


</details>


## Task Types (Aufgabentypen)

<details><summary>Click to expand..</summary>

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













# Beispiele

<details><summary>Click to expand..</summary>


## Beispiel: Search Reranking (Konzept in Node.js)

<details><summary>Click to expand..</summary>

Das Python-Beispiel für Search Reranking ist komplex und involviert Wikipedia-API-Aufrufe, Function Calling und Numpy. Hier ist eine vereinfachte konzeptionelle Darstellung in Node.js, die den Kern des Rerankings mit Embeddings zeigt.

```javascript
// Hilfsfunktion für Kosinus-Ähnlichkeit (aus vorherigem OpenAI Cheatsheet)
function cosineSimilarity(vecA, vecB) {
  if (!vecA || !vecB || vecA.length !== vecB.length || vecA.length === 0) return 0;
  let dotProduct = 0, normA = 0, normB = 0;
  for (let i = 0; i < vecA.length; i++) {
    dotProduct += vecA[i] * vecB[i];
    normA += vecA[i] * vecA[i];
    normB += vecB[i] * vecB[i];
  }
  normA = Math.sqrt(normA); normB = Math.sqrt(normB);
  if (normA === 0 || normB === 0) return 0;
  return dotProduct / (normA * normB);
}

async function rerankSearchResults(userQuery, searchResultsTexts, embeddingModel = "models/text-embedding-004") {
  const genAI = new GoogleGenAI({ apiKey: PLACEHOLDER_API_KEY });

  try {
    // 1. Embedde die Nutzeranfrage
    const queryEmbeddingResponse = await genAI.models.embedContent({
      model: embeddingModel,
      contents: userQuery,
      config: { taskType: "RETRIEVAL_QUERY" } // Wichtig für Suchanfragen
    });
    const queryVector = queryEmbeddingResponse.embeddings.values;

    if (!queryVector) {
      console.error("Konnte Query-Embedding nicht erstellen.");
      return searchResultsTexts; // Fallback: Unsortierte Ergebnisse
    }

    // 2. Embedde alle Suchergebnisse (Dokumente)
    // In einer echten Anwendung würden diese Embeddings oft schon in einer Vektor-DB liegen.
    const documentEmbeddingsResponse = await genAI.models.embedContent({
      model: embeddingModel,
      contents: searchResultsTexts,
      config: { taskType: "RETRIEVAL_DOCUMENT" } // Wichtig für Dokumente
    });
    const documentVectors = documentEmbeddingsResponse.embeddings.map(e => e.values);

    // 3. Berechne Ähnlichkeit und sortiere
    const resultsWithSimilarity = searchResultsTexts.map((text, index) => {
      const docVector = documentVectors[index];
      if (!docVector) return { text, similarity: -1 }; // Fallback, falls ein Embedding fehlt
      return {
        text: text,
        similarity: cosineSimilarity(queryVector, docVector)
      };
    });

    resultsWithSimilarity.sort((a, b) => b.similarity - a.similarity);

    console.log("\n--- Reranked Search Results ---");
    resultsWithSimilarity.forEach(res => {
      console.log(`Ähnlichkeit: ${res.similarity.toFixed(4)} - Text: ${res.text.substring(0, 50)}...`);
    });

    return resultsWithSimilarity.map(res => res.text);

  } catch (error) {
    console.error("Fehler beim Reranking:", error);
    return searchResultsTexts; // Fallback
  }
}

// Beispielaufruf für Reranking:
// const userQuery = "Wie überlebt Leben in der Tiefsee?";
// const initialSearchResults = [
//   "Tiefsee-Organismen nutzen Chemosynthese an hydrothermalen Quellen.", // Relevant
//   "Der Marianengraben ist der tiefste Punkt der Erde.", // Weniger relevant
//   "Biolumineszenz ist eine häufige Anpassung in der Tiefsee.", // Relevant
//   "Druckanpassung ist entscheidend für Tiefseefische." // Sehr relevant
// ];
// rerankSearchResults(userQuery, initialSearchResults, "models/gemini-embedding-exp-03-07");
```

</details>


## Integration mit ChromaDB (Konzept in Node.js)

<details><summary>Click to expand..</summary>

ChromaDB hat einen offiziellen JavaScript-Client (`chromadb`). Hier ist ein konzeptioneller Ablauf:

```javascript
import { ChromaClient } from 'chromadb';
// import { GoogleGenAI } from "@google/genai"; // Bereits oben importiert

// Definieren einer benutzerdefinierten Embedding-Funktion für ChromaDB mit dem Gemini SDK
class GeminiEmbeddingFunctionForChroma {
  constructor(apiKey, modelName = "models/text-embedding-004", taskType = "RETRIEVAL_DOCUMENT", title = undefined) {
    this.genAI = new GoogleGenAI({ apiKey });
    this.modelName = modelName;
    this.taskType = taskType;
    this.title = title;
  }

  async generate(texts) { // Chroma erwartet eine Methode 'generate'
    try {
      const config = { taskType: this.taskType };
      if (this.title && this.taskType === "RETRIEVAL_DOCUMENT") {
        config.title = this.title;
      }

      const response = await this.genAI.models.embedContent({
        model: this.modelName,
        contents: texts, // Chroma übergibt ein Array von Texten
        config: config,
      });
      // Chroma erwartet ein Array von Embedding-Vektoren
      return response.embeddings.map(e => e.values);
    } catch (error) {
      console.error("Fehler in GeminiEmbeddingFunctionForChroma:", error);
      // Chroma braucht für jeden Text ein Embedding, ggf. mit Null-Vektoren füllen oder Fehler werfen
      return texts.map(() => []); // Leere Arrays als Fallback, nicht ideal
    }
  }
}


async function useChromaWithGemini() {
  const chroma = new ChromaClient(); // Standardmäßig In-Memory
  // const chroma = new ChromaClient({ path: "http://localhost:8000" }); // Für einen laufenden Chroma Server

  const geminiEmbedder = new GeminiEmbeddingFunctionForChroma(PLACEHOLDER_API_KEY, "models/text-embedding-004", "RETRIEVAL_DOCUMENT");

  try {
    // Collection erstellen (oder laden, falls sie existiert)
    const collectionName = "google_cars_node";
    let collection;
    try {
      collection = await chroma.getCollection({
        name: collectionName,
        embeddingFunction: geminiEmbedder // Wichtig: Embedding-Funktion übergeben
      });
      console.log(`Collection "${collectionName}" geladen.`);
    } catch (e) {
      console.log(`Collection "${collectionName}" nicht gefunden, erstelle neu...`);
      collection = await chroma.createCollection({
        name: collectionName,
        embeddingFunction: geminiEmbedder
      });
      console.log(`Collection "${collectionName}" erstellt.`);

      // Dokumente hinzufügen (nur beim ersten Erstellen)
      const documents = [
        "Bedienung der Klimaanlage: Ihr Googlecar verfügt über eine Klimaanlage...",
        "Ihr Googlecar hat ein großes Touchscreen-Display für Navigation, Unterhaltung...",
        "Schalten der Gänge: Ihr Googlecar hat ein Automatikgetriebe..."
      ];
      const ids = documents.map((_, i) => `doc${i + 1}`); // Eindeutige IDs

      await collection.add({
        ids: ids,
        documents: documents,
        // Metadaten können hier auch hinzugefügt werden
      });
      console.log(`${documents.length} Dokumente zur Collection hinzugefügt.`);
    }


    // Abfrage der Collection
    const queryText = "Wie benutze ich den Touchscreen im Google Auto?";
    const nResults = 1;

    const results = await collection.query({
      queryTexts: [queryText], // Muss ein Array sein
      nResults: nResults,
      // Optional: 'include: ["documents", "distances", "metadatas"]'
    });

    console.log(`\nAbfrage: "${queryText}"`);
    if (results.documents && results.documents.length > 0 && results.documents[0].length > 0) {
      const relevantPassage = results.documents[0][0];
      console.log("Relevanteste Passage:", relevantPassage);

      // Hier könnte dann ein Prompt für ein LLM (z.B. Gemini Pro) gebaut werden:
      // const prompt = `Beantworte die Frage '${queryText}' basierend auf folgender Passage: '${relevantPassage}'`;
      // const llm = genAI.getGenerativeModel({ model: "gemini-pro" });
      // const llmResponse = await llm.generateContent(prompt);
      // console.log("Antwort vom LLM:", llmResponse.response.text());

    } else {
      console.log("Keine relevanten Passagen gefunden.");
    }

  } catch (error) {
    console.error("Fehler bei der ChromaDB-Integration:", error);
  }
}

// Beispielaufruf für ChromaDB:
// useChromaWithGemini();
```

</details>

</details>











<br><br>
<br><br>

## API Referenz-Punkte (Node.js relevant)

<details><summary>Click to expand..</summary>

*   **`ai.models.embedContent(request)`**:
    *   `request.model`: String (z.B. `"models/text-embedding-004"`)
    *   `request.contents`: String oder Array von Strings.
    *   `request.config` (optional):
        *   `taskType`: Enum (z.B. `"RETRIEVAL_DOCUMENT"`, `"CLASSIFICATION"`)
        *   `title`: String (nur für `taskType: "RETRIEVAL_DOCUMENT"`)
        *   `outputDimensionality`: Integer (z.B. `10`, `256`)
    *   Gibt ein Objekt zurück, das `.embeddings` enthält.
        *   Wenn `contents` ein String war, ist `.embeddings` ein `ContentEmbedding`-Objekt (mit `.values` für den Vektor).
        *   Wenn `contents` ein Array war, ist `.embeddings` ein Array von `ContentEmbedding`-Objekten.

*   **`ai.models.batchEmbedContents(request)`**: (Ältere Methode, `embedContent` mit Array für `contents` ist meist einfacher)
    *   `request.model`: String
    *   `request.requests`: Array von `EmbedContentRequest`-Objekten.
    *   Jedes `EmbedContentRequest`-Objekt hat `model`, `content`, `taskType`, `title`, `outputDimensionality`.

*   **`ContentEmbedding` Objekt:**
    *   `values`: Array von Zahlen (der eigentliche Embedding-Vektor).

*   **`TaskType` Enums (Auszug):**
    *   `RETRIEVAL_QUERY`
    *   `RETRIEVAL_DOCUMENT`
    *   `SEMANTIC_SIMILARITY`
    *   `CLASSIFICATION`
    *   `CLUSTERING`
    *   (Vollständige Liste siehe Google AI Dokumentation)

---

**Hinweis:** Die Beispiele für "Anomaly Detection" und "Train a text classifier" sind komplexer und erfordern ML-Bibliotheken, die über den reinen Embedding-Prozess hinausgehen (wie `sklearn`, `pandas`, `keras` in Python). Eine direkte 1:1-Umwandlung in einfaches Node.js ohne äquivalente ML-Bibliotheken (z.B. `tensorflow.js` oder spezialisierte Pakete) ist sehr aufwendig und würde den Rahmen dieses Embedding-Cheatsheets sprengen. Die Kernidee (Embeddings erstellen und dann als Features für ML-Modelle nutzen) bleibt jedoch gleich.

</details>
  
</details>


