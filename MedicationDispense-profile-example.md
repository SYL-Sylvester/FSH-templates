# This is an example MedicationDispense profile

## An example with supporting artifacts and profiles for references

### // MedicationDispense Profile Definition

    Profile: PharmaMedicationDispenseProfile
    Parent: MedicationDispense
    Id: pharma-medication-dispense-profile
    Title: "Pharmacy MedicationDispense Profile"
    Description: "A profile defining constraints for MedicationDispense resources within a pharmacy context."
    Status: draft
    
    // --- Must Support Flags ---
    // Key elements that implementers must demonstrate support for
    * status MS
    * medication[x] MS
    * subject MS
    * performer MS // The performer backbone element itself
    * performer.actor MS // The actor reference within performer
    * authorizingPrescription MS
    * quantity MS
    * daysSupply MS
    * whenPrepared MS
    * whenHandedOver MS
    * dosageInstruction MS
    
    // --- Cardinality Constraints ---
    // Elements that must be present
    * subject 1..1 // Patient is mandatory
    * performer 1..* // Must have at least one performer (dispenser)
    * dosageInstruction 1..* // Must include dosage instructions
    
    // --- Type Constraints & Reference Profiling ---
    // Medication must be provided as a reference (not inline codeableconcept)
    // and it must conform to our specific Medication profile.
    * medication[x] only Reference(MyMedicationProfile)
    
    // The actor performing the dispense must be a Practitioner or Organization
    // conforming to our specific profiles for those resources.
    * performer.actor only Reference(MyPractitionerProfile | MyOrganizationProfile)
    
    // --- ValueSet Bindings ---
    // Status must be one of the codes defined in the standard HL7 ValueSet.
    * status from http://hl7.org/fhir/ValueSet/medicationdispense-status (required)
    
    // The 'type' of dispense must be one of the codes from our custom ValueSet.
    * type from DispenseTypeVS (required)
    
    // --- Extension Usage ---
    // Allow the optional (0..1) Dispensing Pharmacy ID extension at the root, mark as Must Support.
    // *** Replace URL with your actual canonical URL for the extension ***
    * extension contains http://example-pharmacy.com/fhir/StructureDefinition/dispensing-pharmacy-id 0..1 MS

### // Supporting Artifact: CodeSystem for Dispense Type

    CodeSystem: DispenseTypeCS
    Id: dispense-type-cs
    Title: "Dispense Type Code System"
    Description: "Codes classifying the type of medication dispense event."
    Status: active
    * #initial "Initial Fill" "The first filling of a prescription."
    * #refill "Refill" "A subsequent filling of a prescription."
    * #trial "Trial Fill" "A partial fill to determine tolerance or effectiveness."
    * #emergency "Emergency Supply" "A dispense provided in an emergency situation without a prior prescription."
    * #sample "Sample" "A free sample provided to the patient."

### // Supporting Artifact: ValueSet for Dispense Type

    ValueSet: DispenseTypeVS
    Id: dispense-type-vs
    Title: "Dispense Type Value Set"
    Description: "Includes all codes from the DispenseTypeCS CodeSystem."
    Status: active
    * include codes from system DispenseTypeCS

### // Supporting Artifact: Extension for Dispensing Pharmacy ID

    Extension: DispensingPharmacyIdExtension
    Id: dispensing-pharmacy-id
    Title: "Dispensing Pharmacy Identifier"
    Description: "An identifier associated with the specific pharmacy location performing the dispense."
    Status: active
    // This extension applies to the root of the MedicationDispense resource
    Context: MedicationDispense
    // The value must be an Identifier data type
    * value[x] only Identifier
    // If this extension is used, the Identifier must be populated...
    * valueIdentifier 1..1
    * valueIdentifier.system 1..1 // ...including system...
    * valueIdentifier.value 1..1 // ...and value.
    
    // Canonical URL Example (Replace with your actual base):
    // Url: http://example-pharmacy.com/fhir/StructureDefinition/dispensing-pharmacy-id

### // Supporting Artifacts: Placeholder Profiles for References

    Profile: MyMedicationProfile
    Parent: Medication
    Id: my-medication-profile
    Title: "My Medication Profile (Placeholder)"
    Description: "Placeholder profile representing how medications should be structured."
    Status: draft
    * code MS // Example: Require medication code support
    
    Profile: MyPractitionerProfile
    Parent: Practitioner
    Id: my-practitioner-profile
    Title: "My Practitioner Profile (Placeholder)"
    Description: "Placeholder profile representing how practitioners should be structured."
    Status: draft
    * identifier MS // Example: Require practitioner identifier support
    
    Profile: MyOrganizationProfile
    Parent: Organization
    Id: my-organization-profile
    Title: "My Organization Profile (Placeholder)"
    Description: "Placeholder profile representing how organizations (like pharmacies) should be structured."
    Status: draft
    * identifier MS // Example: Require organization identifier support
    * type MS
