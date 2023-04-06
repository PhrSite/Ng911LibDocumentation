# Introduction
X.509 certificates are used for a variety of purposes in NG9-1-1 applications and networks.

The PSAP Credentialing Agency (PCA) is responsible for issuing certificates to be used by functional elements, services, agencies and agents within a NG9-1-1 emergency services network. Certificates issued by the PCA contain the following information in the otherName sequence of the Subject Alternate Name certificate extension (see Section 4.2.1.6 of RFC 5280  and Section 7.1.2.11 of the PCA Certificate Policy).

1.	ID Type (identifies the entity such as: Element, Service, Agent or Agency)
2.	ID of the entity
3.	Roles assigned to the entity
4.	Owner of the certificate assigned to the entity

The above information can be used by NG9-1-1 applications to perform extended authorization for both servers and clients.

The PSAP Credentialing Agency (PCA) has produced the following document that specifies X.509 certificates specific to NG9-1-1 applications.

> [Public Safety Answering Point (PSAP) Credentialing Agency (PCA) Certificate Policy](https://ng911ioc.org/wp-content/uploads/2023/03/PSAP-Credentialing-Agency-PCA-Certificate-Policy-v1.1-02-22-2023-CLEAN.pdf). NG9-1-1 Interoperability Oversight Commission (NIOC), V1.1, February 22, 2023

The Ng911CertUtils namespace contains classes that allow applications to perform extended authorization and authentication of both clients and servers. It also provides the ability to create self-signed certificates and signed certificates for testing purposes.

# The CertUtils Class
The [CertUtils](~/api/Ng911CertUtils.CertUtils.yml) class is a static class that provides two types of functionality.

## Extended Authorization Support

```
public static Ng911SanParams GetOtherNameParams(X509Certificate2 Cert)
```

This method returns a [Ng911SanParams](~/api/Ng911CertUtils.Ng911SanParams.yml) object if the input certificates contains a Subject Alternative Name (SAN) certificate extension and if that extension contains an Ng9-1-1 custom otherName sequence. Be sure to catch exceptions when calling this method because it throws an ArgumentException if:

1. The certificate does not have a SAN extension
2. The method could not read the SAN extension
3. The otherName sequence in the SAN extension is not formatted as specified in Section 7.1.2.11 of the PCA Certificate Policy document.

The GetOtherName method will throw a AsnContentException if the input certificate cannot be read.

The Ng911SanParams object can then be used to perform extended authoriztion. It provides the following properties for that putpose.

| Property | Type | Description |
|--------|--------|-------------|
| idType | string | Specifies the type of object that is being identified, such as AgentId, AgencyId, ServiceId or ElementId. See Section 2.1 of NENA-STA-010.3.  Will be set to "CAId" if the certificate is for a Certificate Authority.|
| iD | string | Contains a URI of the entity being identified. For example: psap.allegheny.pa.us. |
| roles | List\<string\> | Contains a list of roles. See Section 5.3 of NENA-STA-010.3. The contents and the format of the items in this list depend upon the idType field. |
| owner | string | Contains the ID of the issuing agency of the certificate. For an Intermediage CA (ICA), this field shall contain the iD of the issuing CA. May be null of the certificate is for a root CA or an end entity. |


## Building Test Certificates


```
public static X509Certificate2 CreateEcdsaSelfSignedCertificate(DistinguishedNameParams Dnp, 
    KeyUsageParams Kup,
    Ng911SanParams Nsp,
    int ExpiresYears,
    string strPw,
    bool IsCa,
    out Exception Ex);

public static void CreateEcdsaSelfSignedCertificate(DistinguishedNameParams Dnp,
    KeyUsageParams Kup,
    Ng911SanParams Nsp,
    int ExpiresYears,
    string strPw,
    string strDir,
    string strFileNameNoExtension,
    bool IsCa,
    out Exception Ex);

public static X509Certificate2 CreateEcdsaSignedCertificate(X509Certificate2 Root,
    DistinguishedNameParams Dnp,
    KeyUsageParams Kup,
    Ng911SanParams Nsp,
    bool IsCa, out Exception Ex);

public static void CreateEcdsaSignedCertificate(X509Certificate2 Root,
    DistinguishedNameParams Dnp,
    KeyUsageParams Kup,
    Ng911SanParams Nsp,
    string strPw,
    string strDir,
    string fileNameNoExtension,
    bool IsCa,
    out Exception Ex);

public static X509Certificate2 CreateRsaSelfSignedCertificate(DistinguishedNameParams Dnp,
    KeyUsageParams Kup,
    Ng911SanParams Nsp,
    int ExpiresYears,
    string strPw,
    bool IsCa,
    out Exception Ex);

public static void CreateRsaSelfSignedCertificate(DistinguishedNameParams Dnp, 
    KeyUsageParams Kup, Ng911SanParams Nsp,
    int ExpiresYears,
    string strPw,
    string strDir,
    string strFileNameNoExtension,
    bool IsCa,
    out Exception Ex);

public static X509Certificate2 CreateRsaSignedCertificate(X509Certificate2 Root,
    DistinguishedNameParams Dnp,
    KeyUsageParams Kup,
    Ng911SanParams Nsp,
    string strPw,
    bool IsCa,
    out Exception Ex);

public static void CreateRsaSignedCertificate(X509Certificate2 Root,
    DistinguishedNameParams Dnp,
    KeyUsageParams Kup,
    Ng911SanParams Nsp,
    string strPw,
    string strDir,
    string strFileNameNoExtension,
    bool IsCa,
    out Exception Ex);
```
