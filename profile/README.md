# Mission Statement

**To establish a vendor-agnostic, interoperable standard for integrating AI-driven Medical Scribes with Electronic Medical Records (EMR) systems in India.** By standardizing the "handshake" and data payload, we aim to accelerate innovation, reduce integration friction, and treat clinical data connectivity as a Digital Public Good (DPG).

---

## 1. Strategic Objectives

The primary goal is to decouple the intelligence layer (Scribe) from the system of record (EMR), ensuring seamless data fluidity.

- **Standardized Interoperability**: Eliminate the need for custom, point-to-point contracts between every scribe vendor and EMR provider. A "write once, deploy everywhere" approach.
- **Vendor Agnosticism & No Lock-in**: Empower healthcare facilities to switch scribe vendors based on performance and cost without incurring prohibitive switching costs or technical debt.
- **Reusability**: Ensure that the effort spent mapping scribe outputs to clinical formats is a one-time investment, reusable across different EMR implementations.
- **Predictable Evaluation**: Create a deterministic output standard that allows for the rigorous, apples-to-apples evaluation of different medical scribe platforms.

## 2. Scope & Core Assumptions

To ensure feasibility and immediate impact, this framework operates under the following premises:

### Focus on Discrete Clinical Data
While scribes generate narrative text, the highest value for downstream tasks (Clinical Decision Support, Analytics, Research) lies in structured, discrete entities (e.g., Medications, Vitals, Diagnosis). This framework prioritizes the extraction and standardization of these entities.

### Feasibility of Standardization
We assume that core medical concepts can be mapped to standard ontologies (SNOMED CT, LOINC, ICD10/11, NRCES drug codes) and structural formats without loss of critical clinical context.

### Pareto Principle
Solving for discrete scribe output addresses the vast majority of high-value EMR use cases, including automated order generation and longitudinal health studies.

## 3. Technical Components

The framework is composed of three distinct layers designed to handle the transport, structure, and translation of clinical data.

### A. The Data Exchange Protocol (The "Handshake")
A high-level specification akin to the Model Context Protocol (MCP) or LSP, defining how systems discover and talk to each other.

- **Capability Discovery**: A standard mechanism for the EMR to query the Scribe: "What templates do you offer?" Basis the response, the EMR can select a template to be used during the structuring phase.
- **Multi-Modal Transport**:
    - **Bi-Directional Flow**: Support for pushing context to the scribe (e.g., "Patient is male, 40yo") and pulling results.
- **Interaction Models**:
    - **Synchronous/API**: REST/gRPC for real-time transactional updates.
    - **Asynchronous**: Webhooks or long-polling for batch processing.
- **Artifact Exchange**: Secure handling of large payloads (audio files, raw transcripts) via signed URLs (e.g., S3 pre-signed links).

### B. The FHIR Profiles (The "Payload")
A strict "Profile" of the HL7 FHIR standard to ensure deterministic data exchange.

- **The "Problem of Plenty" Solution**: FHIR is flexible; this framework effectively "constrains" FHIR to remove ambiguity. We will define strict Implementation Guides (IGs).
- **Mandatory vs. Optional**: Explicit definitions of required fields to prevent "data sparseness."
- **Semantic Objectification (Examples)**:
    - **Medication**: A defined subset of the MedicationRequest resource that captures dosage, frequency, and route uniquely, minimizing subjective interpretation.
    - **Observations**: Strict mapping rules (e.g., "Symptom" $\rightarrow$ Observation resource; "Medical History" $\rightarrow$ Condition resource with category='problem-list').

### C. The Integration Adapter (The "Bridge")
A translation layer ensuring the Scribe's standard output matches the EMR's specific input.

- **Role**: The Adapter consumes the standardized FHIR JSON output from the Scribe and converts it into the proprietary or legacy format required by the specific EMR instance.
- **Decoupling**: This ensures that Scribe vendors do not need to build 100 different integrations, and EMRs do not need to build 100 different parsers.

## 4. Utilities & Ecosystem Enablers

To accelerate adoption, the consortium will develop and maintain the following assets:

### 1. SCRIBE-EMR-PROTOCOL (SEP)
- **Description**: An open standard defining the handshake, authentication, and data exchange between EMRs and AI Scribes.
- **Function**: Eliminates custom integrations by standardizing how EMRs discover capabilities and submit audio for transcription.
- **Example**: POST /initialize $\rightarrow$ negotiates supported templates and protocol version.
- **Repository**: https://github.com/MedScribeAlliance/scribe-emr-protocol

### 2. The Open SCRIBE2FHIR SDK
- **Description**: A multi-language SDK (Python, TypeScript/Node, Go, Java) designed for scribe vendors.
- **Function**: Abstract complexity by providing simple methods to generate compliant FHIR resources from raw model outputs.
- **Example**: `sdk.create_symptom("Fever", duration="2 days")` $\rightarrow$ returns validated FHIR JSON.
- **Repository**: https://github.com/MedScribeAlliance/scribe2fhir

### 3. Public Adapter Library (DPGs)
- **Vision**: Adapters for major/common facility standards will be released as Digital Public Goods (DPGs).
- **Impact**: If a facility uses a common schema, the adapter is free and open-source, lowering the barrier to entry for smaller clinics and hospitals.
- **Repository**: https://github.com/MedScribeAlliance/fhir2emr-adapters

### 4. Canonical Documentation
- **Artifacts**: A central documentation hub (GitHub Pages) containing:
    - The API Specification (OpenAPI/Swagger).
    - The FHIR Implementation Guide (IG).
    - Reference Architectures and "Hello World" integration examples.

## 5. Call to Action

We are gathering a consortium of data scientists, engineers, and clinicians to define the future of medical AI integration in India.

### How to Contribute
- **Review the Specs**: Help us refine the FHIR profiles to ensure they cover Indian healthcare nuances.
- **Build Adapters**: Contribute to the open-source library of EMR adapters.
- **Test & Validate**: Run the protocol against your existing scribe/EMR setups.

> This framework is a living document hosted on GitHub. Pull Requests are welcome.
