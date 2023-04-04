# Ng911Lib Namespaces

| Namespace              | Description |
|------------------------|-------------|
| AdditionalData         | Provides classes for working with additional data provided with NG9-1-1 calls. RFC 7852 specifies five components of additional data: Provider Information, Device Information, Service Information, Subscriber Information and Comments. This namespace also provides classes for hangling xCard (the XML format of a vCard) and jCard (the JSON format of a vCard). |
| AgencyLocator          | Contains data/model classes required by the server side and the client side of the Agency Locator service specified in Sections 4.15 and E.10 of NENA-STA-010.3b. |
| BadActor              | Data/model classes for passing data to the bad actor service of a Border Control Function (BCF)  |
| CommonAlertingProtocol |  Data/model classes for handling Common Alerting Protocol (CAP) calls as specified in the [Common Alerting Protocol Version 1.2 OASIS Standard](http://docs.oasis-open.org/emergency/cap/v1.2/CAP-v1.2-os.pdf)     |
| ConferenceEvent        |  Data/model classes for the subscribe/notify SIP interface for conference state as specified in RFC 4575 SIP Event Package for Conference State. |
| DiscrepancyReporting   | Data/model classes for implementing the client side and the server side of the discrepancy reporting service. These classes can be used by the different functional elements within a NG9-1-1 system. See Sections 3.7 and E.2 of NENA-STA-010.3b. |
| ECall                  | Model classes that implement the control and meta-data schemas defined in RFC 8147. Note: These classes do not support the European Minimum Set Data (MSD). The control and meta-data schemas are required for implementing the Next Generation Automatic Crash Notificatation (NG-ACN) calls as specified in RFC 8148. |
| Geocode                | Classes for the Geocode Conversion Service specified in Sections 4.5.1, 4.5.2 and E.5 of NENA-STA-010.3b. |
| Held                   | Classes for the HELD protocol as specified in RFC 5985 HTTP-Enabled Location Delivery |
| I3LogEvents            | Data/model classes for NG9-1-1 logging as specified in Sections 4.12.3 and E.8 of NENA-STA-010.3b. |
| I3SubNot               | Data/model classes for handling NG9-1-1 SIP Subscribe/Notify event packages such as Element State, Service State, Queue State, etc. |
| Lost                   |  Data/model and utility classes for interfacing to an Emergency Call Routing Function (ECRF) using the LoST protocol. See RFC 5222 LoST: A Location-to-Service Translation Protocol. |
| Msag                   |  Data/model classes for the Master Street Address Guide (MSAG) conversion service. See Sections 4.4.1 and E.4 of NENA-STA-010.3b. |
| Ng911CertUtils         |  Contains utility classes for building self-signed  and signed X.509 certificates that can be used for testing. These classes can build X.509 certificates that include the certificate extensions required for NG9-1-1 systems. See [Public Safety Answering Point (PSAP) Credentialing Agency (PCA) Certificate Policy](https://ng911ioc.org/wp-content/uploads/2023/03/PSAP-Credentialing-Agency-PCA-Certificate-Policy-v1.1-02-22-2023-CLEAN.pdf)     |
| Ng911Common            | Data/model classes used by all REST/JSON schemas defined in NENA-STA-010.3b. |
| Ng911Lib.Utilities     | Utility classes for serializing and deserializing JSON and XML documents. |
| Pidf                   | Data/model and utility classes for dealing with location data in NG9-1-1 applications. See [Working with PIDF-LO Data](~/articles/WorkingwithPIDF-LOData.md). |
| PolicyRouting          | Data/model classes for implemting the NG9-1-1 policy routing rules. See Sections 3.3.3 and E.1 of NENA-STA-010.3b. |
| PolicyStore            | Data/model classes for implementing the server side and the client side of the Policy Store Services defined in Sections 3.3.1 and E.1 of NENA-STA-010.3b. |
| SipRecMetaData         |  Data/model classes for dealing with the meta data XML document used in the SIP Session Recording (SIPREC) protocol. See RFC 7865 Session Initiation Protocol (SIP) Recording Metadata. |
| TestCall               |  Data/model classes for the NG9-1-1 test call generator interface. See Sections 4.6.18, 4.6.17.1 and E.6 of NENA-STA-010.3b. |
| Veds                   |  Data/Model classes for the Vehicle Emergency Data Set (VEDS) used in NG9-1-1 Advanced Automatic Crash Notification (AACN) calls. See RFC 8148 Next-Generation Vehicle-Initiated Emergency Calls and Advanced Automatic Collision Notification (AACN) Vehicle Emergency Data Set (VEDS) [APCO/NENA Candidate ANS 2.102.1.2022](https://www.apcointl.org/~documents/standard/21021-2022-aacn-vehicle-data-set-veds). |


