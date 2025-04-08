# Extension Templates

*Here are a few templates for writing Extensions using FHIR Shorthand (FSH)*

## Template 1: Simple Value Extension
This template defines an extension with a single value of a specific data type.

    Extension: [Extension Name]
    Id: [extension-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the extension's purpose.]"
    * context [ResourceType1], [ResourceType2] ... // List the resource types where this extension can be used
    * value[x] : [DataType]  // Replace [x] with the capitalized data type (e.g., valueString, valueCodeableConcept)

*Explanation*

* Extension: [Extension Name]: Declares an Extension with a human-friendly name.
* Id: [extension-id]: Sets the logical identifier (kebab-case is common).
* Title: "[Human-Readable Title]": Provides a descriptive title.
* Description: "[A brief description ... ]": Explains the purpose of the Extension.
  * context [ResourceType1], ...: Specifies the FHIR resource types where this extension can be applied. List multiple resource types separated by commas.
  * value[x] : [DataType]: Defines the value of the extension:
      * value[x] is the element name. Replace [x] with the capitalized FHIR data type (e.g., valueString, valueInteger, valueCodeableConcept, valueReference).
      * :[DataType] specifies the data type of the value.

### Example of using a Simple Value Extension:

    // This is a simple value extension to capture "Preferred Language of Communication"
// First, we need a simple CodeSystem and ValueSet for the language codes used in our extension. We'll use standard ISO language codes.
    
    CodeSystem: LanguageCodeSystem
    Id: language-cs
    Title: "Language Code System"
    Description: "Standard ISO 639-1 language codes."
    * #en "English"
    * #fr "French"
    * #es "Spanish"
    * #it "Italian"
    * #zh "Chinese"
    * #pa "Punjabi"
    * #ur "Urdu"

    ValueSet: LanguageVS
    Id: language-vs
    Title: "Language Value Set"
    Description: "A value set for common languages based on ISO 639-1."
    * include codes from system LanguageCodeSystem

// Then the structure of our new preferred-language extension.

    Extension: PreferredLanguageExtension
    Id: preferred-language // The machine-readable ID for this extension definition
    Title: "Preferred Language of Communication"
    Description: "Specifies the patient's preferred language for spoken and written communication."

    // Context specifies where this extension can be used.
    // Using 'Patient' means it can appear at the root level of a Patient resource.
    Context: Patient

    // Define the extension's value: it must be a CodeableConcept...
    * value[x] only CodeableConcept

    // ...and that CodeableConcept MUST be bound to our LanguageVS ValueSet.
    * valueCodeableConcept from LanguageVS (required)

// This is how we bind to a resource profile.

    Profile: OntarioPatientProfile
    Parent: Patient
    Id: ontario-patient-profile
    Title: "Ontario Patient Profile"
    Description: "A profile for Patient resources in Ontario, requiring preferred language."

    // --- Core Element Constraints (Examples) ---
    * identifier MS // Must support identifiers
    * identifier.system 1..*
    * identifier.value 1..*
    * name MS
    * birthDate MS
    * gender MS

    // --- Extension Constraint ---
    // Use 'contains' to indicate the profile uses this extension.
    // Refer to the extension by its Id ('preferred-language').
    // Cardinality '1..1' makes it mandatory in this profile.
    // 'MS' means implementers must support it.
    * extension contains preferred-language 1..1 MS

// Example in a Patient profile instance

    Instance: ExamplePatientOntario
    InstanceOf: OntarioPatientProfile // States that this instance conforms to our profile
    Usage: #example
    Title: "Example Ontario Patient Instance"
    Description: "An example Patient conforming to the Ontario profile, including the Preferred Language extension."

    * identifier[0].system = "urn:oid:1.2.840.113554.1.2.3.4.5.6" // Example OID for Ontario HCN
    * identifier[0].value = "1234567890-AB"
    * name[0].family = "Singh"
    * name[0].given = "Amar"
    * birthDate = "1975-05-15"
    * gender = #male

    // --- Using the Extension ---
    // Reference the extension by its Id ('preferred-language').
    // Provide the value as a CodeableConcept, using the FSH shorthand for coding.
    // The code MUST come from the 'LanguageVS' we bound in the extension definition.
    * extension[preferred-language].valueCodeableConcept = LanguageCodeSystem#pa "Punjabi"

    // You could also populate the extension value more verbosely:
    // * extension[preferred-language].valueCodeableConcept.coding[0].system = "http://your-canonical-url/CodeSystem/language-cs" // Replace with actual canonical URL
    // * extension[preferred-language].valueCodeableConcept.coding[0].code = #pa
    // * extension[preferred-language].valueCodeableConcept.coding[0].display = "Punjabi"
    // * extension[preferred-language].valueCodeableConcept.text = "Prefers Punjabi"

*There are 2 valid methods for populating a valueCodeableConcept in a FSH instance, but they offer different levels of control and detail.*

Use:

    * extension[preferred-language].valueCodeableConcept = LanguageCodeSystem#pa "Punjabi"

...when:

Simple Case: You only need to provide one single coding for the CodeableConcept.
- Known CodeSystem: The CodeSystem (LanguageCodeSystem in this case) is defined within your FSH project, so the FSH compiler knows its canonical URL.
- Default Display: The display text provided in the shorthand ("Punjabi") is sufficient for the coding.display.
- No CodeableConcept.text Needed: You don't need to populate the separate valueCodeableConcept.text field (the human-readable summary of the concept).
- No Other Coding Details: You don't need to specify other details within the Coding element itself, like version or userSelected.

*In short, use the shorthand for the common, straightforward case of providing a single code from a known system. It's concise and readable.*

Use the verbose method:

    * extension[preferred-language].valueCodeableConcept.coding[0].system = "http://your-canonical-url/CodeSystem/language-cs" // Replace with actual canonical URL
    * extension[preferred-language].valueCodeableConcept.coding[0].code = #pa
    * extension[preferred-language].valueCodeableConcept.coding[0].display = "Punjabi"
    * extension[preferred-language].valueCodeableConcept.text = "Prefers Punjabi"
...when:

- Populating CodeableConcept.text: You specifically need to set the .text element of the CodeableConcept. The shorthand method does not populate this.
- Providing Multiple Codings: You need to represent the same concept with codes from different systems within the same CodeableConcept. You would use the verbose method, incrementing the index (coding[0], coding[1], etc.) for each coding system/code pair.

// Example: Multiple codings (hypothetical)

    * extension[some-condition].valueCodeableConcept.coding[0].system = "http://snomed.info/sct"
    * extension[some-condition].valueCodeableConcept.coding[0].code = #386661006 // Diabetes mellitus type 2
    * extension[some-condition].valueCodeableConcept.coding[0].display = "Diabetes mellitus type 2 (disorder)"
    * extension[some-condition].valueCodeableConcept.coding[1].system = "http://hl7.org/fhir/sid/icd-10-cm"
    * extension[some-condition].valueCodeableConcept.coding[1].code = #E11.9 // Type 2 diabetes mellitus without complications
    * extension[some-condition].valueCodeableConcept.coding[1].display = "Type 2 diabetes mellitus without complications"
    * extension[some-condition].valueCodeableConcept.text = "Type 2 Diabetes"  

- Specifying coding.version or coding.userSelected: If you need to set specific properties of the Coding element itself, like the version of the code system (.version) or whether the code was selected by a user (.userSelected = true), you need the verbose method to access these sub-elements.
- Using External/Undefined CodeSystems: If the CodeSystem's canonical URL isn't defined locally via an FSH CodeSystem definition (e.g., you're referencing a code from a system completely external to your project), you must provide the full URL string explicitly using .system = "...". The shorthand relies on the FSH compiler knowing the system's definition.
- Explicit Clarity/Control: In very complex cases, you might choose the verbose method simply to be absolutely explicit about every piece of data being populated, leaving nothing to the compiler's inference.
  
*In short, use the verbose method when you need more control, need to populate optional elements like .text or .version, need multiple codings, or are referencing systems not defined locally.*











    

## Template 2: Complex Value Extension
This template defines an extension where the value is a complex data type or a contained resource.

    Extension: [Extension Name]
    Id: [extension-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the extension with a complex value.]"
    * ^url = "[Canonical URL for the Extension]"
    * ^version = "[Version number]"
    * ^status = #[draft|active|retired|...]
    * context [ResourceType1], ...
    * contextType #[...]
    * value[x] : [ComplexDataType] // e.g., valueAddress, valueHumanName, valueReference(ResourceType)

### Example of Complex Value Extension
    Extension: PatientUsualAddress
    Id: patient-usual-address
    Title: "Patient Usual Address"
    Description: "Extension to record the patient's usual address (may differ from legal address)."
    * ^url = "http://example.org/fhir/StructureDefinition/patient-usual-address"
    * ^version = "1.0.0"
    * ^status = #draft
    * context Patient
    * contextType #resource
    * valueAddress : Address


## Template 3: Extension with Multiple Value Fields
This template defines an extension with multiple fields to capture different pieces of information. This is achieved by defining child elements within the extension.

    Extension: [Extension Name]
    Id: [extension-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the extension with multiple fields.]"
    * ^url = "[Canonical URL for the Extension]"
    * ^version = "[Version number]"
    * ^status = #[draft|active|retired|...]
    * context [ResourceType1], ...
    * contextType #[...]
    * value[x] MS // The primary value (often a BackboneElement)

    // Define child elements within the extension

    * value[x].field1 : [DataType1] MS // Must Support field
    * value[x].field2 : [DataType2] 0..1 // Optional field
    * value[x].field3 : [DataType3] // Required field (cardinality 1..1 implied)
// Add more fields as needed

### Example of Extension with Multiple Value Fields:

    Extension: ObservationInterpretationQualifier
    Id: observation-interpretation-qualifier
    Title: "Observation Interpretation Qualifier"
    Description: "Extension to provide additional detail about the interpretation of an observation."
    * ^url = "http://example.org/fhir/StructureDefinition/observation-interpretation-qualifier"
    * ^version = "1.0.0"
    * ^status = #draft
    * context Observation.interpretation
    * contextType #element
    * valueCodeableConcept MS // The primary value

    * valueCodeableConcept.coding MS
    * valueCodeableConcept.coding.system MS
    * valueCodeableConcept.coding.code MS
    * valueCodeableConcept.coding.display MS
    * valueCodeableConcept.text 0..1
    * valueString 0..1 // Optional free-text qualifier


## Template 4: Constrained Extension
This template shows how to create an extension that further constrains the data type or cardinality of its value, often based on an existing extension.

    Extension: [Extension Name]
    Parent: [Base Extension URL] // URL of the extension you are constraining
    Id: [extension-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the constrained extension.]"
    * ^url = "[Canonical URL for the Constrained Extension]"
    * ^version = "[Version number]"
    * ^status = #[draft|active|retired|...]
    * value[x] only [SpecificDataType] // Constrain the data type
    * value[x] 1..1 // Constrain the cardinality
    // Add other constraints as needed

### Example of Constrained Extension:

    Extension: USCoreRaceExtension
    Parent: http://hl7.org/fhir/StructureDefinition/us-core-race
    Id: my-us-core-race
    Title: "My US Core Race Extension"
    Description: "A specific profile of the US Core Race Extension, perhaps with additional constraints."
    * ^url = "http://example.org/fhir/StructureDefinition/my-us-core-race"
    * ^version = "1.0.0"
    * ^status = #draft
    * valueCodeableConcept 1..1 // Making it mandatory in this profile

*Remember to replace the bracketed placeholders with your specific details when creating your extensions. The canonical URL (^url) is particularly important as it uniquely identifies your extension within the FHIR ecosystem.*
