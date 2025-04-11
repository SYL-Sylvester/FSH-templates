# Valueset Template

// // ValueSet with codes from a system or ValueSet

    ValueSet: MyValueSet
    Id: my-valuset
    Title: "Simple valueset"
    Description: Template for valuesets
    * include codes from system <CodeSystemURL or Name>
    // Example: Include all codes defined in MyLocalCodeSystem
    * include codes from system MyLocalCodeSystem
    //
    * include codes from valueset <ValueSetURL or Name>
    // Example: Include all codes from the standard administrative gender ValueSet
    * include codes from valueset http://hl7.org/fhir/ValueSet/administrative-gender
    
### Example 1

// ValueSet with codes from multiple systems

    ValueSet: AllDiagnosisVS
    Id: all-diagnosis-vs
    Title: "All Diagnosis Codes"
    Description: "A comprehensive list of diagnosis codes."
    * include codes from system http://snomed.info/snomedct where concept is-a 64572001 // Disease
    * include codes from system http://hl7.org/fhir/sid/icd-10
    // Potentially include other relevant code systems


    Profile: ConditionWithAllDiagnosis
    Parent: Condition // Based on the Condition resource
    Id: condition-with-all-diagnosis
    Title: "Condition Profile using All Diagnosis Value Set"
    Description: "An example profile for Condition that binds the main 'code' element to the AllDiagnosisVS ValueSet."
    * code MS
    * code from AllDiagnosisVS (extensible)
    * subject MS
    * clinicalStatus MS
    * verificationStatus MS 
    

### Example 2

// ValueSet with codes from system and list
    
    ValueSet: LimitedMaritalStatusVS
    Id: limited-marital-status-vs
    Title: "Limited Marital Status Value Set"
    Description: "A specific subset of common marital status codes from HL7 v3 MaritalStatus."
    Status: active // Assuming this set is ready for use
    
    // Include specific listed codes from HL7 v3 MaritalStatus CodeSystem
    // Syntax: * <SystemURL>#<code> "<Display Text>"
    * http://terminology.hl7.org/CodeSystem/v3-MaritalStatus#S "Never Married"
    * http://terminology.hl7.org/CodeSystem/v3-MaritalStatus#M "Married"
    * http://terminology.hl7.org/CodeSystem/v3-MaritalStatus#W "Widowed"
    * http://terminology.hl7.org/CodeSystem/v3-MaritalStatus#D "Divorced"
    * http://terminology.hl7.org/CodeSystem/v3-MaritalStatus#L "Legally Separated"
    
    // Note: An alternative syntax uses 'include codes from system <SystemURL>#<code> codes #code1, #code2'.
    // The syntax above is often preferred as it allows specifying the display text directly.

    Profile: PatientWithLimitedMaritalStatus
    Parent: Patient
    Id: patient-limited-marital-status
    Title: "Patient Profile with Limited Marital Status"
    Description: "A profile that requires the Patient's maritalStatus field to use codes strictly from the LimitedMaritalStatusVS."
    Status: draft
    
    // Bind the maritalStatus field (type CodeableConcept) to our ValueSet.
    // Use 'required' binding strength to enforce that ONLY codes from the
    // ValueSet are permitted. Mark the element as Must Support.
    * maritalStatus from LimitedMaritalStatusVS (required)
    * maritalStatus MS
    
    // Add other constraints if needed
    * name MS
    * identifier MS


### Example 3

// Exclusion rules remove codes from the set defined by the include rules. They use the same syntax patterns as include.
Syntax:

    * exclude codes from system <SystemURL or Name> codes #code1, #code2
    * exclude codes from system <SystemURL or Name> where <filter>
    * exclude codes from valueset <ValueSetURL or Name>

// ValueSet using exclude

    ValueSet: UpperLimbExcludingHandVS
    Id: upper-limb-excluding-hand-vs
    Title: "Upper Limb Structure Excluding Hand Value Set"
    Description: "Includes SNOMED CT concepts for structures of the upper limb, but specifically excludes the 'Structure of hand' hierarchy."
    Status: active
    
    // 1. Include 'Upper limb structure' and all its descendants from SNOMED CT
    * include codes from system http://snomed.info/sct where concept is-a #128119006 // Upper limb structure
    
    // 2. Exclude 'Structure of hand' and all its descendants from the set defined above
    * exclude codes from system http://snomed.info/sct where concept is-a #85517003 // Structure of hand

    Profile: BodyStructureUpperLimbNoHand
    Parent: BodyStructure
    Id: bodystructure-upper-limb-no-hand
    Title: "BodyStructure Profile for Upper Limb (Excluding Hand)"
    Description: "A profile for BodyStructure resources where the location must be part of the upper limb but not the hand."
    Status: draft
    
    // Bind the location field (CodeableConcept) to our ValueSet
    // Use 'required' strength to enforce codes only from the VS.
    * location from UpperLimbExcludingHandVS (required)
    * location MS
    
    // Other example constraints
    * description MS
    * patient 1..1 MS


## Most Commonly used Valuesets for OH:
