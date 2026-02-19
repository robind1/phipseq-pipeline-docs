# FHIR Standards

## Profiles Used

| Resource | Profile URL |
| :--- | :--- |
| **Patient** | `https://fhir.kemkes.go.id/r4/StructureDefinition/Patient` |
| **Specimen** | `https://fhir.kemkes.go.id/r4/StructureDefinition/Specimen` |
| **Organization** | `https://fhir.kemkes.go.id/r4/StructureDefinition/Organization` |
| **Practitioner** | `https://fhir.kemkes.go.id/r4/StructureDefinition/Practitioner` |
| **PractitionerRole** | `https://fhir.kemkes.go.id/r4/StructureDefinition/PractitionerRole` |
| **Observation** | `http://hl7.org/fhir/StructureDefinition/Observation` |
| **Bundle** | `https://fhir.kemkes.go.id/r4/StructureDefinition/Bundle` |

### Observation (PhIP-Seq Epitope Binding)
Each enriched peptide generates an Observation resource:

| Field | Value |
| :--- | :--- |
| **Status** | `final` |
| **Category** | `laboratory` (HL7 observation-category) |
| **Code** | LOINC `94531-1` — Epitope binding signal intensity |
| **Value** | Z-score as `valueQuantity` (unit: `z-score`) |
| **Method** | SNOMED CT `708049000` — Phage immunoprecipitation sequencing |
| **Tag** | `phipseq` (system: `http://terminology.kemkes.go.id/sp`) |

### Observation Interpretation

| Z-score | Interpretation Code | Display |
| :--- | :--- | :--- |
| > 3.5 | `POS` | Positive — Strong viral epitope binding signal detected |
| ≤ 3.5 | `NEG` | Negative — No significant viral epitope binding |

The interpretation threshold of 3.5 matches the default `zscore_threshold` parameter.

## Standard Terminologies

### LOINC Codes

| Code | Display Name | Usage |
| :--- | :--- | :--- |
| **94531-1** | Epitope binding signal intensity | Observation code |

### SNOMED CT Codes

| Code | Display Name | Usage |
| :--- | :--- | :--- |
| **708049000** | Phage immunoprecipitation sequencing | Observation method |
| **119364003** | Serum specimen | Specimen type |
| **33747003** | Blood specimen collection | Specimen collection method |

## Example: Observation Resource

```json
{
  "resourceType": "Observation",
  "id": "SampleA-obs-42",
  "meta": {
    "profile": ["http://hl7.org/fhir/StructureDefinition/Observation"],
    "tag": [
      {
        "system": "http://terminology.kemkes.go.id/sp",
        "code": "phipseq",
        "display": "PhIP-Seq"
      }
    ]
  },
  "status": "final",
  "category": [
    {
      "coding": [
        {
          "system": "http://terminology.hl7.org/CodeSystem/observation-category",
          "code": "laboratory",
          "display": "Laboratory"
        }
      ]
    }
  ],
  "code": {
    "coding": [
      {
        "system": "http://loinc.org",
        "code": "94531-1",
        "display": "Epitope binding signal intensity"
      }
    ],
    "text": "PhIP-Seq Viral Epitope Binding Signal"
  },
  "subject": {
    "reference": "Patient/SampleA-patient"
  },
  "specimen": {
    "reference": "Specimen/SampleA-specimen"
  },
  "valueQuantity": {
    "value": 5.234,
    "unit": "z-score",
    "system": "http://unitsofmeasure.org",
    "code": "{score}"
  },
  "interpretation": [
    {
      "coding": [
        {
          "system": "http://terminology.hl7.org/CodeSystem/v3-ObservationInterpretation",
          "code": "POS",
          "display": "Positive"
        }
      ]
    }
  ],
  "method": {
    "coding": [
      {
        "system": "http://snomed.info/sct",
        "code": "708049000",
        "display": "Phage immunoprecipitation sequencing"
      }
    ],
    "text": "PhIP-Seq using Vir3 Library"
  },
  "note": [
    {"text": "Strong viral epitope binding signal detected"},
    {"text": "Peptide ID: 42, Z-score: 5.234"}
  ]
}
```