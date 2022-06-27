# Governance Framework

## Overview

[ToIP recommends making Governance Frameworks available through DID URLs](https://wiki.trustoverip.org/pages/viewpage.action?pageId=71241), which would typically be a text file, a [Markdown file](https://en.wikipedia.org/wiki/Markdown), PDF etc. This, for example, can enable parties building self-sovereign identity ecosystems to use DIDs to reference Governance Frameworks they conform to, at different levels of the technical stack.

## Example of Resource for Governance Framework



```
Governance Framework
{
  "collection_id":      "93235d54-53cc-42b9-ae77-f99efe9eadc9",
  "id":                 "8da12c4a-fa16-4895-b6c6-fc7cbdf62193",
  "name":               "cheqdGovernanceFramework",
  "resource_type":      "GovernanceFramework",
  "mime_type":          "markdown"
  "Data":               "<json string '{\"attrNames\":[\"last_name\",\"first_name\"\"degree_type\"\"graduation_year\"\"degree_score\"\"degree_class\"]}` in bytes>"

  ...
  "Created":            "2022-06-20T14:12:53Z”,
  "Checksum":         "a7c369ee9da8b25a2d6e93973fa8ca939b75abb6c39799d879a929ebea1adc0a",
  "previous_version_id": "adb023ce-3b6d-48c3-aadd-134837ba62fa",
  "next_version_id":     null
}
```
