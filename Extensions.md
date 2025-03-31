# Extension Templates

*Here are a few templates for writing CodeSystems using FHIR Shorthand (FSH)*

## Template 1: Simple Value Extension
This template defines an extension with a single value of a specific data type.

    Extension: [Extension Name]
    Id: [extension-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the extension's purpose.]"
    * ^url = "[Canonical URL for the Extension (e.g., http://example.org/fhir/StructureDefinition/my-simple-extension)]"
    * ^version = "[Version number (e.g., 1.0.0)]"
    * ^status = #[draft|active|retired|...] // Optional: Set the status
    * context [ResourceType1], [ResourceType2] ... // List the resource types where this extension can be used
    * contextType #resource // Or #datatype, #extension, etc.
    * value[x] : [DataType]  // Replace [x] with the capitalized data type (e.g., valueString, valueCodeableConcept)

### Explanation

* Extension: [Extension Name]: Declares an Extension with a human-friendly name.
* Id: [extension-id]: Sets the logical identifier (kebab-case is common).
* Title: "[Human-Readable Title]": Provides a descriptive title.
* Description: "[A brief description ... ]": Explains the purpose of the Extension.
* * ^url = "[Canonical URL ... ]": Crucial: This is the unique identifier for the Extension. Replace the placeholder with your actual URL.
* * ^version = "[Version number ... ]": Indicates the version of the Extension.
* * ^status = #[...]: (Optional) Sets the lifecycle status.
* * context [ResourceType1], ...: Specifies the FHIR resource types where this extension can be applied. List multiple resource types separated by commas.
* * contextType #[...]: Indicates the type of context:
  * #resource: Applied directly to a resource.
  * #datatype: Applied within a datatype.
  * #extension: Applied within another extension.
  * #element: Applied to a specific element within a resource or datatype.
* * value[x] : [DataType]: Defines the value of the extension:
  * value[x] is the element name. Replace [x] with the capitalized FHIR data type (e.g., valueString, valueInteger, valueCodeableConcept, valueReference).
  * :[DataType] specifies the data type of the value.

### Example of Simple Value Extension:
    Extension: PatientPreferredName
    Id: patient-preferred-name
    Title: "Patient Preferred Name"
    Description: "Extension to record a patient's preferred name."
    * ^url = "http://example.org/fhir/StructureDefinition/patient-preferred-name"
    * ^version = "1.0.0"
    * ^status = #draft
    * context Patient
    * contextType #resource
    * valueString : string


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
