# Using FIDO2 In Electronic Locking Systems 🔐

<img src="profile/img/unlock.jpeg" alt="Unlocking an electronic access reader using a FIDO2 hardware authenticator" width="50%">

This group is a result of the 2021/22 *Hot Topics in Secure Identity Research* seminar and the 2022 *Behavioral Authentication and Physical Access Management* seminar at the [Hasso-Plattner-Institute (HPI)](https://hpi.de/).

## FIDO2 in the context of Electronic Locking Systems

In the 2021/22 winter semester, we evaluated whether FIDO2 can be used not only for authentication in the web,
but also for authentication offline in electronic locking systems.

This organization holds the changes we made to existing tools in order to build a working proof
of concept.
It uses a [Solo 2 hacker edition](https://solokeys.com/) as the FIDO2 authenticator, an ACR-122U NFC reader, a Raspberry Pi 3B+, and some status LEDs.
The access rights are written onto the authenticator using a custom web application and Chromium.

<img src="profile/img/poc.png" alt="Proof of concept consisting of a Solo 2, ACR-122U and Raspberry Pi" width="50%">

### Repository Overview

We modified the following components of the Solo 2 firmware and tools to include the necessary CTAP 2.1 features:

- [solo2](https://github.com/All-Your-Locks-Are-Belong-To-Us/solo2/tree/feature/fido-2-1-large-blobs)
- [fido-authenticator](https://github.com/All-Your-Locks-Are-Belong-To-Us/fido-authenticator/tree/feature/fido-2-1-large-blobs)
- [ctap-types](https://github.com/All-Your-Locks-Are-Belong-To-Us/ctap-types/tree/feature/fido-2-1-largeBlobs)
- [apdu-dispatch](https://github.com/All-Your-Locks-Are-Belong-To-Us/apdu-dispatch)
- [oath-authenticator](https://github.com/All-Your-Locks-Are-Belong-To-Us/oath-authenticator)
- [admin-app](https://github.com/All-Your-Locks-Are-Belong-To-Us/admin-app)
- [ctaphid-dispatch](https://github.com/All-Your-Locks-Are-Belong-To-Us/ctaphid-dispatch)
- [lpc55-host](https://github.com/All-Your-Locks-Are-Belong-To-Us/lpc55-host/tree/fix/configure-pfr)

To test our firmware changes and simulate a lock, we developed a small suite of tools for FIDO2.
Even though the CTAP2.1 standard specifies DEFLATE (RFC 1951) for large blob compression, Chromium uses GZIP (RFC 1952). Therefore, we added GZIP compression to the `libfido2` for now. We filed [a bug report](https://bugs.chromium.org/p/chromium/issues/detail?id=1312802) for Chromium to address this behavior.

- [**fido2-debug-client**](https://github.com/All-Your-Locks-Are-Belong-To-Us/fido2-debug-client)
- [libfido2](https://github.com/All-Your-Locks-Are-Belong-To-Us/libfido2/tree/feature/large-blob-use-gzip)

Finally, we write access rights onto a FIDO2 authenticator using a small web application.
The dependencies we used did not support the newest FIDO2 features, so we added them.

- [**webauthn-updater**](https://github.com/All-Your-Locks-Are-Belong-To-Us/webauthn-updater)
- [python-keycloak](https://github.com/All-Your-Locks-Are-Belong-To-Us/python-keycloak/tree/fix/fetch-token-for-client)
- [webauthn-json](https://github.com/All-Your-Locks-Are-Belong-To-Us/webauthn-json/tree/feature/credential-protection-policy)
- [py_webauthn](https://github.com/All-Your-Locks-Are-Belong-To-Us/py_webauthn/tree/feature/credential-protection-policy)

## Implementing a FIDO2 Relying Party on microcontrollers

In the 2022 summer semester we built upon the previous project and **implemented a FIDO2 library for microcontrollers called [libmicrofido2](https://github.com/All-Your-Locks-Are-Belong-To-Us/libmicrofido2)**.

The library was inspired by [libfido2](https://github.com/Yubico/libfido2/) and we ran it on the AVR ATmega1284P, the nRF52480 and the ESP32-C3FN4 microcontrollers.

With this library, the existing structure from the previous semester can be used on microcontrollers, thus the access control can be implemented on electronic door cylinders.

<img src="profile/img/unlock-cylinder.jpg" alt="Unlocking an electronic locking cylinder using a FIDO2 hardware authenticator" width="50%">

### Repository overview

To implement the [libmicrofido2](https://github.com/All-Your-Locks-Are-Belong-To-Us/libmicrofido2), we modified various other libraries and integrated them into the library.

- [cb0r](https://github.com/All-Your-Locks-Are-Belong-To-Us/cb0r)
- [Monocypher](https://github.com/All-Your-Locks-Are-Belong-To-Us/Monocypher)
- [tinf](https://github.com/All-Your-Locks-Are-Belong-To-Us/tinf)
- [aes_gcm](https://github.com/All-Your-Locks-Are-Belong-To-Us/aes_gcm)

To test and develop the application, the previously mentioned [fido2-debug-client](https://github.com/All-Your-Locks-Are-Belong-To-Us/fido2-debug-client) was used. To create FIDO2 access rights with the CTAP 2.1 largeBlob extension, the [webauthn-updater](https://github.com/All-Your-Locks-Are-Belong-To-Us/webauthn-updater) was used.
