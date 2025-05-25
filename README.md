# Gemini Cheat Sheet






<br><br>
<br><br>
___
<br><br>
<br><br>



# NPM @google/genai
- https://www.npmjs.com/package/@google/genai



# Docs

<details><summary>Click to expand..</summary>

Das Google Gen AI JavaScript SDK ermöglicht Entwicklern, Anwendungen mit Gemini zu erstellen. Es unterstützt sowohl die Gemini Developer API als auch Vertex AI und ist für Gemini 2.0+ Features ausgelegt.

**Wichtige Hinweise:**
*   **API-Key-Sicherheit:** API-Keys **niemals** im Client-seitigen Code exponieren. Für Produktionsumgebungen serverseitige Implementierungen verwenden.
*   **Dokumentation:** [Offizielle Dokumentation](https://googleapis.github.io/js-genai/)

## Voraussetzungen

*   Node.js Version 18 oder höher

## Installation

```bash
npm install @google/genai
```

## Quickstart (Gemini Developer API)

```javascript
import { GoogleGenAI } from '@google/genai';

// API Key aus Umgebungsvariablen laden (empfohlen)
const GEMINI_API_KEY = process.env.GEMINI_API_KEY;

// Initialisierung für Gemini Developer API
const ai = new GoogleGenAI({ apiKey: GEMINI_API_KEY });

async function main() {
  // Modell auswählen (z.B. 'gemini-1.5-flash', 'gemini-1.0-pro')
  const model = 'gemini-1.5-flash-latest'; // Oder ein spezifisches Modell wie 'gemini-1.5-flash-001'

  const response = await ai.models.generateContent({
    model: model,
    contents: [{ role: 'user', parts: [{ text: 'Warum ist der Himmel blau?' }] }],
  });
  console.log(response.text);
}

main();
```

## Initialisierung (`GoogleGenAI` Klasse)

Die Kernklasse für den Zugriff auf alle API-Funktionen.

### 1. Gemini Developer API (Server-seitig & Browser)

```javascript
import { GoogleGenAI } from '@google/genai';
const ai = new GoogleGenAI({ apiKey: 'DEIN_GEMINI_API_KEY' });
```
> **Achtung (Browser):** API-Keys nicht im Client-Code hardcoden! Serverseitige Lösungen für Produktionsanwendungen verwenden.

### 2. Vertex AI

```javascript
import { GoogleGenAI } from '@google/genai';

const ai = new GoogleGenAI({
    vertexai: true,
    project: 'DEIN_GOOGLE_CLOUD_PROJEKT_ID',
    location: 'DEINE_GOOGLE_CLOUD_REGION', // z.B. 'us-central1'
});
```

### API-Version auswählen

Standardmäßig werden Beta-Endpunkte verwendet. Für stabile Endpunkte `v1` setzen:

**Vertex AI mit `v1`:**
```javascript
const ai = new GoogleGenAI({
    vertexai: true,
    project: 'DEIN_PROJEKT',
    location: 'DEINE_REGION',
    apiVersion: 'v1' // oder 'v1beta'
});
```

**Gemini Developer API mit `v1beta` (oder spezifischer):**
```javascript
const ai = new GoogleGenAI({
    apiKey: 'DEIN_API_KEY',
    apiVersion: 'v1beta' // oder z.B. 'v1alpha' für experimentelle Features
});
```

## `GoogleGenAIOptions` (Konfigurationsoptionen)

Wichtige Optionen für den `GoogleGenAI` Konstruktor:

*   `apiKey?: string`: Erforderlich für Gemini Developer API.
*   `vertexai?: boolean`: `true` für Vertex AI, `false` (oder nicht gesetzt) für Gemini Developer API.
*   `project?: string`: Erforderlich für Vertex AI (Google Cloud Projekt ID).
*   `location?: string`: Erforderlich für Vertex AI (Google Cloud Region).
*   `apiVersion?: string`: API-Version (z.B. `v1`, `v1beta`).
*   `googleAuthOptions?`: Authentifizierungsoptionen für Vertex AI (Node.js).
*   `httpOptions?`: HTTP-Request Konfiguration.

## `GoogleGenAI` Submodule Übersicht

Alle API-Features sind über eine Instanz der `GoogleGenAI`-Klasse zugänglich.

*   `ai.models`: Modelle abfragen (Inhalt generieren, Bilder, Embeddings etc.), Metadaten untersuchen.
*   `ai.caches`: Caches erstellen und verwalten, um Kosten bei wiederholter Verwendung großer Prompt-Präfixe zu senken.
*   `ai.chats`: Lokale, zustandsbehaftete Chat-Objekte für vereinfachte Multi-Turn-Interaktionen erstellen.
*   `ai.files`: Dateien zur API hochladen und in Prompts referenzieren (reduziert Bandbreite, handhabt große Dateien).
*   `ai.live` (Experimentell): Live-Sitzungen für Echtzeitinteraktion (Text, Audio, Video Input; Text, Audio Output).
*   `ai.operations`: Status von langlaufenden Operationen abfragen (z.B. Videoerstellung).
*   `ai.tunings` (Experimentell): Fine-Tuning von Modellen verwalten.

## Kernfunktionen

### Inhalt generieren (`ai.models.generateContent`)

```javascript
async function generate() {
  const response = await ai.models.generateContent({
    model: 'gemini-1.5-flash-latest',
    contents: [{ role: 'user', parts: [{ text: 'Erzähle einen Witz.' }] }],
    // Optionale Konfiguration:
    config: {
      candidateCount: 1,
      maxOutputTokens: 100,
      temperature: 0.7,
      // safetySettings, stopSequences, etc.
    }
  });
  console.log(response.text);
  // response.candidates enthält alle generierten Kandidaten
  // response.promptFeedback enthält Feedback zum Prompt
}
```

**Struktur des `contents` Arguments:**
Kann sein:
*   `Content`: Wird in `Content[]` verpackt.
*   `Content[]`: Direkte Verwendung. Struktur: `[{ role: 'user' | 'model', parts: [Part, ...] }, ...]`.
*   `Part | string`: Wird zu `Content` mit `role: 'user'` und in `Content[]` verpackt.
*   `Part[] | string[]`: Wird zu einem einzigen `Content` mit `role: 'user'` und in `Content[]` verpackt.
*   **Wichtig:** Für `FunctionCall` und `FunctionResponse` Parts muss die volle `Content[]` Struktur explizit angegeben werden.

### Streaming (`ai.models.generateContentStream`)

Für schnellere, responsive Interaktionen.

```javascript
async function streamContent() {
  const responseStream = await ai.models.generateContentStream({
    model: 'gemini-1.5-flash-latest',
    contents: [{ role: 'user', parts: [{ text: 'Schreibe ein kurzes Gedicht über Code.' }] }],
  });

  let fullText = "";
  for await (const chunk of responseStream) {
    // chunk.text liefert den Text des aktuellen Chunks
    // chunk.candidates, chunk.promptFeedback etc. sind auch verfügbar
    if (chunk.text) {
      fullText += chunk.text;
      process.stdout.write(chunk.text); // Gibt Chunks direkt aus
    }
  }
  console.log('\n\n--- Vollständiger Text ---\n', fullText);
}
```

### Function Calling

Ermöglicht Gemini, mit externen Systemen zu interagieren.
**4 Schritte:**
1.  Funktionsname, Beschreibung und Parameter deklarieren.
2.  `generateContent` mit aktivierter Funktionsaufruf-Option aufrufen.
3.  Die zurückgegebenen `FunctionCall`-Parameter verwenden, um die tatsächliche Funktion aufzurufen.
4.  Das Ergebnis als `FunctionResponse` zurück an das Modell senden (einfacher mit `ai.chat`).

```javascript
import { FunctionCallingConfigMode, FunctionDeclaration, Type } from '@google/genai';

async function callFunction() {
  const controlLightDeclaration: FunctionDeclaration = {
    name: 'controlLight',
    description: 'Helligkeit und Farbtemperatur eines Raumlichts einstellen.',
    parameters: {
      type: Type.OBJECT,
      properties: {
        brightness: { type: Type.NUMBER, description: 'Lichtlevel 0-100' },
        colorTemperature: { type: Type.STRING, description: '`daylight`, `cool`, oder `warm`' },
      },
      required: ['brightness', 'colorTemperature'],
    },
  };

  const response = await ai.models.generateContent({
    model: 'gemini-1.5-flash-latest', // Ein Modell, das Function Calling unterstützt
    contents: [{ role: 'user', parts: [{ text: 'Dimme das Licht, damit der Raum gemütlich und warm wirkt.' }] }],
    config: {
      toolConfig: {
        functionCallingConfig: {
          mode: FunctionCallingConfigMode.ANY, // Oder .AUTO, .NONE
          allowedFunctionNames: ['controlLight'],
        }
      },
      tools: [{ functionDeclarations: [controlLightDeclaration] }]
    }
  });

  if (response.functionCalls && response.functionCalls.length > 0) {
    console.log('Funktionsaufruf angefordert:', response.functionCalls[0]);
    // Hier die Funktion `controlLight` mit `response.functionCalls[0].args` aufrufen
    // und das Ergebnis zurück an das Modell senden.
  } else {
    console.log('Kein Funktionsaufruf, normale Antwort:', response.text);
  }
}
```

### Chat (`ai.chats`)

Für zustandsbehaftete Konversationen.

**1. Chat-Sitzung erstellen (`ai.chats.create`)**
```javascript
const chat = ai.chats.create({
  model: 'gemini-1.5-flash-latest',
  // Optionale globale Konfiguration für diese Chat-Sitzung
  config: {
    temperature: 0.8,
    maxOutputTokens: 500,
  },
  // Optionaler Startverlauf
  history: [
    { role: 'user', parts: [{ text: 'Hallo KI.' }] },
    { role: 'model', parts: [{ text: 'Hallo! Wie kann ich dir helfen?' }] }
  ]
});
```

**2. Nachrichten senden (`chat.sendMessage` oder `chat.sendMessageStream`)**
```javascript
async function chatWithAI() {
  const chat = ai.chats.create({ model: 'gemini-1.5-flash-latest' });

  // Nicht-Streaming
  let response = await chat.sendMessage({ message: 'Was ist die Hauptstadt von Frankreich?' });
  console.log('AI:', response.text);

  // Streaming
  const streamResponse = await chat.sendMessageStream({ message: 'Erzähle mir mehr darüber.' });
  process.stdout.write('AI (streaming): ');
  for await (const chunk of streamResponse) {
    if (chunk.text) process.stdout.write(chunk.text);
  }
  console.log();

  // Verlauf abrufen
  const history = chat.getHistory(); // curated: false (default) für kompletten Verlauf
  // const curatedHistory = chat.getHistory(true); // für bereinigten Verlauf
  console.log('\nChat Verlauf:', history);
}
```

### Embeddings (`ai.models.embedContent`)

Text in Vektorrepräsentationen umwandeln.

```javascript
async function getEmbeddings() {
  const response = await ai.models.embedContent({
    model: 'text-embedding-004', // Oder ein anderes Embedding-Modell
    contents: [
      { role: 'user', parts: [{ text: 'Was ist dein Name?' }] },
      { role: 'user', parts: [{ text: 'Was ist deine Lieblingsfarbe?' }] }
    ],
    // config: { outputDimensionality: 64 } // Optional
  });
  console.log(response.embeddings); // Array von ContentEmbedding Objekten
}
```

### Token zählen (`ai.models.countTokens`)

```javascript
async function countMyTokens() {
  const response = await ai.models.countTokens({
    model: 'gemini-1.5-flash-latest',
    contents: [{ role: 'user', parts: [{ text: 'Der schnelle braune Fuchs springt über den faulen Hund.' }] }],
  });
  console.log('Total Tokens:', response.totalTokens);
}
```

### Bilder generieren (`ai.models.generateImages`)

(Benötigt ein Bildgenerierungsmodell wie `imagen-3.0-generate-002` - primär über Vertex AI)

```javascript
async function generateImage() {
  // Dieses Beispiel funktioniert typischerweise besser mit Vertex AI Konfiguration
  // const vertexAi = new GoogleGenAI({ vertexai: true, project: '...', location: '...' });
  try {
    const response = await ai.models.generateImages({ // oder vertexAi.models.generateImages
      model: 'imagen-3.0-generate-002', // Beispielmodell
      prompt: 'Ein Roboter, der ein rotes Skateboard hält',
      config: {
        numberOfImages: 1,
        // includeRaiReason: true, // Für Responsible AI Infos
      },
    });
    // response.generatedImages[0].image.imageBytes (Base64-codierte Bilddaten)
    console.log('Bild generiert (Details in response Objekt).');
  } catch (e) {
    console.error("Fehler bei der Bildgenerierung:", e);
  }
}
```

### Dateien verwalten (`ai.files`)

(Primär für Gemini Developer API)

```javascript
// Beispiel: Datei hochladen (Node.js Pfad oder Blob)
async function uploadMyFile() {
  try {
    const file = await ai.files.upload({
      file: 'pfad/zu/deiner/datei.txt', // oder ein File/Blob Objekt im Browser
      config: {
        mimeType: 'text/plain', // Wird oft automatisch erkannt
        displayName: 'Meine Testdatei'
      }
    });
    console.log('Datei hochgeladen:', file.name, file.uri); // file.name ist die Referenz für Prompts

    // Datei in einem Prompt verwenden (Beispiel, braucht Anpassung im `contents` Format)
    // const promptWithFile = `Analysiere diese Datei: ${file.uri}`;

    // Dateiinformationen abrufen
    const fileInfo = await ai.files.get({ name: file.name });
    console.log('Dateiinfo:', fileInfo);

    // Dateien auflisten
    const fileListPager = await ai.files.list({ config: { pageSize: 10 } });
    for await (const listedFile of fileListPager) {
      console.log('Gelistete Datei:', listedFile.name);
    }

    // Datei löschen
    // await ai.files.delete({ name: file.name });
    // console.log('Datei gelöscht.');

  } catch (e) {
    console.error("Fehler bei Dateioperation:", e);
  }
}
```
> **Hinweis:** `ai.files.download()` ist nur für Node.js verfügbar.

### Caching (`ai.caches`)

Reduziert Kosten durch Caching von Prompt-Präfixen (für spezifische Modelle).

```javascript
async function manageCache() {
  // Erfordert ein Modell, das Caching unterstützt
  const modelName = 'gemini-1.5-flash-latest'; // Überprüfen, ob das Modell Caching unterstützt

  try {
    const contentsToCache = [ /* ... Große Content-Objekte ... */ ];
    const cacheResponse = await ai.caches.create({
      model: modelName,
      config: {
        contents: contentsToCache,
        displayName: 'Mein Test-Cache',
        // systemInstruction: '...', // Optional
        ttl: '86400s', // Time To Live (z.B. 1 Tag)
      }
    });
    console.log('Cache erstellt:', cacheResponse.name);

    // Cache abrufen, auflisten, aktualisieren, löschen mit:
    // ai.caches.get({ name: cacheResponse.name });
    // ai.caches.list();
    // ai.caches.update({ name: cacheResponse.name, config: { ttl: '...' } });
    // ai.caches.delete({ name: cacheResponse.name });
  } catch (e) {
    console.error("Cache-Fehler:", e.message);
  }
}
```

### Pagers

Viele `list*` Methoden (z.B. `ai.files.list()`, `ai.caches.list()`) geben ein `Pager`-Objekt zurück.

```javascript
async function iterateWithPager() {
  const filesPager = await ai.files.list({ config: { pageSize: 5 } }); // Max 5 pro Seite

  // Alle Elemente über alle Seiten hinweg iterieren
  console.log("Alle Dateien:");
  for await (const file of filesPager) {
    console.log(file.name);
  }

  // Manuelle Paginierung (Beispiel)
  const modelsPager = await ai.models.list(); // Standard Seitengröße
  let currentPageItems = modelsPager.page;
  console.log("\nModelle (Seite 1):", currentPageItems.map(m => m.name));

  if (modelsPager.hasNextPage()) {
    currentPageItems = await modelsPager.nextPage();
    console.log("Modelle (Seite 2):", currentPageItems.map(m => m.name));
  }
}
```
**Pager Eigenschaften/Methoden:**
*   `pager.page`: `T[]` - Aktuelle Seite mit Elementen.
*   `pager.hasNextPage(): boolean` - Gibt es weitere Seiten?
*   `pager.nextPage(): Promise<T[]>` - Lädt die nächste Seite.
*   `pager[Symbol.asyncIterator]()`: Ermöglicht `for await...of`.

## Unterschiede zu anderen Google AI SDKs

*   `@google/genai`: "Vanilla" SDK von Google DeepMind für generative KI. Hier werden neue KI-Features zuerst hinzugefügt. Unterstützt Modelle auf Vertex AI und der Gemini Developer Plattform.
*   `@google/generative_language` & `@google-cloud/vertexai`: Frühere Versionen, erhalten keine neuen Gemini 2.0+ Features mehr.

## Typen (`@google/genai/types`)

Das Modul `types` enthält alle relevanten Enums, Interfaces und Typ-Aliase (z.B. `Content`, `Part`, `FunctionDeclaration`, `SafetySetting` etc.), die für die Arbeit mit dem SDK benötigt werden. Diese werden oft direkt importiert, z.B.:
`import { HarmCategory, HarmBlockThreshold } from '@google/genai';`

---

Dieses Cheatsheet deckt die wichtigsten Aspekte ab. Für detaillierte Informationen und alle Optionen immer die [offizielle Dokumentation](https://googleapis.github.io/js-genai/) konsultieren.
   
</details>


















<br><br>
<br><br>

<details><summary>Click to expand..</summary>



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

  
</details>
