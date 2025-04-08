# Slicing Templates

## Template 1: Slicing by Value
This template slices a repeating element based on the specific value of one of its child elements.

    Profile: [Profile Name]
    Parent: [Base Resource or Type]
    Id: [profile-id]
    Title: "[Human-Readable Title]"
    Description: "[Description of the profile with slicing.]"

    * [repeatingElement] ^slicing.discriminator.type = #value
    * [repeatingElement] ^slicing.discriminator.path = "[path.to.discriminating.element]"
    * [repeatingElement] ^slicing.rules = #[closed|open|openAtEnd]

    * [repeatingElement][sliceName1] MS where [path.to.discriminating.element] = '[expectedValue1]'
    * [repeatingElement][sliceName2] 0..1 where [path.to.discriminating.element] = '[expectedValue2]'
    // Add more slices as needed

### Explanation:

* [repeatingElement]: The name of the repeating element you are slicing (e.g., telecom, address, component).
* ^slicing.discriminator.type = #value: Specifies that the slicing is based on the value of an element.
* ^slicing.discriminator.path = "[path.to.discriminating.element]": The FHIR path to the element whose value will determine the slice (e.g., system for telecom, use for address, code.coding.code for component).
* ^slicing.rules = #[closed|open|openAtEnd]: Defines whether only the defined slices are allowed (#closed), or if additional unsliced or differently sliced elements are permitted (#open or #openAtEnd).
* [repeatingElement][sliceName1]: Defines the first slice with a unique name (e.g., phone, home, systolic).
* MS or cardinality: Specifies the cardinality of this specific slice.
* where [path.to.discriminating.element] = '[expectedValue1]': The constraint that determines which instances of the repeating element belong to this slice. Replace [expectedValue1] with the specific value to match (e.g., 'phone', 'home', '8462-4').

### Example: Slicing Patient.telecom by value:

    Profile: PatientContactDetails
    Parent: Patient
    Id: patient-contact-details
    Title: "Patient Contact Details"
    Description: "Patient profile with sliced telecom for phone and email."

    * telecom ^slicing.discriminator.type = #value
    * telecom ^slicing.discriminator.path = "system"
    * telecom ^slicing.rules = #open

    * telecom[phone] MS where system = 'phone'
    * telecom[email] 0..1 where system = 'email'


## Template 2: Slicing by Type
This template slices a repeating element based on the data type of its content (useful for polymorphic elements).

    Profile: [Profile Name]
    Parent: [Base Resource or Type]
    Id: [profile-id]
    Title: "[Human-Readable Title]"
    Description: "[Description of the profile with slicing.]"

    * [repeatingElement] ^slicing.discriminator.type = #type
    * [repeatingElement] ^slicing.discriminator.path = "$this" // Usually "$this" for type-based slicing
    * [repeatingElement] ^slicing.rules = #[closed|open|openAtEnd]

    * [repeatingElement][sliceName1] MS { [DataType1] }
    * [repeatingElement][sliceName2] 0..1 { [DataType2] }
    // Add more slices as needed

### Explanation:

* ^slicing.discriminator.type = #type: Specifies that the slicing is based on the data type.
* ^slicing.discriminator.path = "$this": Indicates that the discriminator is the type of the element itself.
* { [DataType1] }: Specifies the data type for this slice (e.g., { Quantity }, { CodeableConcept }, { Reference(Observation) }).    
    
### Example: Slicing Observation.value[x] by data type:

    Profile: ObservationWithValueTypes
    Parent: Observation
    Id: observation-with-value-types
    Title: "Observation with Value Type Slices"
    Description: "Observation profile slicing value[x] by data type."

    * value[x] ^slicing.discriminator.type = #type
    * value[x] ^slicing.discriminator.path = "$this"
    * value[x] ^slicing.rules = #open

    * value[x][valueQuantity] MS { Quantity }
    * value[x][valueCodeableConcept] 0..1 { CodeableConcept }
    * value[x][valueString] 0..1 { string }


## Template 3: Slicing by Existence of an Extension
This template slices a repeating element based on whether a specific extension is present.  

    Profile: [Profile Name]
    Parent: [Base Resource or Type]
    Id: [profile-id]
    Title: "[Human-Readable Title]"
    Description: "[Description of the profile with slicing.]"

    * [repeatingElement] ^slicing.discriminator.type = #exists
    * [repeatingElement] ^slicing.discriminator.path = "extension('[extension-url]')"
    * [repeatingElement] ^slicing.rules = #[closed|open|openAtEnd]

    * [repeatingElement][withExtension] MS where extension contains '[extension-url]'
    * [repeatingElement][withoutExtension] 0..1 where extension does not contain '[extension-url]'

### Explanation:

* ^slicing.discriminator.type = #exists: Specifies that the slicing is based on the presence or absence of an element or extension.
* ^slicing.discriminator.path = "extension('[extension-url]')": Specifies the extension URL to check for.
* where extension contains '[extension-url]': Constraint for the slice where the extension is present.
* where extension does not contain '[extension-url]': Constraint for the slice where the extension is absent.

### Example: Slicing Observation.related based on the presence of a "reason" extension:

    Profile: ObservationRelatedWithReason
    Parent: Observation
    Id: observation-related-with-reason
    Title: "Observation.related with Reason Slice"
    Description: "Observation profile slicing related by the presence of a reason extension."

    * related ^slicing.discriminator.type = #exists
    * related ^slicing.discriminator.path = "extension('http://example.org/fhir/StructureDefinition/related-reason')"
    * related ^slicing.rules = #open

    * related[withReason] MS where extension contains 'http://example.org/fhir/StructureDefinition/related-reason'
    * related[withoutReason] 0..* where extension does not contain 'http://example.org/fhir/StructureDefinition/related-reason'


## Template 4: Slicing with a Pattern
This template slices a repeating element based on a specific pattern that a child element must match.    

    Profile: [Profile Name]
    Parent: [Base Resource or Type]
    Id: [profile-id]
    Title: "[Human-Readable Title]"
    Description: "[Description of the profile with slicing.]"

    * [repeatingElement] ^slicing.discriminator.type = #pattern
    * [repeatingElement] ^slicing.discriminator.path = "[path.to.discriminating.element]"
    * [repeatingElement] ^slicing.rules = #[closed|open|openAtEnd]

    * [repeatingElement][sliceName1] MS where [path.to.discriminating.element] = [CodeableConcept or Coding with specific values]
    * [repeatingElement][sliceName2] 0..1 where [path.to.discriminating.element] = [CodeableConcept or Coding with other values]

### Explanation:

* ^slicing.discriminator.type = #pattern: Specifies that the slicing is based on a pattern.
* where [path.to.discriminating.element] = [CodeableConcept or Coding with specific values]: The constraint uses a specific CodeableConcept or Coding to define the pattern for the slice.    

### Example: Slicing Observation.component based on the code:

    Profile: BloodPressureObservation
    Parent: Observation
    Id: blood-pressure-observation
    Title: "Blood Pressure Observation Profile"
    Description: "Observation profile slicing component for systolic and diastolic blood pressure."

    * component ^slicing.discriminator.type = #pattern
    * component ^slicing.discriminator.path = "code"
    * component ^slicing.rules = #closed

    * component[systolic] MS where code = http://loinc.org#8462-4 "Systolic blood pressure"
    * component[diastolic] MS where code = http://loinc.org#8480-2 "Diastolic blood pressure"


## Template 5: Open Slicing
This template demonstrates how to define slicing that allows for additional unsliced or differently sliced elements.

    Profile: [Profile Name]
    Parent: [Base Resource or Type]
    Id: [profile-id]
    Title: "[Human-Readable Title]"
    Description: "[Description of the profile with open slicing.]"

    * [repeatingElement] ^slicing.discriminator.type = #value // Or other discriminator type
    * [repeatingElement] ^slicing.discriminator.path = "[path.to.discriminating.element]"
    * [repeatingElement] ^slicing.rules = #open

    * [repeatingElement][sliceName1] MS where [path.to.discriminating.element] = '[expectedValue1]'
    // Other slices can be defined here

### Explanation:

* ^slicing.rules = #open: This is the key element for open slicing. It indicates that while you are defining specific slices, instances can also include other occurrences of the [repeatingElement] that don't match your defined slices or are not sliced at all.


## Template 6: Closed Slicing
This template demonstrates how to define slicing that strictly limits the repeating element to only the defined slices.

    Profile: [Profile Name]
    Parent: [Base Resource or Type]
    Id: [profile-id]
    Title: "[Human-Readable Title]"
    Description: "[Description of the profile with closed slicing.]"

    * [repeatingElement] ^slicing.discriminator.type = #value // Or other discriminator type
    * [repeatingElement] ^slicing.discriminator.path = "[path.to.discriminating.element]"
    * [repeatingElement] ^slicing.rules = #closed

    * [repeatingElement][sliceName1] MS where [path.to.discriminating.element] = '[expectedValue1]'
    * [repeatingElement][sliceName2] 0..1 where [path.to.discriminating.element] = '[expectedValue2]'
    // Only these defined slices are allowed

### Explanation:

* ^slicing.rules = #closed: This is the key element for closed slicing. It indicates that instances of the [repeatingElement] must conform to one of the defined slices. No other unsliced occurrences or differently sliced elements are allowed.    



# Example AllergyIntolerance Profile
    
    `Profile: AllergyToPeanutProfile
    Parent: AllergyIntolerance
    Id: allergy-to-peanut
    Title: "AllergyIntolerance Profile for Peanut Allergy"
    Description: "A profile for recording a patient's allergy specifically to peanuts, including reaction details with a slice for severity."
    * patient MS
    * substance MS
    * substance.coding MS
    * substance.coding only AllergySubstanceCS#peanut "Must be coded as Peanut from our Allergy Substance CodeSystem"
    * reaction 1..* MS
    * reaction ^slicing.discriminator.type = #exists
    * reaction ^slicing.discriminator.path = "extension(AllergyReactionSeverityExt)"
    * reaction ^slicing.rules = #open
    * reaction[with-severity] MS where extension contains AllergyReactionSeverityExt`

### Explanation of example
* ^slicing.discriminator.type = #exists: This indicates that the discriminator for the slice is based on whether a specific element or extension exists.
* ^slicing.discriminator.path = "extension(AllergyReactionSeverityExt)": This specifies that we are discriminating based on the presence of our AllergyReactionSeverityExt extension.
* ^slicing.rules = #open: This means that additional slices or unsliced reaction elements are allowed.
* reaction[with-severity] MS where extension contains AllergyReactionSeverityExt: This creates a slice named "with-severity". It is Must Support and has a where constraint that requires the reaction to contain our AllergyReactionSeverityExt extension.

## Explanation of the slicing in FSH

Slicing in FSH (and FHIR) allows you to define specific subsets or variations of repeating elements within a resource or profile. 
It enables you to enforce different constraints or expectations on different occurrences of the same element.

Here's a breakdown of the key aspects of slicing in FSH:

* ^slicing.discriminator.type: This specifies how to distinguish between the different slices. Common types include:
   * #value: Discriminates based on the actual value of a specific element.
   * #exists: Discriminates based on whether a specific element or extension is present.
   * #pattern: Discriminates based on a pattern that the element must match.
   * #type: Discriminates based on the data type of the element.
   * #profile: Discriminates based on the profile that the referenced resource conforms to.
 
* ^slicing.discriminator.path: This specifies the path to the element whose value, existence, or type will be used to determine which slice an instance belongs to.
  For extensions, you use the extension() function with the URL of the extensiion.
  
* ^slicing.rules: This defines how the slices are handled:
    * #closed: Only the defined slices are allowed. No other occurrences of the element are permitted.
    * #open: The defined slices are expected, but other unsliced occurrences or additional slices are also allowed.
    * #openAtEnd: The defined slices are expected, and additional unsliced occurrences are allowed at the end of the repeating element.

* [sliceName]: You define a name for each slice within the repeating element (e.g., reaction[with-severity]).
This name is used to refer to that specific slice and apply constraints to it.
  
* where constraint: You can use a where constraint within a slice definition to further refine which instances of the repeating element belong to that slice.


### In our example, we used #exists slicing to create a slice specifically for reactions that have the AllergyReactionSeverityExt extension. This allows us to make the presence of that extension a requirement for that particular slice of the reaction element.
