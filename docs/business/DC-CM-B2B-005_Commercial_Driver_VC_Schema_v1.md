# DC-CM-B2B-005 Commercial Driver VC Schema v1

**DC-CM-B2B-005**
**D-CENTRAL GROUP**
**CivicMesh Inc.**
Commercial Driver Verifiable Credential Schema


| Document ID | DC-CM-B2B-005 |
|---|---|
| Version | 1.0 |
| Status | Draft — SR&ED: novel VC schema for commercial platform integration |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Moderate — novel VC schema design for commercial platform credentialing within enforcement ecosystem |
| Related Documents | DC-CM-B2B-001 | DC-CM-VC-001 | DC-CM-DID-001 | DC-CM-B2B-003 | DC-CM-CHAIN-001 |




| Executive Summary
The Commercial Driver Verifiable Credential (CDVC) extends the CivicMesh W3C VC schema registry to support commercial platform drivers. The critical design distinction from the civilian CCSC membership credential: the CDVC is issued by the platform's DID (Uber Canada's DID, Lyft Canada's DID) — not by D-Central Group or CCSC. This makes the credential portable across platforms, revocable by the platform when a driver deactivates, and cryptographically distinguishable from civilian credentials in the evidence pipeline. A driver who drives for both Uber and Lyft holds two CDVCs — one from each platform — both linked to the same personal DID and CCSC membership. |
|---|


# 1. Credential Schema — Full Specification


| {
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://schema.civicmesh.ca/v1/commercial-driver"
  ],
  "type": ["VerifiableCredential", "CivicMeshCommercialDriverCredential"],
  "issuer": "did:ethr:polygon:0x...UberCanada",
  "issuanceDate": "2026-05-25T00:00:00Z",
  "expirationDate": "2027-05-25T00:00:00Z",
  "credentialSubject": {
    "id": "did:ethr:polygon:0x...DriverPersonalDID",
    "platform": "Uber",
    "platformRegion": "Ontario-Canada",
    "driverStatus": "active",
    "vehicleDIDs": [
      "did:key:z6Mk...Node1DID",
      "did:key:z6Mk...Node2DID"
    ],
    "revenueShareRecipient": "did:ethr:polygon:0x...CCScPatronageWallet",
    "insuranceDataShareConsent": true,
    "insurerDID": "did:ethr:polygon:0x...IntactInsurance",
    "tripContextConsent": true,
    "citationRevenueOptIn": true,
    "driverCertification": {
      "trackCompleted": "CivicMesh-Rideshare-L1",
      "completedAt": "2026-05-20T00:00:00Z",
      "issuedBy": "did:web:training.civicmesh.ca"
    },
    "ccscMemberDID": "did:ethr:polygon:0x...CCScMemberCredential",
    "platformDriverId": "sha256:hashed-platform-id"
  },
  "proof": {
    "type": "EthereumEip712Signature2021",
    "created": "2026-05-25T00:00:00Z",
    "verificationMethod": "did:ethr:polygon:0x...UberCanada#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "0x..."
  }
} |
|---|


# 2. Field-by-Field Privacy Analysis


| Field | Contains PII? | Privacy Protection | Who Can Read |
|---|---|---|---|
| id (driver DID) | No — pseudonymous DID | DID does not contain name, address, or phone number. Mapping to real identity held only by driver. | Anyone — DIDs are public |
| platform | No | Non-identifying — just the platform name | Anyone |
| driverStatus | No | Binary active/inactive | Anyone — used for credential verification |
| vehicleDIDs | No — node DIDs only | Node DIDs do not contain vehicle plate or VIN in the DID itself. Mapping held separately. | Anyone — used for evidence chain verification |
| revenueShareRecipient | No — wallet DID only | Patronage wallet DID not linked to real identity without driver consent | Anyone — used for revenue routing |
| insurerDID | No — insurer DID only | Reveals which insurer the driver uses — considered non-sensitive for this purpose | Anyone — needed for insurance data routing |
| platformDriverId | Hashed only | SHA-256 hash of Uber's internal driver ID. Non-reversible without Uber's salt. Used for revenue routing only — never for identity lookups by CivicMesh. | CivicMesh revenue routing system only |
| tripContextConsent | No | Boolean flag | Anyone — determines evidence package enrichment |


# 3. Credential Lifecycle


| Issuance | Driver completes opt-in in Member App. Platform API confirms active driver status. Platform's DID (Uber Canada) issues credential to driver's personal DID. |
|---|---|
| Active Status | Credential is valid while driver maintains active platform status AND annual renewal fee is current (if applicable). Platform API checks driver status on each credential issuance/renewal. |
| Revocation Triggers | Driver deactivated on platform (voluntary or involuntary), driver requests opt-out in Member App, credential expires without renewal, driver's CCSC membership lapses. Revocation logged on Polygon StatusList2021 within 60 seconds. |
| Driver Deactivation by Platform | If Uber deactivates a driver, their CDVC is revoked — future evidence packages will no longer carry commercial context. However, the driver's personal CCSC membership credential and accumulated patronage are NOT affected. The cooperative membership is independent of the platform relationship. |
| Multi-Platform Drivers | A driver who operates on both Uber and Lyft holds two CDVCs — one from each platform issuer. Both link to the same personal DID and CCSC wallet. Citation revenue from each platform's nodes attributed correctly to the single CCSC patronage account. |


# 4. Trust Registry Integration


| Platform as Authorized Issuer | Uber Canada DID and Lyft Canada DID must be registered in the CivicMesh Trust Registry as authorized issuers of CivicMeshCommercialDriverCredential type before any driver credentials are issued. Registration requires Platform API Agreement to be executed. |
|---|---|
| Issuer Revocation | If CivicMesh terminates the Platform API Agreement with Uber, Uber's issuer authorization is revoked from the Trust Registry. All subsequently-verified CDVCs issued by Uber's DID will fail — existing evidence packages already verified remain valid. |
| Verification in Officer Portal | Officer portal auto-verifies the full credential chain including CDVC when reviewing evidence from commercial vehicle nodes. The platform badge (Uber/Lyft) appears as additional context — does not affect the enforcement decision. |


# 5. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

