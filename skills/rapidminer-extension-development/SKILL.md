---
name: rapidminer-extension-development
description: Comprehensive guide for creating, debugging, and building Altair AI Studio (RapidMiner) Java extensions. Includes knowledge on ExampleSets, Documents, Ports, Operator Registration, and XML Documentation properties.
---

# Altair AI Studio (RapidMiner) Extension Development

## 1. Project Setup and Build
- **Build Tool**: Gradle. Use `./gradlew clean installExtension` to compile and install the extension locally.
- **Java Version**: RapidMiner Studio version 10.1+ requires **Java 11**.
- **Installation Path**: Built extensions are automatically installed to `~/.AltairRapidMiner/AI Studio/shared/extensions/`.
- **Logs Location**: `~/.AltairRapidMiner/AI Studio/<version>/ai-studio.log` (e.g. `2026.0.5/ai-studio.log`). *Always check this file if the GUI doesn't load the operators or crashes on startup.*

## 2. Operator Structure (Java API)
- Classes implement RapidMiner's specific functionality by extending `Operator` (or `DocumentOperator`).
- Define input and output requirements explicitly using `InputPort` and `OutputPort`.
  ```java
  private final InputPort exampleSetInput = getInputPorts().createPort("example set", ExampleSet.class);
  private final OutputPort exampleSetOutput = getOutputPorts().createPort("example set");
  ```
- Override `doWork()` to process the incoming data:
  ```java
  ExampleSet exampleSet = exampleSetInput.getData(ExampleSet.class);
  // process the ExampleSet...
  ```

## 3. Creating Tabular Data (ExampleSets)
When fabricating tabular data from scratch (like parsing unknown JSON into columns):
1. Create columns (attributes) using `AttributeFactory`:
  ```java
  Attribute myAttr = AttributeFactory.createAttribute("MyColumn", Ontology.STRING);
  ```
2. Store attributes in an ordered `List<Attribute>`.
3. Create a `MemoryExampleTable` initialized with the attributes list.
4. Construct rows using a `DataRowFactory` (typically `DataRowFactory.TYPE_DOUBLE_ARRAY` mapped to an `Ontology` like `Ontology.STRING`).
5. Populate the `MemoryExampleTable` with `DataRow` elements iterating through your dataset.
6. Create the outputtable format from the populated table: `ExampleSet newSet = exampleTable.createExampleSet();`
7. Push it out the port: `exampleSetOutput.deliver(newSet);`

## 4. Processing Raw Documents
- To process raw text files directly (without the need for cumbersome ExampleSet iteration blocks in the UI), use an `InputPort` expecting `com.rapidminer.operator.text.Document`.
- You can read the entire document text simply: `String fullText = document.getTokenText();`

## 5. UI Documentation and XML Registries (i18n)
- **Groups / Folders**: Declared in `src/main/resources/.../Operators<Extension>.xml` and `groups<Extension>.properties`.
- **Help Documentation**: Kept in `src/main/resources/.../i18n/OperatorsDoc<Extension>.xml`.
  - Must use the strict format `<operatorHelp lang="en_EN"><operator><key>...</key><name>...</name><synopsis>...</synopsis><help>...</help></operator></operatorHelp>` deeply wrapped within `<rapidminerdoc>`.
  - **CRITICAL XML RULE**: Do NOT add the `p1:documents` namespace to the root of `OperatorsDoc<Extension>.xml`. Injecting unrecognized schema tags into the main registry explicitly crashes the core RapidMiner XML parser (`XMLTools.parse`) and prevents the entire extension from loading.
- **Icons**: Specified using `<icon>icon_name.png</icon>` tags wrapped directly inside the operator `<group>` elements in the configuration tree.

## 6. Tutorial Processes Binding
- **CRITICAL UI RULE**: Embedding `<tutorialProcesses>` recursively inside the main translation `OperatorsDoc` XML file can fail rendering entirely depending on RapidMiner builds.
- Instead, RapidMiner's XSLT renderer explicitly searches for process mappings separated into heavily isolated `.xml` files corresponding exactly to their `operator_key`.
- In those isolated XML mapping files, they strictly *require* the RapidMiner schema namespace exact wrapper (`<p1:documents ...>`) to trigger the "Jump to Tutorial Process" hyperlink generation inside the UI.

## 7. JSON Tools Memory
- `com.jayway.jsonpath:json-path` is excellent for extracting dynamic JSONPath queries. Follow RapidMiner dependency practices strictly by resolving conflicts appropriately during gradle implementation.
- Utilizing recursion with Jackson/Gson allows you to fully crawl deeply nested JSON structures, identify complex lists, and natively flatten them sideways using dynamic dot-notation string tracking (e.g. `address.city = 'Austin'`). 
