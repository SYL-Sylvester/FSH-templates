# This is an example patient profile

## An example of A valueset definitions and two slice defintions


### // Patient Profile

    Profile: MyPatientProfile
    Parent: Patient
    * identifier 1..* MS
    * identifier ^slicing.discriminator.type = #pattern
    * identifier ^slicing.discriminator.path = "system"
    * identifier ^slicing.rules = #open
    * identifier contains IdentifierForMRN 1..1 and IdentifierForSSN 0..1
    * name 1..* MS
    * name.use from AdministrativeGenderVS (required)
    * gender 1..1 MS
    * birthDate 1..1 MS
    * address 1..*
    * address.use from ValueSet[AddressUse] (required)
    * telecom 1..*
    * telecom.system from ValueSet[ContactPointSystem] (required)
    * telecom.use from ValueSet[ContactPointUse] (required)

### // Define slices for identifier

    Slice: IdentifierForMRN
    * system = "urn:oid:1.2.3.4.5.6.7"
    * type from ValueSet[IdentifierType] (required)
  
    Slice: IdentifierForSSN
    * system = "http://hl7.org/fhir/sid/us-ssn"
    * type from ValueSet[IdentifierType] (required)

### // Supporting Artifact- ValueSet for Administrative Gender

    ValueSet: AdministrativeGenderVS
    * ^url = "http://hl7.org/fhir/ValueSet/administrative-gender"
    * ^version = "4.0.1"
    * include codes from system "http://hl7.org/fhir/administrative-gender"




