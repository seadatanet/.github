# SeaDataNet Software Ecosystem

> **Status snapshot: October 2026**  
> This page documents the SeaDataNet software ecosystem used to **prepare, validate, harmonise and replicate marine data and metadata**. It deliberately stops at the central **Import Manager / EUDAT** layer and does not describe the CDI discovery portal itself.

The ecosystem is evolving from a set of historically private Java applications towards a more open, FAIR and maintainable software stack. The first diagram gives a deliberately simplified functional overview; the following sections add publication status, technical dependencies and modernisation details.

## At a glance

```mermaid
flowchart LR

  VOC["Controlled vocabularies & directories<br/>Shared semantic references<br/>BODC · MARIS · ICES"]

  NEMO["NEMO<br/>Harmonise & convert data"]
  OCT["OCTOPUS<br/>Check & convert SDN formats"]
  EB["EndsAndBends (E&B)<br/>Simplify navigation tracks"]
  MIK["MIKADO<br/>Generate XML metadata"]
  RM["Replication Manager<br/>Validate, link & replicate"]
  IM["Import Manager<br/>Central metadata ingestion"]
  EUDAT["EUDAT<br/>Replicate unrestricted data"]

  NEMO -->|"CDI summary"| MIK
  EB -->|"spatial geometry"| MIK

  NEMO -->|"standard data files"| RM
  MIK -->|"CDI XML + coupling table"| RM

  NEMO -.->|"uses"| OCT
  RM -.->|"uses"| OCT

  RM -->|"metadata"| IM
  RM -->|"unrestricted data"| EUDAT

  VOC -.->|"local synchronisation"| NEMO
  VOC -.-> OCT
  VOC -.-> MIK
  VOC -.-> RM
```

This overview is intentionally functional: it shows **what each component does and how the main data/metadata flows connect**. Openness, repository visibility, FAIRisation and modernisation status are shown in the detailed ecosystem view below.

## Software ecosystem

```mermaid
flowchart TB

  subgraph SEM["Controlled vocabularies & directories"]
    direction LR
    ICES["ICES<br/>C17 source"]
    BODC["BODC / NVS<br/>C*, L*, P* · EDMED · EDIOS · C17"]
    MARIS["MARIS<br/>EDMO · EDMERP"]
    SYNC["Web-service synchronisation<br/>local copies in SeaDataNet software"]

    ICES -->|"C17 synchronisation"| BODC
    BODC --> SYNC
    MARIS --> SYNC
  end

  subgraph NODC["NODC software environment"]
    direction LR
    EB["EndsAndBends (E&B)<br/>2.2.0"]
    NEMO["NEMO<br/>2.1.1 → 2.2.0"]
    MIKADO["MIKADO<br/>3.8.4 / 3.8.2"]
    RM["Replication Manager (RM)<br/>1.2.0"]
    OCTOPUS["OCTOPUS<br/>1.12.0"]
  end

  subgraph CENTRAL["Central services"]
    direction LR
    IM["Import Manager<br/>operated by MARIS"]
    EUDAT["EUDAT<br/>data replication"]
  end

  EB -->|"simplified spatial geometry / GML"| MIKADO
  NEMO -->|"CDI summary"| MIKADO
  NEMO -->|"SeaDataNet data files"| RM
  MIKADO -->|"CDI XML + coupling table"| RM

  NEMO -.->|"software dependency"| OCTOPUS
  RM -.->|"software dependency"| OCTOPUS

  RM -->|"CDI XML + coupling table"| IM
  RM -->|"unrestricted data"| EUDAT

  SYNC -.->|"direct WS sync"| OCTOPUS
  SYNC -.->|"direct WS sync"| NEMO
  SYNC -.->|"direct WS sync"| MIKADO
  SYNC -.->|"direct WS sync"| RM

  classDef public fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#111;
  classDef transition fill:#fff3e0,stroke:#2e7d32,stroke-width:2px,stroke-dasharray:6 4,color:#111;
  classDef external fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,color:#111;
  classDef semantic fill:#f3e5f5,stroke:#6a1b9a,stroke-width:1.5px,color:#111;

  class OCTOPUS public;
  class NEMO,MIKADO,RM,EB transition;
  class IM,EUDAT external;
  class ICES,BODC,MARIS,SYNC semantic;
```

### Legend

| Visual status | Meaning |
|---|---|
| **Green** | Public/open-source and FAIRised |
| **Orange + dashed green border** | Private today, with public release / FAIRisation planned or in progress |
| **Blue** | External or centrally operated service |
| **Purple** | Shared semantic reference layer |

> **Restricted data are not replicated to EUDAT.** They remain at the originating NODC. Their CDI metadata are still transferred through the Replication Manager workflow.

## Core software

| Logo | Software | Maintainer / operator | Current version | Java / runtime | Current licence | FAIR / publication status | Private repository | Public repository |
|---|---|---|---|---|---|---|---|---|
| <img src="assets/logos/octopus.png" alt="OCTOPUS" height="68"> | **OCTOPUS** | Ifremer | **1.12.0** | OpenJDK 11+ | **LGPL v3** | **FAIRised** and public | [Ifremer GitLab](https://gitlab.ifremer.fr/seadatanet/applications/octopus) | [github.com/seadatanet/octopus](https://github.com/seadatanet/octopus) |
| <img src="assets/logos/nemo.jpg" alt="NEMO" height="68"> | **NEMO** | Ifremer | **2.1.1** → **2.2.0 upcoming** | JDK 8 → OpenJDK 11+ | SeaDataNet 1.0 → **LGPL v3** | FAIRisation + public release planned with 2.2.0 | [Ifremer GitLab](https://gitlab.ifremer.fr/seadatanet/applications/nemo) | [github.com/seadatanet/nemo](https://github.com/seadatanet/nemo) *(repository created; code publication planned)* |
| <img src="assets/logos/mikado.jpg" alt="MIKADO" height="68"> | **MIKADO** | Ifremer | **3.8.4** / **3.8.2** | Java 21 / Jakarta; legacy JDK 8 line | SeaDataNet 1.0 → **LGPL v3** | FAIRisation and public release planned | [Ifremer GitLab](https://gitlab.ifremer.fr/seadatanet/applications/mikado-software) | <https://github.com/seadatanet/mikado> *(planned)* |
| <img src="assets/logos/rm.png" alt="Replication Manager" height="68"> | **Replication Manager (RM)** | Ifremer | **1.2.0** | JDK 8; Tomcat < 10 | SeaDataNet 1.0 → **LGPL v3** | FAIRisation and public release planned | [Ifremer GitLab](https://gitlab.ifremer.fr/seadatanet/applications/ReplicationManager) | <https://github.com/seadatanet/rm> *(planned)* |
| <img src="assets/logos/endsandbends.jpg" alt="EndsAndBends" height="60"> | **EndsAndBends (E&B)** | Ifremer | **2.2.0** | Java 21; OpenJDK / Azul Zulu build | SeaDataNet 1.0 → **LGPL v3** | FAIRisation and public release planned in the near term | **TBD – Ifremer GitLab project URL** | <https://github.com/seadatanet/endsandbends> *(planned publication)* |
| <img src="assets/logos/import-manager.png" alt="Import Manager" height="60"> | **Import Manager** | MARIS | **TBD** | Central web application | **TBD** | Externally operated; source status to document | — | — |

### Main roles

- **OCTOPUS** — multiformat checker, splitter and converter for SeaDataNet formats. It validates and converts MedAtlas, ODV and NetCDF/CF data and provides format-processing capabilities used by both NEMO and Replication Manager.
- **NEMO** — harmonises heterogeneous marine data into SeaDataNet exchange formats (ODV, MedAtlas and NetCDF/CF) and can produce a CDI summary used by MIKADO.
- **MIKADO** — prepares and manages SeaDataNet XML metadata for EDMED, CSR, EDMERP, CDI and EDIOS. It can work manually or generate metadata from local databases / CSV files. It also generates the coupling table used by Replication Manager.
- **Replication Manager** — one instance is typically deployed by each NODC. It synchronises data and CDI metadata with the central SeaDataNet infrastructure, performs consistency/format checks, handles data-access workflows, transfers CDI XML and the coupling table to Import Manager, and replicates unrestricted data to EUDAT.
- **EndsAndBends (E&B)** — simplifies raw vessel navigation tracks while preserving their geometry, producing spatial objects suitable for CDI/CSR records and GIS use. Its GML output can be injected into MIKADO files.
- **Import Manager** — MARIS-operated central web component receiving CDI metadata and coupling information from NODC Replication Manager instances and supporting ingestion/administration of the central workflow.

## Technical dependencies

The functional diagram above intentionally hides most implementation details. The following view exposes the main **confirmed software dependencies** that are useful for understanding the SeaDataNet client stack, while omitting purely internal Ifremer utility libraries.

```mermaid
flowchart TD

  RM["Replication Manager"] --> RMD["rm-data"]
  RMD --> RMC["rm-common"]
  RMC --> OCT["OCTOPUS"]

  NEMO["NEMO"] --> OCT

  OCT --> MED["medatlasreader"]
  MED --> O2C["OdvSDN2CFPointLib"]
  O2C --> CFP["cfpointlib"]

  OCT --> MGD["mgd"]
  OCT --> SVA["SoftwareVersionApi"]

  OCT -.->|"via internal Ifremer adapters (omitted)"| EDMO["EDMO services"]
  OCT -.->|"via internal Ifremer adapters (omitted)"| VOC["SeaDataNet controlled vocabularies"]

  classDef app fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,color:#111;
  classDef component fill:#f5f5f5,stroke:#616161,stroke-width:1.5px,color:#111;
  classDef shared fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#111;
  classDef service fill:#f3e5f5,stroke:#6a1b9a,stroke-width:1.5px,color:#111;

  class RM,NEMO app;
  class OCT shared;
  class RMD,RMC,MED,O2C,CFP,MGD,SVA component;
  class EDMO,VOC service;
```

This dependency view is deliberately **not an exhaustive library inventory**. In particular, internal Ifremer utility libraries are not shown when they do not help an external SeaDataNet developer understand the public architecture. Detailed versioning, licensing and lifecycle information for `rm-data`, `rm-common` and the OCTOPUS support libraries can be documented later as the technical inventory is consolidated.

The confirmed RM dependency chain is therefore:

```text
Replication Manager → rm-data → rm-common → OCTOPUS
```

OCTOPUS is also a direct dependency of NEMO and provides shared SeaDataNet format checking and conversion capabilities.

## Modernisation trajectory

```mermaid
flowchart LR

  OCT_C["OCTOPUS 1.12.0<br/>OpenJDK 11+<br/>LGPL v3<br/>public GitHub<br/>FAIRised"]

  NEMO_C["NEMO 2.1.1<br/>JDK 8<br/>SeaDataNet 1.0<br/>private"] --> NEMO_T["NEMO 2.2.0<br/>OpenJDK 11+<br/>LGPL v3<br/>public GitHub<br/>FAIRised"]

  EB_C["E&B 2.2.0<br/>Java 21<br/>SeaDataNet 1.0<br/>private"] --> EB_T["E&B target<br/>LGPL v3<br/>public GitHub<br/>FAIRised"]

  MIK_C["MIKADO<br/>3.8.2 · JDK 8<br/>3.8.4 · Java 21 / Jakarta<br/>SeaDataNet 1.0 · private"] --> MIK_T["MIKADO target<br/>LGPL v3<br/>public GitHub<br/>FAIRised"]

  RM_C["RM 1.2.0<br/>JDK 8<br/>Tomcat <10<br/>SeaDataNet 1.0 · private"] --> RM_T["RM target<br/>LGPL v3<br/>public GitHub<br/>FAIRised"]

  classDef achieved fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#111;
  classDef current fill:#fff3e0,stroke:#ef6c00,stroke-width:2px,color:#111;
  classDef target fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,stroke-dasharray:6 4,color:#111;

  class OCT_C achieved;
  class NEMO_C,EB_C,MIK_C,RM_C current;
  class NEMO_T,EB_T,MIK_T,RM_T target;
```

The trajectory combines several dimensions of technical-debt reduction:

- migration away from legacy JDK 8 components where a modern runtime is already defined;
- migration from the **SeaDataNet 1.0 licence** to **LGPL v3**;
- progressive publication of source code in the **SeaDataNet GitHub organisation**;
- FAIRisation of software metadata, documentation, licensing and citation information.

For RM, a future runtime upgrade may be desirable, but no target Java/Tomcat version is documented here yet.

## Controlled vocabularies and directories

SeaDataNet software relies on shared semantic resources that are synchronised locally through web services. These resources provide a common reference layer across data and metadata workflows.

| Logo | Organisation | Main SeaDataNet resources | Notes |
|---|---|---|---|
| <img src="assets/logos/bodc.jpg" alt="BODC" height="72"> | **BODC / NVS** | SeaDataNet controlled vocabularies (`C*`, `L*`, `P*`), EDMED, EDIOS, exposed C17 | Main vocabulary web-service provider used by SeaDataNet applications |
| <img src="assets/logos/maris.png" alt="MARIS" height="64"> | **MARIS** | EDMO, EDMERP | Directories exposed through MARIS web services |
| <img src="assets/logos/ices.png" alt="ICES" height="68"> | **ICES / CIEM** | C17 source | C17 is created/maintained by ICES, synchronised to BODC and exposed through the BODC service |

OCTOPUS, NEMO, MIKADO and Replication Manager each contact the relevant web services directly to maintain a local copy. The use of SeaDataNet vocabularies by EndsAndBends is still to be confirmed.

## Central services

| Logo | Service | Organisation / operator | Role |
|---|---|---|---|
| <img src="assets/logos/import-manager.png" alt="Import Manager" height="64"> | **Import Manager** | **MARIS** | Receives **CDI XML + coupling table** from Replication Manager instances. The coupling table links CDI metadata to the corresponding data files. |
| <img src="assets/logos/eudat.jpg" alt="EUDAT" height="72"> | **EUDAT** | **EUDAT** | Receives replicated **unrestricted data** from Replication Manager. Restricted data remain at the originating NODC. |

## Maintainer

Most client-side SeaDataNet software described here is developed and maintained by:

<img src="assets/logos/ifremer.svg" alt="Ifremer" height="42">

**Ifremer** — Institut français de recherche pour l'exploitation de la mer.

## Known documentation gaps

This first ecosystem view intentionally keeps a few items explicit rather than guessing them:

- exact private Ifremer GitLab URL for **EndsAndBends**;
- **Import Manager** current version, licence and source-code repository status;
- confirmation of whether **EndsAndBends** directly consumes SeaDataNet controlled vocabularies;
- any future Java/Tomcat target for **Replication Manager**.

These points can be completed as the software inventory is consolidated.
