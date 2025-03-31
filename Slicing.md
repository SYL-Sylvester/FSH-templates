# Slicing in FSH

## Example AllergyIntolerance Profile
    
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
