# This is an example Condition profile

## An example with supporting artifacts and profiles for references

### // Condition Profile Definition

    Profile: EncounterDiagnosisConditionProfile
    Parent: Condition
    Id: encounter-diagnosis-condition-profile
    Title: "Encounter Diagnosis Condition Profile"
    Description: "Profile for Condition resources that represent diagnoses identified and recorded during a specific patient encounter."
    Status: draft
    
    // --- Must Support Flags ---
    // Key elements implementers must support for this profile
    * clinicalStatus MS
    * verificationStatus MS
    * category MS
    * code MS // The diagnosis code itself
    * subject MS // The patient
    * encounter MS // The link to the encounter
    * onset[x] MS // When the condition began (approximate or specific)
    * recordedDate MS // When the condition was recorded
    
    // --- Cardinality / Existence Constraints ---
    // These elements must be present
    * code 1..1 // A diagnosis code is mandatory
    * subject 1..1 // Must link to a patient
    * encounter 1..1 // Must link to the specific encounter
    * recordedDate 1..1 // Must specify when it was recorded
    
    // --- Fixed Values / ValueSet Bindings ---
    // Bind clinicalStatus, using required strength
    * clinicalStatus from http://hl7.org/fhir/ValueSet/condition-clinical (required)
    // Example: Could fix clinicalStatus to 'active' if only active diagnoses allowed
    // * clinicalStatus = http://terminology.hl7.org/CodeSystem/condition-clinical#active
    
    // Bind verificationStatus, using required strength
    * verificationStatus from http://hl7.org/fhir/ValueSet/condition-ver-status (required)
    // Example: Could limit verificationStatus via a specific VS if needed
    
    // Fix the category to 'encounter-diagnosis' using the standard code
    * category = http://terminology.hl7.org/CodeSystem/condition-category#encounter-diagnosis
    
    // Bind the diagnosis code (Condition.code) to our specific diagnosis ValueSet
    * code from ProblemDiagnosisVS (required) // Enforce codes only from ProblemDiagnosisVS
    
    // --- Type Constraints & Reference Profiling ---
    // Ensure references point to appropriately profiled resources
    * subject only Reference(MyPatientProfile) // Subject must conform to MyPatientProfile
    * encounter only Reference(MyEncounterProfile) // Encounter must conform to MyEncounterProfile
    
    // --- Extension Usage ---
    // Allow the optional Diagnosis Rank extension, mark as Must Support
    // *** Replace URL with your actual canonical URL for the extension ***
    * extension contains http://example-clinic.com/fhir/StructureDefinition/diagnosis-rank 0..1 MS


### // Supporting Artifact: Placeholder ValueSet for Diagnoses

    ValueSet: ProblemDiagnosisVS
    Id: problem-diagnosis-vs
    Title: "Problem/Diagnosis Value Set (Placeholder)"
    Description: "Placeholder ValueSet: Defines the set of codes acceptable for representing problems or diagnoses. Typically includes SNOMED CT clinical findings or ICD codes appropriate for the implementation context."
    Status: draft
    // Example: Include SNOMED CT clinical findings hierarchy
    * include codes from system http://snomed.info/sct where concept is-a #404684003 // Clinical finding (finding)
   

### // Supporting Artifact: Extension for Diagnosis Rank

    Extension: DiagnosisRankExtension
    Id: diagnosis-rank
    Title: "Diagnosis Rank"
    Description: "Indicates the rank or priority of this diagnosis relative to others recorded for the same encounter or context (e.g., 1 = Primary Diagnosis, 2 = Secondary, etc.)."
    Status: draft
    // Applicable to the Condition resource root
    Context: Condition
    // The value is a positive integer representing the rank
    * value[x] only positiveInt
    // The rank value is required if the extension is used
    * valuePositiveInt 1..1
    
    

### // Supporting Artifacts: Placeholder Profiles for References

    Profile: MyPatientProfile
    Parent: Patient
    Id: my-patient-profile
    Title: "My Patient Profile (Placeholder)"
    Description: "Placeholder profile for Patient resources."
    Status: draft
    * identifier MS // Example constraint
    
    Profile: MyEncounterProfile
    Parent: Encounter
    Id: my-encounter-profile
    Title: "My Encounter Profile (Placeholder)"
    Description: "Placeholder profile for Encounter resources."
    Status: draft
    * status MS // Example constraint
    * class MS
    * period MS
