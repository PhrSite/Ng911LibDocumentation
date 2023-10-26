# The Ng911Lib Class Library
The Ng911Lib project is a class library that provides a set of classes for the data schemas required for implementing Next Generation 9-1-1 (NG9-1-1) applications. This class library provides classes that enable application developers to create, serialize and deserialize JSON and XML documents used in NG9-1-1.

This portable, cross-platform class library is written in the C# language and the library package targets the .NET 7.0 environment. It may be used by applications that target the Windows (version 10 or later) or Linux operating systems.

The following document specifies the design and functional requirements of various functional elements as well as the protocols and interfaces required for those functional elements to communicate with each other.

>[NENA i3 Standard for Next Generation 9-1-1](https://cdn.ymaws.com/www.nena.org/resource/resmgr/standards/nena-sta-010.3b-2021_i3_stan.pdf), National Emergency Number Association (NENA) 911 Core Services Committee, i3 Architecture Working Group, NENA-STA-010.3b-2021, October 7, 2021.

## JSON Schemas Defined in NENA-STA-010.3b
Appendix E of NENA-STA-010.3b contains the REST/JSON schema definitions specific to NG9-1-1.

| NENA-STA-010.3b Appendix | NENA-STA-010.3b Sections | NG911Lib Namespaces | Description |
|--------|---------|-----------|-------------|
| E.1 Policy Store     | 3.3.1, 3.3.3 | PolicyStore, PolicyRouting | Data/model classes for implementing a policy store service and policy routing rules |
| E.2 Discrepancy Report | 3.7 | DiscrepancyReporting | Data/model classes for implementing the client side of the discrepancy reporting service. These classes can be used by the different functional elements within a NG9-1-1 system.
| E.3 Dequeue Registration | 4.2.1.4 | I3SubNot | Data/model classes for all SIP NG9-1-1 subscribe/notify interfaces |
| E.4 MSAG Conversion Service | 4.4.1 | Msag | Data/model classes for the Master Street Address Guide (MSAG) conversion service |
| E.5 Geocode Conversion Service | 4.5.1, 4.5.2 | Geocode | Data/model classes for the geocode conversion service |
| E.6 Test Call | 4.6.18, 4.6.17.1 | TestCall | Data/model classes for the NG9-1-1 test call generator |
| E.7 Additional Data Repository | 4.11 | TBD | TBD |
| E.8 Logging Service | 4.12.3 | I3V3.LogEvents | Data/model classes for NG9-1-1 logging |
| E.9 Bad Actor Service | 4.1.2 | BadActor | Data/model classes for passing data to the bad actor service of a Border Control Function (BCF) |
| E.10 Service/Agency Locator Service | 4.15 | AgencyLocator| Data/model classes for the agency locator service interface |
| E.11 Notify Bodies | 4.2.1.6, 4.3.4, 4.6.8, 2.4.1, 4.2.1.3, 2.4.2 | I3SubNot | Data/model classes for all SIP NG9-1-1 subscribe/notify interfaces. The subscribe/notify event packages include ESRP Notify, Abandoned Call, Element State, Service State, and Queue State |
| E.11.4 Common YAML | NA | Ng911Common | Data/model classes used by all REST/JSON schemas |

## XML Schemas Required in NENA-STA-010.3b
Many of the schemas used in NG9-1-1 are specified by standards produced by the Internet Engineering Task Force (IETF)

| Ng911Lib Namespace | Related Standards | Description |
|--------------------|-------------------|-------------|
| AdditionalData     | RFC 7852 and others. See [Working with Additional Data](~/articles/WorkingWithAdditionalData.md) | Data/Model classes for dealing with additional data for NG9-1-1 calls|
| CommonAlertingProtocol | [Common Alerting Protocol Version 1.2 OASIS Standard](http://docs.oasis-open.org/emergency/cap/v1.2/CAP-v1.2-os.pdf) | Data/model classes for handling Common Alerting Protocol (CAP) calls | 
| ConferenceEvent | RFC 4575 SIP Event Package for Conference State | Data/model classes for the subscribe/notify SIP interface for conference state |
| Held | RFC 5985 HTTP-Enabled Location Delivery (HELD) | Data/model classes for requesting and receiving location information |
| Lost | RFC 5222 LoST: A Location-to-Service Translation Protocol | Data/model and utility classes for interfacing to an Emergency Call Routing Function (ECRF) using the LoST protocol |
| Pidf | RFC 4119, RFC 5491 and others. See [Working with PIDF-LO Data](~/articles/WorkingwithPIDF-LOData.md) | Data/model and utility classes for dealing with location data in NG9-1-1 applications |
| SipRecMetaData | RFC 7865 Session Initiation Protocol (SIP) Recording Metadata | Data/model classes for dealing with the meta data XML document used in the SIP Session Recording (SIPREC) protocol |
| ECall | RFC 8147 Next-Generation Pan-European eCall | Model classes that implement the control and meta-data schemas defined in RFC 8147. Note: These classes do not support the European Minimum Set Data (MSD). |
| Veds | RFC 8148 Next-Generation Vehicle-Initiated Emergency Calls, Advanced Automatic Collision Notification (AACN) Vehicle Emergency Data Set (VEDS) [APCO/NENA Candidate ANS 2.102.1.2022](https://cdn.ymaws.com/www.nena.org/resource/resmgr/standards/apco_nena_ans_2.102.1-2022_a.pdf) | Data/Model classes for the Vehicle Emergency Data Set (VEDS) used in NG9-1-1 Advanced Automatic Crash Notification (AACN) calls |





