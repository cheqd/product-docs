# Data model

## Process for creating a trust chain

1. Create `did:cheqd` DID for Root TAO
2. Establish root of trust, by:
   1. Associating Root TAO DID with X.509 certificate; or
   2. Associating Root TAO DID with cheqd Validator
3. Create `did:cheqd` DIDs for TAOs or TIs within the ecosystem
4. Create body of Verifiable Accreditation, specifying:
   1. The `did:cheqd` DID of the subject organisation that the Accreditation is being issued to
   2. A UUID as a reservedAttributeId which aligns with the resourceId of the DID-Linked Resource
5. Encode Verifiable Accreditation as a hexidecimal and as a base64 encoded value
6. Compile payload file for writing Verifiable Accreditation as a DID-Linked Resource
7. Publish transaction as a DID-Linked Resource, using the same UUID for the `resourceId` as the value specified in the `reservedAttributeId`

#### Resource Payload File fields

| Field      | Description                                                                                                  | Required |
| ---------- | ------------------------------------------------------------------------------------------------------------ | -------- |
| did        | DID of the DID Controller publishing the DID-Linked Resource                                                 | Yes      |
| hash       | Hash of the Verifiable Accreditation as a Base64 encoded value                                               | Yes      |
| body       | Body of the Verifiable Accreditation as a hexidecimal string                                                 | Yes      |
| type       | Type of Resource being created, defaults to `VerifiableAccreditation`                                        | Yes      |
| issuerType | <p>Type of Issuer that is issuing this Verifiable Accreditation, can be:<br>"RootTAO", "TAO" or "SubTAO"</p> | Yes      |
| tao        | DID of the TAO that accredited the Issuer                                                                    | Yes      |
| rootTao    | DID of the root of trust that accredited the TAO and the issuer                                              | Yes      |
| revoked    | Boolean value indicating whether the Verifiable Accreditation is revoked or not                              | Yes      |
| Encrypted  | Boolean value indicating whether the Verifiable Accreditation is encrypted or not                            | No       |

#### Example payload file

````json
```json
{
  "attribute": {
    "did": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6",
    "hash": "bcdb6bc952c8c897ca1e605fce25f82604c76c16d479770014b7b262b93c0250",
    "body": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImRpZDplYnNpOnpqSFpqSjRTeTdyOTJCeFh6RkdzN3FEI1Q2aVBNVy1rOE80dXdaaWQyOUd3TGUtTmpnNDBFNmpOVDdoZExwSjNaU2cifQ.eyJpYXQiOjE2OTE1OTA3NjgsImp0aSI6InVybjp1dWlkOmZmMzFhZmM4LTFmNGUtNGMxMy04ZjdhLTM0MDQ0ODZlYWRlYSIsIm5iZiI6MTY5MTU5MDc2OCwiZXhwIjoxNzIzMTI2NzY4LCJzdWIiOiJkaWQ6ZWJzaTp6akhaako0U3k3cjkyQnhYekZHczdxRCIsInZjIjp7IkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIl0sImlkIjoidXJuOnV1aWQ6ZmYzMWFmYzgtMWY0ZS00YzEzLThmN2EtMzQwNDQ4NmVhZGVhIiwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlZlcmlmaWFibGVBdHRlc3RhdGlvbiIsIlZlcmlmaWFibGVBY2NyZWRpdGF0aW9uIiwiVmVyaWZpYWJsZUFjY3JlZGl0YXRpb25Ub0F0dGVzdCJdLCJpc3N1ZXIiOiJkaWQ6ZWJzaTp6akhaako0U3k3cjkyQnhYekZHczdxRCIsImlzc3VhbmNlRGF0ZSI6IjIwMjMtMDgtMDlUMTQ6MTk6MjhaIiwiaXNzdWVkIjoiMjAyMy0wOC0wOVQxNDoxOToyOFoiLCJ2YWxpZEZyb20iOiIyMDIzLTA4LTA5VDE0OjE5OjI4WiIsImV4cGlyYXRpb25EYXRlIjoiMjAyNC0wOC0wOFQxNDoxOToyOFoiLCJjcmVkZW50aWFsU3ViamVjdCI6eyJpZCI6ImRpZDplYnNpOnpqSFpqSjRTeTdyOTJCeFh6RkdzN3FEIiwiYWNjcmVkaXRlZEZvciI6W3sic2NoZW1hSWQiOiJodHRwczovL2FwaS1jb25mb3JtYW5jZS5lYnNpLmV1L3RydXN0ZWQtc2NoZW1hcy1yZWdpc3RyeS92My9zY2hlbWFzL3ozTWdVRlVrYjcyMnVxNHgzZHY1eUFKbW5ObXpERmVLNVVDOHg4M1FvZUxKTSIsInR5cGVzIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF0dGVzdGF0aW9uIiwiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIl19XSwicmVzZXJ2ZWRBdHRyaWJ1dGVJZCI6IjRlYzcwN2YxZDMxZjBlOTg5OTZlYzY2MmQ3ZDk4MjA4NzRiNGRkMjNjZGMwM2Y3OGEyYWIxNDgzYzM5NjlmM2EifSwiY3JlZGVudGlhbFNjaGVtYSI6eyJpZCI6Imh0dHBzOi8vYXBpLWNvbmZvcm1hbmNlLmVic2kuZXUvdHJ1c3RlZC1zY2hlbWFzLXJlZ2lzdHJ5L3YzL3NjaGVtYXMvempWRk52YkVCUEFyM2E3MjREdHRpb1pwZ1ptTnI3NUJCdFJ6WnFrN3BrRGUiLCJ0eXBlIjoiRnVsbEpzb25TY2hlbWFWYWxpZGF0b3IyMDIxIn19LCJpc3MiOiJkaWQ6ZWJzaTp6akhaako0U3k3cjkyQnhYekZHczdxRCJ9.o6olFmrYFpWy36N2Jnz1DHKBkAcSt3vN7RzSU-j6HGyhzHVZAEG7W20EtZDw4LVvKE0GzH9burBAdfJkAWR_6w",
  },
  "metadata": {
    "type": "VerifiableAccreditation",
    "issuerType": "RootTAO",
    "tao": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6",
    "rootTao": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6",
    "revoked": false,
    "encrypted": false
  }
}
```
````

