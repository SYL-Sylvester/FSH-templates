# Extension Templates

*Here are a few templates for writing Extensions using FHIR Shorthand (FSH)*

## Template 1: Simple Extension with a Primitive Value
Purpose: Adds a single piece of information represented by a primitive data type (string, boolean, integer, date, code, etc.).
Example: A flag indicating if a diagnosis is considered primary for an encounter.

// --- 1a. Extension Definition ---

    Extension: IsPrimaryDiagnosisExtension
    Id: is-primary-diagnosis
    Title: "Is Primary Diagnosis Flag"
    Description: "A simple boolean flag indicating if a condition is considered the primary diagnosis for an encounter or episode."
    Context: Condition // Applicable to the Condition resource

    // Define the single value - must be a boolean
    * value[x] only boolean
    // Optionally make the value mandatory if the extension is present
    * valueBoolean 1..1


// --- 1b. Profile Definition (Using the Extension) ---

    Profile: ConditionWithPrimaryFlag
    Parent: Condition
    Id: condition-primary-flag-profile
    Title: "Condition Profile with Primary Flag"
    Description: "Allows marking a Condition as primary."

    * code MS
    * subject MS

    // Apply the simple extension at the root level of Condition
    // Make it optional (0..1) and Must Support (MS)
    * extension contains http://your-canonical-url-base.com/fhir/StructureDefinition/is-primary-diagnosis 0..1 MS

// --- 1c. Instance Definition (Example) ---

    Instance: PrimaryDiagnosisExampleCondition
    InstanceOf: ConditionWithPrimaryFlag
    Usage: #example
    Title: "Example Condition Marked as Primary"

    * id = "cond-primary-ex1"
    * clinicalStatus = http://terminology.hl7.org/CodeSystem/condition-clinical#active
    * verificationStatus = http://terminology.hl7.org/CodeSystem/condition-ver-status#confirmed
    * code = http://snomed.info/sct#386661006 "Type 2 diabetes mellitus" // Example code
    * subject.reference = "Patient/example"

    // Use the extension and set its boolean value
    * extension[is-primary-diagnosis].valueBoolean = true

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
    

## Template 2: Complex Extension

// A "complex" extension is one that doesn't just have a single value[x] element, but instead groups multiple data elements together. Each data element within the complex extension is itself a nested "simple" extension with its own url (relative to the main extension) and value[x].

    Extension: MyComplexExtensionName // e.g., BillingDetailsExtension
    Id: my-complex-extension-id // e.g., billing-details
    Title: "My Complex Extension Title" // e.g., "Billing Details"
    Description: "A clear description of what this complex extension represents as a whole." // e.g., "Groups related billing information like invoice number and billing period."
    
    // Define where this complex extension structure can be used.
    // Can be a Resource (Patient, Claim, etc.), a DataType (Address, Identifier),
    // or another Extension. Use specific ElementDefinition paths if needed.
    Context: Resource // Example: Allow on any resource root
    
    // --- Root Level Constraints ---
    // A complex extension typically doesn't have a value at the root.
    // It serves as a container for its components.
    * value[x] 0..0
    
    // --- Component Definitions (Slices on 'extension') ---
    
    // Component 1: A mandatory text component (e.g., Invoice Number)
    * extension contains componentOneName 1..1 MS // Define slice name, make mandatory (1..1), Must Support (MS)
    * extension[componentOneName].url = "componentOneName" // *** CRITICAL: Fix the relative URL for this component ***
    * extension[componentOneName].value[x] only string // Define the data type for this component's value
    * extension[componentOneName].valueString MS // Can add constraints to the specific value type
    
    // Component 2: An optional date component (e.g., Issue Date)
    * extension contains componentTwoName 0..1 // Define slice name, make optional (0..1)
    * extension[componentTwoName].url = "componentTwoName" // *** CRITICAL: Fix the relative URL for this component ***
    * extension[componentTwoName].value[x] only date // Define the data type
    
    // --- Add more components as needed following the pattern above ---



### Example of Complex Extension

// This is a Complex extension for Funding source.
// We need these for the source component of our complex extension.

    CodeSystem: FundingSourceCS
    Id: funding-source-cs
    Title: "Funding Source Code System"
    Description: "Codes identifying the type of funding source."
    * #govt "Government Program"
    * #ins "Insurance"
    * #priv "Private Funding"
    * #research "Research Grant"
    * #other "Other"
    
    ValueSet: FundingSourceVS
    Id: funding-source-vs
    Title: "Funding Source Value Set"
    Description: "A value set for funding source types."
    * include codes from system FundingSourceCS

// This is the extension structure for grouping funding details.

    Extension: FundingSourceDetailExtension
    Id: funding-source-detail // The unique ID for this extension definition
    Title: "Funding Source Detail"
    Description: "Provides detailed information about a source of funding, including the source type/name, an identifier for the funding, the amount, and the applicable period."
    Status: draft
    Context: Patient // This extension can be used on the Patient resource root
    
    // --- Root Level Constraints ---
    * value[x] 0..0 // No single value at the root, it's a container
    
    // --- Component Definitions (Slices on 'extension') ---
    
    // Component 1: The source type/name (e.g., Government, Insurance) - Mandatory
    * extension contains source 1..1 MS
    * extension[source].url = "source" // Relative URL for this component
    * extension[source].value[x] only CodeableConcept
    * extension[source].valueCodeableConcept from FundingSourceVS (required) // Bind to our ValueSet
    
    // Component 2: An identifier for the specific funding/grant/policy - Optional
    * extension contains identifier 0..1 MS
    * extension[identifier].url = "identifier" // Relative URL for this component
    * extension[identifier].value[x] only Identifier
    
    // Component 3: The amount of funding - Optional
    * extension contains amount 0..1
    * extension[amount].url = "amount" // Relative URL for this component
    * extension[amount].value[x] only Money
    
    // Component 4: The period the funding applies to - Optional
    * extension contains fundingPeriod 0..1
    * extension[fundingPeriod].url = "fundingPeriod" // Relative URL for this component
    * extension[fundingPeriod].value[x] only Period

// This is how we bind to a resource profile.

    Profile: PatientWithFundingProfile
    Parent: Patient
    Id: patient-with-funding-profile
    Title: "Patient Profile with Funding Source Details"
    Description: "A Patient profile that includes the complex Funding Source Detail extension, allowing for multiple funding sources."
    Status: draft
    
    // --- Core Element Constraints (Examples) ---
    * identifier MS
    * name MS
    * birthDate MS
    
    // --- Extension Constraint ---
    // Include the complex extension. Allow multiple instances (0..*). Make it Must Support (MS).
    // Reference it by the Id of the Extension definition ('funding-source-detail').
    * extension contains funding-source-detail 0..* MS

// Example Patient Profile that includes one instance of the funding-source-detail complex extension.

    Instance: ExampleFundedPatient
    InstanceOf: PatientWithFundingProfile // Conforms to the profile above
    Usage: #example
    Title: "Example Patient with Funding Details"
    Description: "Demonstrates a Patient instance using the complex Funding Source Detail extension."
    
    // --- Basic Patient Data ---
    * id = "patient-funding-ex01"
    * identifier[0].use = #official
    * identifier[0].system = "urn:oid:1.2.840.113554.1.2.3.4.5.6" // Example Ontario HCN OID
    * identifier[0].value = "111222333-AB"
    * name[0].family = "Funding"
    * name[0].given = "Example"
    * birthDate = "1988-11-01"
    * gender = #female
    
    // --- Complex Extension Instance Data ---
    // Start the slice for the complex extension using its definition ID.
    // Use index [0] since cardinality is 0..* (even if there's only one instance here).
    // * extension[<ExtensionDefinitionId>][<index>]...
    
    // --- Funding Source 1 ---
    // Now, within the complex extension instance, add each component using *its* specific url.
    // * extension[<ExtensionDefinitionId>][<index>].extension[<componentUrl>].value<DataType> = <value>
    
    // Component: source (Mandatory)
    * extension[funding-source-detail][0].extension[source].valueCodeableConcept = FundingSourceCS#govt "Government Program"
    
    // Component: identifier (Optional)
    * extension[funding-source-detail][0].extension[identifier].valueIdentifier.system = "urn:oid:2.16.840.1.113883.3.4.5.6" // Example Funding Program OID
    * extension[funding-source-detail][0].extension[identifier].valueIdentifier.value = "ON-Disability-Support-123"
    * extension[funding-source-detail][0].extension[identifier].assigner.display = "Ontario Ministry of Community and Social Services"
    
    // Component: amount (Optional)
    * extension[funding-source-detail][0].extension[amount].valueMoney.value = 1250.00
    * extension[funding-source-detail][0].extension[amount].valueMoney.currency = #CAD
    
    // Component: fundingPeriod (Optional)
    * extension[funding-source-detail][0].extension[fundingPeriod].valuePeriod.start = "2024-04-01"
    
    
    // --- Optional: Example of a Second Funding Source ---
    /*
    * extension[funding-source-detail][1].extension[source].valueCodeableConcept = FundingSourceCS#ins "Insurance"
    * extension[funding-source-detail][1].extension[identifier].valueIdentifier.system = "urn:oid:1.1.1.1.1"
    * extension[funding-source-detail][1].extension[identifier].valueIdentifier.value = "PolicyXYZ-99"
    * extension[funding-source-detail][1].extension[amount].valueMoney.value = 800.00
    * extension[funding-source-detail][1].extension[amount].valueMoney.currency = #CAD
    * extension[funding-source-detail][1].extension[fundingPeriod].valuePeriod.start = "2024-01-01"
    * extension[funding-source-detail][1].extension[fundingPeriod].valuePeriod.end = "2024-12-31"


## Template 3: Constrained Extension

// A constrained extension is a Profile built on top of an existing Extension definition. You're not creating an entirely new extension type from scratch, but rather taking a more general base extension and making it more specific for a particular use case by adding constraints.

// Example:

Let's assume there's a base extension GeneralCommentExtension that allows a string or Annotation as its value. We want to create a constrained version ClinicalNoteExtension that only allows a string and makes the value mandatory.

    // --- Assume this Base Extension is defined elsewhere ---
    /*
    Extension: GeneralCommentExtension
    Id: general-comment-extension
    Title: "General Comment"
    Description: "A general comment or note, allowing string or annotation."
    Context: Element // Can be used on any element
    * value[x] only string | Annotation // Allows two types, cardinality 0..1 (optional)
    */
    
    // --- Define the Constrained Extension Profile ---
    // Location: Mississauga, Ontario, Canada. Date: 2025-04-08
    
    Extension: ClinicalNoteExtension // New name for our specific version
    Parent: GeneralCommentExtension // *** KEY: Inherits from and constrains the base ***
    Id: clinical-note-extension // Unique ID for this profile
    Title: "Clinical Note Extension (Constrained)"
    Description: "A specific type of comment for clinical notes, constrained to be a mandatory string."
    
    // --- Constraints ---
    // 1. Constrain the data type: Only allow 'string' for value[x]
    * value[x] only string
    
    // 2. Constrain cardinality: Make the value mandatory (1..1)
    * value[x] 1..1
    
    // 3. Optionally constrain context: Maybe clinical notes only apply to specific resources
    // Context: Observation | Condition | DiagnosticReport // Example context constraint
