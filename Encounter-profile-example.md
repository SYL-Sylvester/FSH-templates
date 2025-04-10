# This is an example Encounter Profile

## An example with supporting artifacts and profiles for references

### // Encounter Profile Definition: Ambulatory Clinic Visit

    Profile: AmbulatoryClinicEncounterProfile
    Parent: Encounter
    Id: ambulatory-clinic-encounter-profile
    Title: "Ambulatory Clinic Encounter Profile"
    Description: "Profile constraints for encounters representing outpatient/ambulatory clinic visits."
    
    // --- Must Support Flags ---
    // Key elements implementers must support
    * status MS
    * class MS
    * type MS
    * subject MS
    * participant MS // Participant backbone element
    * participant.type MS // Type of participation
    * participant.individual MS // The participating individual (e.g., Practitioner)
    * period MS // Encounter time period
    * period.start MS // Specifically the start time
    * reasonCode MS // Reason for encounter (coded)
    * serviceProvider MS // Organization providing service
    
    // --- Cardinality / Existence Constraints ---
    * subject 1..1 // Patient subject is mandatory
    * participant 1..* // Must be at least one participant recorded
    * period.start 1..1 // Encounter must have a start time
    
    // --- Fixed Values / ValueSet Bindings ---
    // This profile only represents encounters that are finished
    * status = #finished
    
    // The class of encounter MUST be Ambulatory (AMB) from the HL7 v3 ActCode system
    * class = http://terminology.hl7.org/CodeSystem/v3-ActCode#AMB "ambulatory"
    
    // The type of encounter MUST use a code from our custom ClinicVisitTypeVS
    * type from ClinicVisitTypeVS (required)
    
    // Participant type MUST use a code from the standard HL7 ParticipationType ValueSet
    * participant.type from http://terminology.hl7.org/ValueSet/v3-ParticipationType (required)
    
    // --- Type Constraints & Reference Profiling ---
    // The subject must be a Patient conforming to MyPatientProfile
    * subject only Reference(MyPatientProfile)
    
    // The participating individual must be a Practitioner conforming to MyPractitionerProfile
    * participant.individual only Reference(MyPractitionerProfile)
    
    // The service provider must be an Organization conforming to MyOrganizationProfile
    * serviceProvider only Reference(MyOrganizationProfile)
    
    // --- Slicing Example (Optional but common) ---
    // Ensure there is at least one participant identified as the Primary Performer (PRF)
    * participant ^slicing.discriminator.type = #pattern // Slice by the type sub-element
    * participant ^slicing.discriminator.path = "type"
    * participant ^slicing.rules = #open // Allow other participant types
    * participant contains primaryPerformer 1..1 MS // Define a slice named 'primaryPerformer', require 1, mark MS
    * participant[primaryPerformer].type = http://terminology.hl7.org/CodeSystem/v3-ParticipationType#PRF "performer" // Fix the type for this slice to PRF (Performer)
    
    // --- Extension Usage ---
    // Allow the optional Follow-up Required flag, mark as Must Support
    // *** Replace URL with your actual canonical URL for the extension ***
    * extension contains http://example-clinic.com/fhir/StructureDefinition/followup-required 0..1 MS


  ### // Supporting Artifact: CodeSystem for Clinic Visit Type

    CodeSystem: ClinicVisitTypeCS
    Id: clinic-visit-type-cs
    Title: "Clinic Visit Type Code System"
    Description: "Codes classifying the primary reason or type for an ambulatory clinic visit."
    Status: active
    * #checkup "Check-up" "A routine check-up or physical examination."
    * #followup "Follow-up" "A follow-up visit regarding a previous issue or treatment."
    * #newissue "New Issue" "A visit for a new medical concern."
    * #procedure "Procedure" "A visit primarily for a minor procedure."
    * #consult "Consultation" "A specialist consultation visit."


### // Supporting Artifact: ValueSet for Clinic Visit Type

    ValueSet: ClinicVisitTypeVS
    Id: clinic-visit-type-vs
    Title: "Clinic Visit Type Value Set"
    Description: "Includes all codes from the ClinicVisitTypeCS CodeSystem."
    Status: active
    * include codes from system ClinicVisitTypeCS


### // Supporting Artifact: Extension for Follow-up Required Flag

    Extension: FollowupRequiredExtension
    Id: followup-required
    Title: "Follow-up Required Flag"
    Description: "Indicates whether follow-up is required after this encounter."
    Status: draft
    Context: Encounter // Applicable to the Encounter resource root
    * value[x] only boolean
    * valueBoolean 1..1 // If extension is used, the boolean value is required
    
    // Canonical URL Example (Replace with your actual base):
    // Url: http://example-clinic.com/fhir/StructureDefinition/followup-required


### // Supporting Artifacts: Placeholder Profiles for References

    Profile: MyPatientProfile
    Parent: Patient
    Id: my-patient-profile
    Title: "My Patient Profile (Placeholder)"
    Description: "Placeholder profile for Patient resources."
    Status: draft
    * identifier MS // Example constraint
    
    Profile: MyPractitionerProfile
    Parent: Practitioner
    Id: my-practitioner-profile
    Title: "My Practitioner Profile (Placeholder)"
    Description: "Placeholder profile for Practitioner resources."
    Status: draft
    * identifier MS // Example constraint
    
    Profile: MyOrganizationProfile
    Parent: Organization
    Id: my-organization-profile
    Title: "My Organization Profile (Placeholder)"
    Description: "Placeholder profile for Organization resources (e.g., clinic)."
    Status: draft
    * identifier MS // Example constraint
    * type MS
