# JSON-LD

JSON-LD is a lightweight [Linked Data format](https://en.wikipedia.org/wiki/Linked\_data). It is easy for humans to read and write. It is based on the already successful JSON format and provides a way to help JSON data interoperate at Web-scale.

[Linked Data](https://en.wikipedia.org/wiki/Linked\_data) empowers people that publish and use information on the Web. It is a way to create a network of standards-based, machine-readable data across Web sites. It allows an application to start at one piece of Linked Data, and follow embedded links to other pieces of Linked Data that are hosted on different sites across the Web.

The main difference between JSON-LD credentials and JSON credentials is the inclusion of a @`context` field within the credential payload. In JSON-LD, the `@context` [property](https://www.w3.org/TR/vc-data-model/#dfn-property) can also be used to communicate other details, such as datatype information, language information, transformation rules, and so on, which are beyond the needs of this specification, but might be useful in the future or to related work.&#x20;

```
EXAMPLE from VC Data Model: Usage of the @context property
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://www.w3.org/2018/credentials/examples/v1"
  ],
  "id": "http://example.edu/credentials/58473",
  "type": ["VerifiableCredential", "AlumniCredential"],
  "issuer": "https://example.edu/issuers/565049",
  "issuanceDate": "2020-05-01T06:09:10Z",
  "credentialSubject": {
    "id": "did:cheqd:c822493c-9728-4279-bc88-a03904fff8b2",
    "alumniOf": {
      "id": "did:cheqd:66694a94-b6b3-4731-ab65-dc80bb24e58d",
      "name": [{
        "value": "cheqd University",
        "lang": "en"
      }, {
        "value": "cheqd d'Université",
        "lang": "fr"
      }]
    }
  },
  "proof": {  }
}
```

## cheqd JSON-LD implementation

cheqd's work on JSON-LD is in progress. _cheq_ back here soon to get more information about how cheqd is supporting this Credential type.&#x20;

