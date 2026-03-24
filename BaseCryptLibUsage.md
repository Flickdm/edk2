# BaseCryptLib Usage in EDK2

This document identifies all locations in the EDK2 codebase that consume cryptographic functionality from `BaseCryptLib` (and its variants).

---

## Summary

| Metric | Count |
|--------|-------|
| `.inf` files using BaseCryptLib | ~68 |
| `.c`/`.h` files with BaseCryptLib includes | 120+ |
| `.dsc` files referencing BaseCryptLib | 24 |
| Major functional areas | 11 |

---

## 1. Secure Boot & Digital Signature Verification

UEFI Secure Boot relies on BaseCryptLib for PKCS#7 and RSA signature verification against the platform key database (PK, KEK, db, dbx).

| Module | INF Path | Purpose |
|--------|----------|---------|
| SecureBootConfigDxe | `SecurityPkg/VariableAuthenticated/SecureBootConfigDxe/SecureBootConfigDxe.inf` | Secure Boot configuration UI |
| DxeImageVerificationLib | `SecurityPkg/Library/DxeImageVerificationLib/DxeImageVerificationLib.inf` | PE/COFF binary signature (PKCS#7) verification |
| AuthVariableLib | `SecurityPkg/Library/AuthVariableLib/AuthVariableLib.inf` | Authenticated UEFI variable management |
| SecureBootVariableProvisionLib | `SecurityPkg/Library/SecureBootVariableProvisionLib/SecureBootVariableProvisionLib.inf` | Initializes KEK, DB, DBX variables |
| Pkcs7VerifyDxe | `SecurityPkg/Pkcs7Verify/Pkcs7VerifyDxe/Pkcs7VerifyDxe.inf` | PKCS#7 message verification driver |

---

## 2. Firmware Capsule Update

Capsule update authentication ensures only legitimately signed firmware images are applied.

| Module | INF Path | Purpose |
|--------|----------|---------|
| FmpDxe | `FmpDevicePkg/FmpDxe/FmpDxe.inf` | Firmware Management Protocol with signing |
| FmpDxeLib | `FmpDevicePkg/FmpDxe/FmpDxeLib.inf` | FMP support library |
| FmpAuthenticationLibPkcs7 | `SecurityPkg/Library/FmpAuthenticationLibPkcs7/FmpAuthenticationLibPkcs7.inf` | PKCS#7-based capsule authentication |
| FmpAuthenticationLibRsa2048Sha256 | `SecurityPkg/Library/FmpAuthenticationLibRsa2048Sha256/FmpAuthenticationLibRsa2048Sha256.inf` | RSA-2048 + SHA-256 capsule authentication |
| PeiRsa2048Sha256GuidedSectionExtractLib | `SecurityPkg/Library/PeiRsa2048Sha256GuidedSectionExtractLib/` | PEI signed section extraction |
| DxeRsa2048Sha256GuidedSectionExtractLib | `SecurityPkg/Library/DxeRsa2048Sha256GuidedSectionExtractLib/` | DXE signed section extraction |

---

## 3. BIOS / HDD Passwords

| Module | INF Path | Purpose |
|--------|----------|---------|
| HddPasswordDxe | `SecurityPkg/HddPassword/HddPasswordDxe.inf` | ATA Security password hashing via SHA-256 |

---

## 4. TLS & Network Security

TLS 1.2 support for network protocols (HTTPS, iSCSI, etc.) is backed by BaseCryptLib.

| Module | INF Path | Purpose |
|--------|----------|---------|
| TlsDxe | `NetworkPkg/TlsDxe/TlsDxe.inf` | TLS 1.2 protocol driver |
| TlsLib | `CryptoPkg/Library/TlsLib/TlsLib.inf` | Underlying TLS library |
| TlsLibNull | `CryptoPkg/Library/TlsLibNull/TlsLibNull.inf` | Null stub (TLS disabled) |
| IScsiDxe | `NetworkPkg/IScsiDxe/IScsiDxe.inf` | iSCSI driver with optional TLS encryption |

---

## 5. Firmware Integrity Measurement & TPM

Boot measurements are hashed and extended into TPM PCRs to provide firmware integrity evidence.

| Module | INF Path | Purpose |
|--------|----------|---------|
| TcgPei | `SecurityPkg/Tcg/TcgPei/TcgPei.inf` | TPM 1.2 PEI measurement |
| TcgDxe | `SecurityPkg/Tcg/TcgDxe/TcgDxe.inf` | TPM 1.2 DXE measurement |
| DxeTpmMeasureBootLib | `SecurityPkg/Library/DxeTpmMeasureBootLib/` | Measures boot objects into TPM |
| DxeTpm2MeasureBootLib | `SecurityPkg/Library/DxeTpm2MeasureBootLib/` | TPM 2.0 variant |
| SecTpmMeasurementLib | `SecurityPkg/Library/SecTpmMeasurementLib/` | Measurement for TEE environments |
| FvReportPei | `SecurityPkg/FvReportPei/` | Cryptographic firmware volume measurement |
| TpmCommLib | `SecurityPkg/Library/TpmCommLib/` | Low-level TPM protocol communication |

---

## 6. Cryptographic Hash Services

| Module | INF Path | Purpose |
|--------|----------|---------|
| Hash2DxeCrypto | `SecurityPkg/Hash2DxeCrypto/Hash2DxeCrypto.inf` | DXE driver exposing Hash protocol |
| HashInstanceLibSha1 | `SecurityPkg/Library/HashInstanceLibSha1/` | SHA-1 hash instance |
| HashInstanceLibSha256 | `SecurityPkg/Library/HashInstanceLibSha256/` | SHA-256 hash instance |
| HashInstanceLibSha384 | `SecurityPkg/Library/HashInstanceLibSha384/` | SHA-384 hash instance |
| HashInstanceLibSha512 | `SecurityPkg/Library/HashInstanceLibSha512/` | SHA-512 hash instance |
| HashInstanceLibSm3 | `SecurityPkg/Library/HashInstanceLibSm3/` | SM3 hash instance (Chinese national standard) |
| BaseHashApiLib | `CryptoPkg/Library/BaseHashApiLib/BaseHashApiLib.inf` | Abstract multi-algorithm hash API |

---

## 7. Device Security (SPDM)

SPDM (Security Protocol and Data Model) authenticates PCIe and other devices at runtime.

| Module | INF Path | Purpose |
|--------|----------|---------|
| SpdmSecurityLib | `SecurityPkg/DeviceSecurity/SpdmSecurityLib/SpdmSecurityLib.inf` | SPDM integration library |
| SpdmCryptLib | `SecurityPkg/DeviceSecurity/SpdmLib/SpdmCryptLib/` | SPDM cryptographic functions |
| SpdmSecuredMessageLib | `SecurityPkg/DeviceSecurity/SpdmLib/SpdmSecuredMessageLib/` | SPDM secured message handling |
| CryptlibWrapper | `SecurityPkg/DeviceSecurity/OsStub/CryptlibWrapper/CryptlibWrapper.inf` | Wraps BaseCryptLib for SPDM OS-stub use |

---

## 8. Trusted Execution Environments (TEE)

### Intel TDX (Trusted Domain Extensions)

| Module | INF Path | Purpose |
|--------|----------|---------|
| TdxHelperLib (SEC) | `OvmfPkg/IntelTdx/TdxHelperLib/SecTdxHelperLib.inf` | TDX measurement and attestation |
| DxeTdxMeasurementLib | `OvmfPkg/IntelTdx/TdxMeasurementLib/DxeTdxMeasurementLib.inf` | Cryptographic measurement reporting |
| TdTcg2Pei | `OvmfPkg/Tcg/TdTcg2Pei/TdTcg2Pei.inf` | TCG2/TPM 2.0 for TDX |

### AMD SEV (Secure Encrypted Virtualization)

| Module | INF Path | Purpose |
|--------|----------|---------|
| BlobVerifierLibSevHashes | `OvmfPkg/AmdSev/BlobVerifierLibSevHashes/BlobVerifierLibSevHashes.inf` | SEV blob verification via hash checking |

---

## 9. CryptoPkg — Library Implementations & Drivers

### 9.1 Crypto Protocol/PPI Drivers

These drivers expose BaseCryptLib functionality as a protocol/PPI so other modules can link against crypto dynamically rather than statically.

| Module | INF Path | Phase |
|--------|----------|-------|
| CryptoDxe | `CryptoPkg/Driver/CryptoDxe.inf` | DXE |
| CryptoSmm | `CryptoPkg/Driver/CryptoSmm.inf` | SMM |
| CryptoStandaloneMm | `CryptoPkg/Driver/CryptoStandaloneMm.inf` | Standalone MM |
| CryptoPei | `CryptoPkg/Driver/CryptoPei.inf` | PEI |

### 9.2 BaseCryptLib — OpenSSL Implementation (Default)

| Variant | Path | Execution Phase |
|---------|------|-----------------|
| BaseCryptLib (DXE) | `CryptoPkg/Library/BaseCryptLib/` | DXE |
| RuntimeCryptLib | `CryptoPkg/Library/BaseCryptLib/RuntimeCryptLib/` | UEFI Runtime |
| SmmCryptLib | `CryptoPkg/Library/BaseCryptLib/SmmCryptLib/` | SMM |
| PeiCryptLib | `CryptoPkg/Library/BaseCryptLib/PeiCryptLib/` | PEI |
| SecCryptLib | `CryptoPkg/Library/BaseCryptLib/SecCryptLib/` | SEC |
| UnitTestHostBaseCryptLib | `CryptoPkg/Library/BaseCryptLib/UnitTestHostBaseCryptLib/` | Host testing |

### 9.3 BaseCryptLibMbedTls — MbedTLS Alternative Implementation

Drop-in replacement for the OpenSSL-based implementation, selectable at compile time.

| Variant | Path | Execution Phase |
|---------|------|-----------------|
| BaseCryptLibMbedTls (DXE) | `CryptoPkg/Library/BaseCryptLibMbedTls/` | DXE |
| RuntimeCryptLib | `CryptoPkg/Library/BaseCryptLibMbedTls/RuntimeCryptLib/` | UEFI Runtime |
| SmmCryptLib | `CryptoPkg/Library/BaseCryptLibMbedTls/SmmCryptLib/` | SMM |
| PeiCryptLib | `CryptoPkg/Library/BaseCryptLibMbedTls/PeiCryptLib/` | PEI |

### 9.4 BaseCryptLibNull — Stub (No Crypto)

| Module | INF Path | Purpose |
|--------|----------|---------|
| BaseCryptLibNull | `CryptoPkg/Library/BaseCryptLibNull/BaseCryptLibNull.inf` | Stub for platforms that disable crypto |

### 9.5 BaseCryptLibOnProtocolPpi — Dynamic/Deferred Linking

Modules that use this variant defer all crypto calls to the Crypto protocol/PPI at runtime instead of statically embedding the crypto code.

| Variant | Path |
|---------|------|
| DxeCryptLib | `CryptoPkg/Library/BaseCryptLibOnProtocolPpi/DxeCryptLib.inf` |
| SmmCryptLib (OnProtocolPpi) | `CryptoPkg/Library/BaseCryptLibOnProtocolPpi/SmmCryptLib.inf` |
| PeiCryptLib (OnProtocolPpi) | `CryptoPkg/Library/BaseCryptLibOnProtocolPpi/PeiCryptLib.inf` |
| StandaloneMmCryptLib | `CryptoPkg/Library/BaseCryptLibOnProtocolPpi/StandaloneMmCryptLib.inf` |

---

## 10. Unit Tests

A comprehensive test suite exercises all BaseCryptLib functionality.

**Test INFs:**
- `CryptoPkg/Test/UnitTest/Library/BaseCryptLib/TestBaseCryptLibHost.inf`
- `CryptoPkg/Test/UnitTest/Library/BaseCryptLib/TestBaseCryptLibShell.inf`

**Test modules (20+ files):**

| Test File | Algorithms/APIs Covered |
|-----------|------------------------|
| `AeadAesGcmTests.c` | AES-GCM authenticated encryption |
| `AuthenticodeTests.c` | Authenticode / PE signing |
| `BlockCipherTests.c` | AES block cipher modes |
| `BnTests.c` | Big number arithmetic |
| `DhTests.c` | Diffie-Hellman key exchange |
| `EcTests.c` | Elliptic curve cryptography |
| `HashTests.c` | SHA-1/256/384/512, SM3 |
| `HkdfTests.c` | HMAC-based key derivation |
| `HmacTests.c` | HMAC-SHA256/384/512 |
| `OaepEncryptTests.c` | RSA-OAEP encryption |
| `ParallelhashTests.c` | Parallel hash |
| `Pkcs5Pbkdf2Tests.c` | PBKDF2 key derivation |
| `Pkcs7SignTests.c` / `Pkcs7VerifyTests.c` | PKCS#7 sign/verify |
| `RandTests.c` | Random number generation |
| `RsaTests.c` | RSA key gen, sign, verify |
| `RsaPssTests.c` | RSA-PSS signature scheme |
| `TSTests.c` | Timestamp protocol |
| `X509Tests.c` | X.509 certificate parsing/validation |

---

## 11. Platform Integration (DSC Files)

Platforms and packages that wire BaseCryptLib into their build:

| DSC File | Notes |
|----------|-------|
| `CryptoPkg/CryptoPkg.dsc` | Core crypto package (OpenSSL) |
| `CryptoPkg/CryptoPkgMbedTls.dsc` | Core crypto package (MbedTLS) |
| `SecurityPkg/SecurityPkg.dsc` | Security package |
| `SignedCapsulePkg/SignedCapsulePkg.dsc` | Signed capsule update |
| `NetworkPkg/NetworkPkg.dsc` | Network stack with TLS |
| `FmpDevicePkg/FmpDevicePkg.dsc` | Firmware Management Protocol |
| `OvmfPkg/OvmfPkgX64.dsc` | OVMF x64 virtual machine firmware |
| `OvmfPkg/OvmfPkgIa32X64.dsc` | OVMF IA32/X64 |
| `OvmfPkg/OvmfXen.dsc` | Xen virtual machine |
| `OvmfPkg/CloudHv/CloudHvX64.dsc` | Cloud Hypervisor |
| `OvmfPkg/Bhyve/BhyveX64.dsc` | bhyve hypervisor |
| `OvmfPkg/AmdSev/AmdSevX64.dsc` | AMD SEV |
| `OvmfPkg/IntelTdx/IntelTdxX64.dsc` | Intel TDX |
| `EmulatorPkg/EmulatorPkg.dsc` | Emulator platform |
| `UefiPayloadPkg/UefiPayloadPkg.dsc` | UEFI payload |
| `TcgTpmPkg/TcgTpmPkg.dsc` | TCG TPM package |

---

## Key Observations

### Dual Crypto Backends
BaseCryptLib has two interchangeable implementations selectable at compile time:
- **OpenSSL** — the traditional default
- **MbedTLS** — a lighter-weight alternative added more recently

### Execution Phase Variants
Because EDK2 has distinct execution environments, BaseCryptLib is compiled separately for each phase to respect memory model and service availability constraints:

| Phase | Description |
|-------|-------------|
| SEC | First code to run; extremely constrained |
| PEI | Pre-EFI Initialization; limited services |
| DXE | Driver Execution Environment; full services |
| Runtime | After `ExitBootServices()`; very limited |
| SMM | System Management Mode; isolated |

### Linking Strategies

| Strategy | Mechanism | Use Case |
|----------|-----------|---------|
| Static | BaseCryptLib linked directly into module | Smallest modules, PEI/SEC |
| Dynamic (Protocol/PPI) | BaseCryptLibOnProtocolPpi calls Crypto driver | Reduces firmware size |
| Null stub | BaseCryptLibNull | Platforms with no crypto requirement |

### Most-Used Algorithms
- **RSA** (2048/3072/4096) — signing and verification
- **SHA-256** — primary hash (SHA-384/512 for higher security)
- **AES** (128/192/256) — symmetric encryption
- **X.509** — certificate chain handling
- **PKCS#7** — signed data format for capsules and Secure Boot
- **HMAC/HKDF** — keyed hashing and key derivation

---

## 12. Unused BaseCryptLib Functions

Of the 186 functions declared in `CryptoPkg/Include/Library/BaseCryptLib.h`, approximately **69 (37%)** have no call sites outside of CryptoPkg itself. They are grouped below by crypto family.

> **Note:** "Unused" means no EDK2 consumer module calls these functions. They may still be needed for platform-specific or out-of-tree consumers.

### Entire Algorithm Families — Zero Usage in EDK2

| Family | Unused Functions | Notes |
|--------|-----------------|-------|
| **MD5** | `Md5GetContextSize`, `Md5Init`, `Md5Update`, `Md5Final`, `Md5HashAll`, `Md5Duplicate` | Cryptographically broken; should not be used |
| **Camellia** | `CamelliaGetContextSize`, `CamelliaEncrypt`, `CamelliaDecrypt` | No EDK2 consumer requires this cipher |
| **DH (Diffie-Hellman)** | `DhNew`, `DhFree`, `DhGenerateParameter`, `DhSetParameter`, `DhGenerateKey`, `DhComputeKey` | Key exchange not used directly in EDK2 |

### Partially Unused Families

| Family | Unused Functions | Used Counterparts |
|--------|----------------|-------------------|
| **AES** | `AesGetContextSize`, `AesDecrypt`, `AesCbcEncrypt`, `AesCbcDecrypt` | AES-GCM and ECB modes are used |
| **SHA-1** | `Sha1Duplicate`, `Sha1HashAll` | Streaming API (`Init`/`Update`/`Final`) is used |
| **SHA-512** | `Sha512Duplicate`, `Sha512HashAll` | Streaming API is used |
| **SM3** | `Sm3Duplicate`, `Sm3HashAll` | Streaming API is used |
| **Hash Utilities** | `ParallelHash256HashAll` | — |
| **RSA** | `RsaCheckKey`, `RsaGenerateKey`, `RsaOaepEncrypt`, `RsaOaepDecrypt`, `RsaPssSign`, `RsaPssVerify` | Basic PKCS#1 v1.5 (`RsaPkcs1Sign`, `RsaPkcs1Verify`) is used |
| **Elliptic Curve** | `EcGroupInit`, `EcGroupGetCurve`, `EcGroupGetOrder`, `EcGetPubKey`, `EcPointEqual`, `EcPointInvert`, `EcPointIsAtInfinity`, `EcPointIsOnCurve`, `EcPointSetCompressedCoordinates` | High-level API (`EcDsaSign`, `EcDsaVerify`, `EcGenerateKey`) is used |
| **BigNum** | `BigNumInit`, `BigNumCopy`, `BigNumBits`, `BigNumBytes`, `BigNumIsWord`, `BigNumIsOdd`, `BigNumSetUint`, `BigNumCmp`, `BigNumAdd`, `BigNumSub`, `BigNumMod`, `BigNumAddMod`, `BigNumSqrMod`, `BigNumDiv`, `BigNumRShift`, `BigNumToBin`, `BigNumConstTime`, `BigNumValueOne` | High-level ops (`ExpMod`, `InverseMod`, `MulMod`) are used |
| **PKCS** | `Pkcs1v2Encrypt`, `Pkcs1v2Decrypt`, `Pkcs5HashPassword`, `Pkcs7Sign`, `Pkcs7GetCertificatesList` | Verification API (`Pkcs7Verify`, `Pkcs7GetSigners`) is used |
| **X.509** | `X509ConstructCertificateStack`, `X509ConstructCertificateStackV`, `X509Free`, `X509StackFree` | Certificate verification API is used |
| **Other** | `VerifyEKUsInPkcs7Signature` | — |

### Key Insight

EDK2 code predominantly uses the **streaming (incremental) API** pattern (`Init` → `Update` → `Final`) rather than one-shot digest helpers. This explains why many `*HashAll` and `*Duplicate` helpers are unused even when the underlying algorithm is actively used.

---

## 13. Code Paths Requiring PQC Migration (RSA / EC Usage)

The following consumer code paths call RSA or EC functions from BaseCryptLib and will require updates when transitioning to Post-Quantum Cryptography (PQC) algorithms (e.g., ML-DSA / CRYSTALS-Dilithium for signatures, ML-KEM / CRYSTALS-Kyber for key encapsulation).

### Priority Summary

| Functional Area | Component | Crypto Used | Migration Priority |
|---|---|---|---|
| Secure Boot | Image Verification | RSA (Authenticode) | **Critical** |
| Secure Boot | PKCS#7 Verify Driver | RSA/ECDSA | **Critical** |
| Secure Boot | Authenticated Variables | RSA/ECDSA (via PKCS#7) | **Critical** |
| Capsule Update | FmpAuthenticationLibRsa2048Sha256 | RSA-2048 PKCS#1 v1.5 | **Critical** |
| Capsule Update | FmpAuthenticationLibPkcs7 | RSA/ECDSA (via PKCS#7) | **Critical** |
| Capsule Update | Rsa2048Sha256GuidedSectionExtract (DXE + PEI) | RSA-2048 PKCS#1 v1.5 | **Critical** |
| Device Security | SPDM CryptlibWrapper | RSA + ECDSA + X.509 | **High** |
| TLS / Network | TlsDxe / TlsLib | RSA, ECDSA (in TLS handshake) | **High** |
| TLS / Network | TlsAuthConfigDxe | RSA/EC (X.509 CA store) | **Medium** |
| TEE | Intel TDX / AMD SEV measurement | RSA/EC (attestation) | **High** |
| Secure Boot UI | SecureBootConfigDxe | RSA/EC (cert enrollment) | **Medium** |

---

### Detailed Findings

#### 13.1 Secure Boot — Image Verification (`DxeImageVerificationLib`)

**File:** `SecurityPkg/Library/DxeImageVerificationLib/DxeImageVerificationLib.c`

| Function Called | Purpose |
|---|---|
| `AuthenticodeVerify()` | Verifies Authenticode (RSA) signature on PE/COFF images |
| `ImageTimestampVerify()` | Validates RSA/EC timestamp countersignature |
| `X509GetTBSCert()` | Parses X.509 TBS certificate data |

This is the core Secure Boot enforcement path. Every image loaded during boot (drivers, OS loader) is verified here. RSA is embedded in Authenticode signatures; a PQC replacement (e.g., ML-DSA) must be supported.

---

#### 13.2 Secure Boot — PKCS#7 Verification Protocol (`Pkcs7VerifyDxe`)

**File:** `SecurityPkg/Pkcs7Verify/Pkcs7VerifyDxe/Pkcs7VerifyDxe.c`

| Function Called | Purpose |
|---|---|
| `Pkcs7Verify()` | Validates PKCS#7 SignedData (RSA/EC inside) |

Exposes the UEFI PKCS#7 Verification Protocol consumed by other drivers. Needs to support PQC signers within PKCS#7 structures.

---

#### 13.3 Authenticated Variables (`AuthVariableLib`)

**File:** `SecurityPkg/Library/AuthVariableLib/AuthService.c`

| Function Called | Purpose |
|---|---|
| `Pkcs7Verify()` | Validates signatures on authenticated UEFI variables (PK, KEK, db, dbx) |

Protects all authenticated UEFI variables. PQC migration here requires updating the authenticated variable format and the UEFI spec.

---

#### 13.4 Capsule Update — RSA-2048 SHA-256 Authentication

**File:** `SecurityPkg/Library/FmpAuthenticationLibRsa2048Sha256/FmpAuthenticationLibRsa2048Sha256.c`

| Function Called | Purpose |
|---|---|
| `RsaNew()` | Allocates RSA context |
| `RsaSetKey()` | Sets RSA modulus (N) and public exponent (E) |
| `RsaPkcs1Verify()` | Verifies RSA PKCS#1 v1.5 signature over SHA-256 digest |
| `Sha256Init/Update/Final()` | Computes SHA-256 digest of capsule |

Direct RSA-2048 hardcoded into the capsule authentication path. A new `FmpAuthenticationLibMlDsa` (or similar) library will be needed.

---

#### 13.5 Capsule Update — PKCS#7 Authentication

**File:** `SecurityPkg/Library/FmpAuthenticationLibPkcs7/FmpAuthenticationLibPkcs7.c`

| Function Called | Purpose |
|---|---|
| `Pkcs7Verify()` | RSA/EC verification within PKCS#7 signed capsule |

PKCS#7 envelope approach; needs PQC signer support within the envelope.

---

#### 13.6 Guided Section Extraction (PEI + DXE)

**Files:**
- `SecurityPkg/Library/PeiRsa2048Sha256GuidedSectionExtractLib/PeiRsa2048Sha256GuidedSectionExtractLib.c`
- `SecurityPkg/Library/DxeRsa2048Sha256GuidedSectionExtractLib/DxeRsa2048Sha256GuidedSectionExtractLib.c`

| Function Called | Purpose |
|---|---|
| `RsaNew()`, `RsaSetKey()` | Construct RSA public key from embedded certificate |
| `RsaPkcs1Verify()` | Verify RSA-2048 signature on signed firmware section |
| `Sha256Init/Update/Final()` | Hash the section payload |

Signed firmware sections are verified during image loading at both PEI and DXE phases. New PQC-based GUIDed section extractors will be required alongside the existing RSA ones.

---

#### 13.7 Device Security — SPDM (`CryptlibWrapper`)

**File:** `SecurityPkg/DeviceSecurity/OsStub/CryptlibWrapper/CryptlibWrapper.c`

**RSA calls:**

| Function | Purpose |
|---|---|
| `RsaFree()` | Free RSA context |
| `RsaPkcs1Sign()` (SHA-256/384/512) | Sign with RSA PKCS#1 v1.5 |
| `RsaPkcs1Verify()` (SHA-256/384/512) | Verify RSA PKCS#1 v1.5 |
| `RsaGetPrivateKeyFromPem()` | Load RSA private key |
| `RsaGetPublicKeyFromX509()` | Extract RSA public key from certificate |

**EC calls:**

| Function | Purpose |
|---|---|
| `EcNewByNid()` | Create EC context (P-256/P-384/P-521) |
| `EcFree()` | Free EC context |
| `EcGenerateKey()` | Generate EC key pair |
| `EcDhComputeKey()` | ECDH key exchange |
| `EcDsaSign()` | ECDSA signing |
| `EcDsaVerify()` | ECDSA verification |
| `EcGetPrivateKeyFromPem()` | Load EC private key |
| `EcGetPublicKeyFromX509()` | Extract EC public key from certificate |

SPDM is used for mutual authentication between host and PCIe/CXL devices. PQC migration must update algorithm negotiation, certificate handling, and session key establishment.

---

#### 13.8 TLS / Network (`TlsDxe` / `TlsLib`)

**Files:** `NetworkPkg/TlsDxe/TlsImpl.c`, `CryptoPkg/Library/TlsLib/TlsLib.c`

RSA and ECDSA are used implicitly via the underlying TLS library (OpenSSL or MbedTLS) for:
- Server and client certificate verification
- TLS handshake signing
- Session key establishment

PQC migration here depends on the underlying TLS library adding PQC cipher suite support (per IETF drafts for TLS 1.3 + PQC).

---

#### 13.9 Secure Boot Configuration UI (`SecureBootConfigDxe`)

**File:** `SecurityPkg/VariableAuthenticated/SecureBootConfigDxe/SecureBootConfigImpl.c`

Manages Secure Boot variables (db, dbx, KEK, PK) and validates certificates during manual enrollment. Will need to support PQC certificate formats in the configuration UI.

---

### Migration Strategy Recommendations

1. **Dual-Algorithm Support:** Ship PQC libraries (e.g., `FmpAuthenticationLibMlDsa`) in parallel with legacy RSA libraries; select via DSC.
2. **Hybrid Signatures:** During the transition period, require both RSA and PQC signatures (hybrid mode) on capsules, images, and certificates.
3. **Phased Rollout:**
   - Phase 1: Add PQC verification support alongside RSA
   - Phase 2: Require hybrid (RSA + PQC) signatures
   - Phase 3: Deprecate RSA-only paths
4. **Protocol / Format Updates:** FMP capsule headers, Authenticode structures, SPDM negotiation, and UEFI authenticated variable format all need to accommodate larger PQC signature sizes (ML-DSA signatures are ~2–3 KB vs. ~256 bytes for RSA-2048).
5. **Library Abstraction:** Introduce a new `BasePqcLib` or extend `BaseCryptLib` with PQC algorithm families (`MlDsa`, `MlKem`, `SlhDsa`).
