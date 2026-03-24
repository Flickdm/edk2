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
