# This is an example Observation profile

## An example with supporting artifacts and profiles for references

### // Observation Profile Definition: Blood Pressure

    Profile: BloodPressureObservationProfile
    Parent: Observation
    Id: blood-pressure-observation-profile
    Title: "Blood Pressure Observation Profile"
    Description: "Profile for representing Blood Pressure"
    
    // --- Must Support Flags ---
    * status MS // Observation status (e.g., final, amended)
    * category MS // Vital Signs category
    * code MS // The overall panel code (LOINC 85354-9)
    * subject MS // The patient
    * effective[x] MS // When the BP was taken
    * performer MS // Who took the BP
    * component MS // The component backbone element itself
    * component.code MS // Code for each component (Systolic/Diastolic)
    * component.valueQuantity MS // Value/unit for each component
    
    // --- Cardinality / Existence Constraints ---
    * subject 1..1 // Must be linked to a patient
    * component 2..2 // Must have exactly TWO components
    
    // --- Fixed Values / ValueSet Bindings ---
    // Typically, only final BPs would conform, but could bind to VS if needed
    * status = #final
    
    // Fix the category to 'vital-signs'
    * category = http://terminology.hl7.org/CodeSystem/observation-category#vital-signs
    
    // Fix the main code to the LOINC Blood Pressure Panel code
    * code = http://loinc.org#85354-9 "Blood pressure panel with all children optional"
    
    // --- Type Constraints & Reference Profiling ---
    * subject only Reference(MyPatientProfile)
    * performer only Reference(MyPractitionerProfile) // Assumes Practitioner performer
    * device only Reference(MyDeviceProfile) // Optional constraint on device used
    
    // --- Slicing the 'component' Element ---
    // Slice components based on their specific LOINC code using pattern matching
    * component ^slicing.discriminator.type = #pattern
    * component ^slicing.discriminator.path = "code" // Discriminate by the component's code element
    * component ^slicing.rules = #closed // Only allow the defined slices (Systolic, Diastolic)
    * component ^slicing.ordered = false
    
    // --- Define Systolic Component Slice ---
    * component contains systolicBP 1..1 MS // Slice name: systolicBP, Mandatory (1..1), Must Support
    * component[systolicBP].code = http://loinc.org#8480-6 "Systolic blood pressure" // Fix the code for this slice
    * component[systolicBP].valueQuantity 1..1 // Systolic value is required
    * component[systolicBP].valueQuantity.system = "http://unitsofmeasure.org" // Enforce UCUM units
    * component[systolicBP].valueQuantity.code = #"mm[Hg]" // Enforce code for millimeters of mercury
    * component[systolicBP].valueQuantity.unit = "mmHg" // Enforce unit display string
    
    // --- Define Diastolic Component Slice ---
    * component contains diastolicBP 1..1 MS // Slice name: diastolicBP, Mandatory (1..1), Must Support
    * component[diastolicBP].code = http://loinc.org#8462-4 "Diastolic blood pressure" // Fix the code for this slice
    * component[diastolicBP].valueQuantity 1..1 // Diastolic value is required
    * component[diastolicBP].valueQuantity.system = "http://unitsofmeasure.org" // Enforce UCUM units
    * component[diastolicBP].valueQuantity.code = #"mm[Hg]" // Enforce code for millimeters of mercury
    * component[diastolicBP].valueQuantity.unit = "mmHg" // Enforce unit display string


### // Supporting Artifacts: Placeholder Profiles for References

    Profile: MyPatientProfile
    Parent: Patient
    Id: my-patient-profile
    Title: "My Patient Profile (Placeholder)"
    Description: "Placeholder profile for Patient resources."
    Status: draft
    * identifier MS
    
    Profile: MyPractitionerProfile
    Parent: Practitioner
    Id: my-practitioner-profile
    Title: "My Practitioner Profile (Placeholder)"
    Description: "Placeholder profile for Practitioner resources."
    Status: draft
    * identifier MS
    
    Profile: MyDeviceProfile
    Parent: Device
    Id: my-device-profile
    Title: "My Device Profile (Placeholder)"
    Description: "Placeholder profile for Device resources (e.g., BP cuff)."
    Status: draft
    * manufacturer MS
    * modelNumber MS
