# Valueset Template

    ValueSet: MyValueSet
    Id: my-valuset
    Title: "Simple valueset
    Description: Template for valuesets
    * #example-code (REQUIRED) 
    * include codes from system 

    
### Example 1

    // ValueSet with codes from multiple systems

    ValueSet AllDiagnosisVS {
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
    
    ValueSet: ListEthnicityVS
    Id: list-ethnicity-vs
    Title: "Simple Ethnicity Value Set"
    Description: "A value set listing two ethnicities with a canonical URL."
    // codesystem
    * include codes from system http://terminology.hl7.org/CodeSystem/v3-Ethnicity
    // list
    * #hispanic "Hispanic" "Individuals who identify as Hispanic or Latino."
    * #native "Native American" "Individuals who identify as Native American or Alaska Native."

    Extension EthnicityExtension
    Id: ethnicity-extension
    Title: "Ethnicity Extension"
    Description: "An extension to capture patient ethnicity."
    Context: Patient
    Value[x]: CodeableConcept
    valueCodeableConcept from ListEthnicityVS (extensible)


    Instance: MyPatientInstance 
    InstanceOf: MyPatientProfile
    Id: patient-example
    extension[ethnicity][0].valueCodeableConcept.coding[0].system = "http://terminology.hl7.org/CodeSystem/v3-Ethnicity"
    extension[ethnicity][0].valueCodeableConcept.coding[0].code = "2162-6" // Hispanic or Latino

    extension[ethnicity][1].valueCodeableConcept.coding[0].system = "http://example.org/fhir/CodeSystem/list-ethnicity-vs"
    extension[ethnicity][1].valueCodeableConcept.coding[0].code = #native
    extension[ethnicity][1].valueCodeableConcept.coding[0].display = "Native American"
    



## Most Commonly used Valuesets for OH:
