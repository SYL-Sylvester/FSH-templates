# Codesystem Templates

*Here are a few templates for writing CodeSystems using FHIR Shorthand (FSH)*

## Template 1: Basic CodeSystem with Simple Codes
This is the most fundamental template for defining a CodeSystem with a list of codes.

    CodeSystem: [CodeSystem Name]
    Id: [code-system-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the code system.]"
    * #code1 "[Display Name for Code 1]" "[Definition for Code 1]"
    * #code2 "[Display Name for Code 2]" "[Definition for Code 2]"
    * #another-code "[Display Name for Another Code]" "[Definition for Another Code]"
    // Add more codes as needed



## Template 2: CodeSystem with a ValueSet Binding
While not directly part of the CodeSystem definition, it's common to create a ValueSet that includes the codes from your CodeSystem.

    CodeSystem: [CodeSystem Name]
    Id: [code-system-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the code system.]"
    * #codeA "Display A" "Definition A"
    * #codeB "Display B" "Definition B"

// --- Separate ValueSet definition (often in a different .fsh file) ---

    ValueSet: [ValueSet Name]
    Id: [value-set-id]
    Title: "[Human-Readable Title for ValueSet]"
    Description: "[A brief description of the value set.]"
    * include codes from system [Canonical URL for the CodeSystem]

*The separate ValueSet definition uses * include codes from system [Canonical URL for the CodeSystem] to include all codes from the CodeSystem you just defined. This is the typical way to link a CodeSystem to a ValueSet that contains all its codes.*




## Template 3: CodeSystem with Hierarchy (using concept element)
This template shows how to define a hierarchical structure within your CodeSystem.

    CodeSystem: [CodeSystem Name]
    Id: [code-system-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the code system with hierarchy.]"
    * ^hierarchyMeaning = #[grouped-by|is-a|part-of|classified-with|...] // Optional: Describe the hierarchy
    * concept
      * code: code1
        display: "[Display Name for Code 1]"
        definition: "[Definition for Code 1]"
      * concept
        * code: code1-1
          display: "[Display Name for Code 1.1]"
          definition: "[Definition for Code 1.1]"
        * code: code1-2
          display: "[Display Name for Code 1.2]"
          definition: "[Definition for Code 1.2]"
      * concept
        * code: code2
          display: "[Display Name for Code 2]"
          definition: "[Definition for Code 2]"

* The basic CodeSystem metadata is the same.
* The * concept keyword introduces a top-level concept.
* Nested * concept lines define child concepts, creating a hierarchy.
* Each concept has code, display, and definition.
* ^hierarchyMeaning (optional) can be used to specify the type of hierarchical relationship (e.g., "is-a" for a taxonomy).

### Example

    CodeSystem: MedicalEquipmentTypes
    Id: medical-equipment-types
    Title: "Medical Equipment Types"
    Description: "A hierarchical code system for classifying types of medical equipment."
    * ^hierarchyMeaning = #is-a // Indicates an "is-a" relationship

    * concept
      * code: device
        display: "Medical Device"
        definition: "A general category for medical devices."
      * concept
        * code: monitoring
          display: "Monitoring Device"
          definition: "Devices used for observing or tracking a patient's condition."
        * concept
          * code: blood-pressure-monitor
            display: "Blood Pressure Monitor"
            definition: "Device used to measure blood pressure."
          * code: cardiac-monitor
            display: "Cardiac Monitor"
            definition: "Device used to monitor heart activity."
      * concept
        * code: therapeutic
          display: "Therapeutic Device"
          definition: "Devices used for treating a medical condition."
        * concept
          * code: infusion-pump
            display: "Infusion Pump"
            definition: "Device used to deliver fluids into a patient's body."
          * code: ventilator
            display: "Ventilator"
            definition: "Device used to assist or control breathing."

*Explanation of this Example:*

* CodeSystem: MedicalEquipmentTypes: The name of our CodeSystem.
* Id: medical-equipment-types: The logical ID.
* Title: "Medical Equipment Types": The human-readable title.
* Description: "A hierarchical code system for classifying types of medical equipment.": Describes the purpose.
* * ^url = "http://example.org/fhir/CodeSystem/medical-equipment-types": The canonical URL for this CodeSystem.
* * ^version = "1.0.0": The version of the CodeSystem.
* * ^status = #draft: The status is set to "draft".
* * ^hierarchyMeaning = #is-a: We specify that the hierarchy represents an "is-a" relationship (e.g., a Blood Pressure Monitor is a Monitoring Device, which is a Medical Device).
* * concept: Introduces the top-level concept "Medical Device" with code device.
* Nested * concept: Define child concepts:
  * "Monitoring Device" (monitoring) and "Therapeutic Device" (therapeutic) are children of "Medical Device".
  * "Blood Pressure Monitor" (blood-pressure-monitor) and "Cardiac Monitor" (cardiac-monitor) are children of "Monitoring Device".
  * "Infusion Pump" (infusion-pump) and "Ventilator" (ventilator) are children of "Therapeutic Device".
* This example demonstrates how you can structure a CodeSystem with a clear hierarchy using the concept element in FSH. When this CodeSystem is processed by SUSHI, it will generate a FHIR CodeSystem resource that represents this hierarchical structure.




## Template 4: CodeSystem with Properties
This template demonstrates how to add properties to the CodeSystem and individual codes.

    CodeSystem: [CodeSystem Name]
    Id: [code-system-id]
    Title: "[Human-Readable Title]"
    Description: "[A brief description of the code system with properties.]"
    * ^property.code = "copyright"
    * ^property.uri = "http://example.org/fhir/CodeSystem/my-codes/copyright"
    * ^property.description = "Copyright information for this code system."
    * ^property.type = #string

    * property[copyright] = "© 2023 My Organization"

    * #codeA "Display A" "Definition A"
      * property.code = "alt-code"
      * property.valueCode = "alternate-a"
      * property.code = "deprecated"
      * property.valueBoolean = true

    * #codeB "Display B" "Definition B"
      * property.code = "alt-code"
      * property.valueCode = "alternate-b"

* The basic CodeSystem metadata is the same.
* *^property.code, * ^property.uri, * ^property.description, * ^property.type define properties for the CodeSystem itself.
* *property[copyright] = "..." assigns a value to the "copyright" property of the CodeSystem.
* Within each code definition (* #codeA ...), you can use * property.code, * property.valueCode, * property.valueBoolean, etc., to add properties to individual codes.

### Example

    CodeSystem: MedicationAdminStatusCodes
    Id: medication-admin-status-codes
    Title: "Medication Administration Status Codes with Properties"
    Description: "A code system for the status of a medication administration, including copyright and alternate codes."
    * ^url = "http://example.org/fhir/CodeSystem/medication-admin-status-codes"
    * ^version = "1.0.0"
    * ^status = #active

    * ^property.code = "copyright"
    * ^property.uri = "http://example.org/fhir/CodeSystem/medication-admin-status-codes/copyright"
    * ^property.description = "Copyright information for this code system."
    * ^property.type = #string

    * property[copyright] = "© 2025 Example Healthcare Inc."

    * #in-progress "In Progress" "The medication administration is currently being performed."
      * property.code = "alt-code"
      * property.valueCode = "active"
      * property.code = "notes"
      * property.valueString = "Administration has started but not yet completed."

    * #completed "Completed" "The medication administration has been successfully completed."
      * property.code = "alt-code"
      * property.valueCode = "done"

    * #on-hold "On Hold" "The medication administration has been temporarily paused."
      * property.code = "notes"
      * property.valueString = "Administration is paused and may resume later."

    * #cancelled "Cancelled" "The medication administration has been stopped and will not be completed."
      * property.code = "deprecated"
      * property.valueBoolean = false // Not deprecated in this example

    * #entered-in-error "Entered in Error" "The medication administration record was created in error."
      * property.code = "deprecated"
      * property.valueBoolean = true
      * property.code = "replaces"
      * property.valueCode = "incorrect-record"

*Explanation of this Example:*

* CodeSystem: MedicationAdminStatusCodes: The name of our CodeSystem.
* Id: medication-admin-status-codes: The logical ID.
* Title: "Medication Administration Status Codes with Properties": The human-readable title.
* Description: "A code system for the status of a medication administration, including copyright and alternate codes.": Describes the purpose.
* * ^url = "http://example.org/fhir/CodeSystem/medication-admin-status-codes": The canonical URL for this CodeSystem.
* * ^version = "1.0.0": The version of the CodeSystem.
* * ^status = #active: The status is set to "active".
* * ^property.code = "copyright", etc.: Define the properties for the CodeSystem itself (copyright in this case).
* * property[copyright] = "© 2025 Example Healthcare Inc.": Assigns the copyright information to the CodeSystem.
* * #in-progress "In Progress" "...": Defines the code "in-progress" with its display and definition.
  * * property.code = "alt-code", * property.valueCode = "active": Adds a property named "alt-code" with the value "active" to the "in-progress" code.
  * * property.code = "notes", * property.valueString = "...": Adds a "notes" property with a string value.
* Similar property assignments are done for other codes like "completed", "on-hold", "cancelled", and "entered-in-error", demonstrating the use of alt-code, deprecated (with a boolean value), and replaces.
* This example shows how you can add metadata to your CodeSystem and specific information (properties) to individual codes within it using the FSH syntax. These properties can be useful for documentation, tooling, or extending the basic meaning of the codes.
