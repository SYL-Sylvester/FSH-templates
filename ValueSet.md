# Valueset Template

    ValueSet: MyValueSet
    Id: my-valuset
    Title: "Simple valueset
    Description: Template for valuesets
    * ^url = "http://example.org/fhir/ValueSet/MyValueSet"
    * ^version = "1.0.0"
    * #example-code (REQUIRED) / include codes from system 

    
### Example

    ValueSet: ListEthnicityVS
    Id: list-ethnicity-vs
    Title: "Simple Ethnicity Value Set"
    Description: "A value set listing two ethnicities with a canonical URL."
    * ^url = "http://example.org/fhir/ValueSet/ethnicity-vs"
    * ^version = "1.0.0"
    * ^status = #draft 
    // codesystem
    * include codes from system http://terminology.hl7.org/CodeSystem/v3-Ethnicity
    // list
    * #hispanic "Hispanic" "Individuals who identify as Hispanic or Latino."
    * #native "Native American" "Individuals who identify as Native American or Alaska Native."

## To bind to a profile

Profile: MyPatientProfile

    * maritalStatus from MyValueSet (required)

## Most Commonly used Valuesets for OH:
