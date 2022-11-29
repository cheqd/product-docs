# Trust Registry

## Overview

Trust Registries are a **vital component** of trusted ecosystems in Self-Sovereign Identity. They list and detail which parties are authorised to issue Verifiable Credentials for a particular purpose.&#x20;

On cheqd, Trust Registries are created as resources on-ledger, associated with their own DID and DID Document for authorisation and delegation of the management of the Trust Registry.&#x20;

Trust Registries MAY be referenced in Governance Files to tie a Trust Registry into a particular Governance ecosystem.&#x20;

## Creating a Trust Registry

```json
{
    "@context": "https://docs.cheqd.io/identity", 
    "id": "did:cheqd:testnet:b68c46ab-b821-4b03-aecb-6ce6c659de83/resources/ea9d4ac2-5d85-4240-801d-699be065c71c",
    "participants": [
        {
            "name": "National Museum",
            "type": "Issuer",
            "id": "did:cheqd:testnet:43cb8f4c-41ea-4141-800e-48dd5707b7bb",
            "describe" {
                "label": "National Museum",
                "sublabel": null,
                "website": "gatf.nationalmuseum.com",
                "email": "admin@gatf.nationalmuseum.com"
            }
        },
        {
            "name": "Science Museum",
            "type": "Issuer",
            "id": "did:cheqd:testnet:eb67c238-c4e4-4abc-8b00-b095db954d3a",
            "describe" {
                "label": "Science Museum",
                "sublabel": "GATF Science Museum",
                "website": "gatf.sciencemuseum.com",
                "email": "admin@gatf.sciencemuseum.com"
            }
        },
        {
            "name": "Natural History Museum",
            "type": "Issuer",
            "id": "did:cheqd:testnet:1953df92-8ac8-4403-9ce1-b00a64e845a9",
            "describe" {
                "label": "Natural History Museum",
                "sublabel": "Museum of Natural History",
                "website": "gatf.naturalhistorymuseum.com",
                "email": "admin@gatf.naturalhistorymuseum.com" 
            }
        }
    ]
}       
        
```
