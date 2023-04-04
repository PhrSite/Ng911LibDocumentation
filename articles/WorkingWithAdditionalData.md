# Introduction
In NG9-1-1 applications, additional data refers to data such as information about the subscriber or the device that was used to place the emergency call.

The following document describes five different blocks of additional data about a call and defines the XML schemas for each of these data blocks.

> RFC 7852 Additional Data Related to an Emergency Call

NENA has defined two additional data blocks in the following document.

> [NENA Standard for NG9 1 1 Additional Data](https://cdn.ymaws.com/www.nena.org/resource/resmgr/standards/nena-sta-012.2_addldata_2017.pdf), National Emergency Number Association (NENA), NENA-STA-012.2-2017, November 29, 2017.

Additional data may be delivered with a call by-value within the body of a SIP INVITE request. It may also be delivered by-reference by including SIP Call-Info headers that contain a URIs that can be used to retrieve the data via an HTTP GET request. RFC 7852 describes the mechanism and protocols that must be used to deliver additional data.

The AdditionalData namespace provides data/model classes that can be used for serialization and deserialization of additional data XML documents.

# The AdditionalData Namespace

This namespace contains data classes for additional data for an emergency call. RFC 7852 defines the first five blocks of additional data listed in the following table. NENA-STA-012.2 defines the last two blocks in the list.

| Data Type | AdditionalData Class | Description |
|-----------|----------------------| ------------|
| Subscriber&nbsp;Information  | [SubscriberInfoType](~/api/AdditionalData.SubscriberInfoType.yml) |Describes the owner of the device (if provided by the device) and the subscriber information (if provided by a service provider). Also may contain a contact location that is not necessarily the location of the caller or incident but is rather the nominal contact address. |
| Service&nbsp;Information | [ServiceInfoType](~/api/AdditionalData.ServiceInfoType.yml) | Describes the service that the service provider provides to the caller. This information includes the service environment (business, residence), service type (wireless, landline, etc.), service mobility (mobile, fixed, etc.) and other information. |
| Device&nbsp;Information | [DeviceInfoType](~/api/AdditionalData.DeviceInfoType.yml) | Provides information about the device that was used to place the emergency call.  |
| Data&nbsp;Provider&nbsp;Information | [ProviderInfoType](~/api/AdditionalData.ProviderInfoType.yml) | This block of data contains identification and contact information about the provider of one or more of additional data blocks about the emergency call. |
| Comment | [CommentType](~/api/AdditionalData.CommentType.yml) | This block provides a mechanism for the data provider to supply extra, human-readable information. |
| Caller Information | [CallerInfo](~/api/AdditionalData.CallerInfo.yml) | Additional data about the caller. Defined in NENA-STA-012.2 |
| Location Information | [LocationInfo](~/api/AdditionalData.LocationInfo.yml) | Additional data about the location from which the emergency call was made. Defined in NENA-STA-012.2 |


# The vcardType Class
In NG9-1-1 applications, the term "vCard" refers to the XML schema of the vCard defined in RFC 6350. RFC 6351 defines the XML schema using Relax NG notation. Appendix A of RFC 7852. Occasionally, the term xCard refers to the XML schema for the vCard.

The name of the model class for a vCard is vcardType. The [vcardType](~/api/AdditionalData.vcardType.yml) class contains information about a person that is in some way related to to an additional data block. For example, the SubscriberData field of the SubscriberInfoType class is an array of vcardType objects. The information in this array contains information information about the individual subscribers to the telephone service that was used to make the emergency call.

The vcardType class was automatically generated using the Visual Studio utility application called xsd.exe from the XML Schema Definition (XSD) file shown in Appendix A of RFC 7852.

## vcardType Class Helper Properties
The XSD file for the vcardType specifies that just about every element is an array of elements. For example, the element for name is called "n" Within the "n" element, elements for first, surname, etc. are all specified as arrays of strings. While this provides flexibility, it creates a somewhat difficult to use programming interface. The following code snippet shows what is required to set the name for a vcardType object.

```
vcardType vc = new vcardType();
vc.n = new n();
vc.n.surname = new string[1];
vc.n.surname[1] = "Doe";
vc.n.given = new string[1];
vc.n.given[1] = "John";
```
Several helper properties have been added to the vcardType class to simplify the programming interface for this class. See the following table for a list of the helper properties. The helper properties focus on what is believed to be the most commonly used aspects of the subject of a vCard in NG9-1-1 applications. These are the name, address, telephone and e-mail address.

Each helper property is flagged with an XmlIgnore attribute so that it is not serialized or deserialized. Each helper property assumes that only the first element in an array is of interest. The setter for the property automatically creates the array and assigns the first element to the value parameter. The getter for the property returns the first element in the array or null if the array field is null or empty.

The following code snippet is an example of how to set the last and first names in a vcardType object using the helper properties.

```
vcardType vc = new vcardType();
vc.LastName = "Doe";
vc.FirstName = "John";
```

The property names in several cases have been changed to make the interface more intuitive. For example, "surname" has been changed to "LastName" and "given" has been changed to "FirstName".

| vcardType Helper Property | Description |
|---------------------------|-------------|
| City       | Gets or sets the first locality (city) of the first address in the vcardType |
| Country | Gets or sets the country element of the first address in the vcardType |
| EMail | Gets or sets the first email address |
| Ext | Gets or sets the extended (ext) address field of the first address in the vcard |
| FirstName | Gets or sets the first (given) name |
| FullName |  Gets or sets the first fn (formatted full name) field of the vcardType |
| LastName | Gets or sets the last name (surname) |
| MiddleName | Gets or sets the middle name |
| PoBox | Gets or sets the first pobox element of the first address in the vcard |
| Prefix | Gets or sets the first name prefix (Mr., Mrs., Ms., etc) |
| State | Gets or sets the first region (state) in the first address of the vcard |
| Street | Gets or sets the first street address field of the first address in the vcardType |
| Suffix | Gets or sets the first suffix field of the n element |
| TelephoneNumber | Gets or sets the first telephone number in the vcardType. Telephone numbers are treated as 10-digit numeric strings by this property. |
| ZipCode | Gets or sets the first code (zip code) element of the first address in the vcardType |

## adr Class Helper Properties
The "adr" class is used in the vcardType class to represent an address. As with most other fields in the vcardType, all elements within this class are defined as arrays. The following table lists the helper properties that have been added to the adr class to simplify the programming interface.

| adr Helper Property | Description |
|---------------------------|-------------|
| Address Type | Gets or sets the first type field value for the address. |
| City | Gets or sets the first City (locality) field. |
| Country | Gets or sets the first country field.|
| Ext | Gets or sets the first ext (extended address information) field. This field can contain extended address information such as suite numbers, etc. |
| PoBox | Gets or sets the first pobox field |
| Pref | Gets or sets the pref (preference) value of the adr object. The preference is an integer greater than 1 stored as a string. |
| State | Gets or sets the first State (region) field. |
| Street | Gets or sets the first street field. The street field is the full street address that includes house number, street name and any prefixes and suffixes. |
| ZipCode | Gets or sets the first ZipCode (code) field. |

# The JCard Class
In NG9-1-1 applications, interfaces that use XML use the XML version of the vCard. Interfaces that use JSON use the JSON version of the vCard, which is called the jCard. The following document specifies the schema of the jCard.

> RFC 7095 jCard: The JSON Format for vCard

The following code snippet is an example of a jCard.
```
["vcard",
  [
    ["version", {}, "text", "4.0"],
    ["fn", {}, "text", "John Doe"],
    ["gender", {}, "text", "M"],
    ["categories", {}, "text", "computers", "cameras"],
    ["adr", {}, "text",
      [
        "", "", "123 Main Street",
        "Any Town", "CA", "91921-1234", "U.S.A."
      ]
    ]
  ]
]
```

Instead of named property/value pairs, RFC 7095 defines everything as an array of variable length arrays. Even the root element is an array instead of an object delimited by curly brackets (as is typical of JSON objects).

In order to simplify the programming interface for a jCard, the AdditionalData namespece provides a class called [JCard](~/api/AdditionalData.JCard.yml). JCard is a static class that converts between a jCard string and a vcardType object for deserialization and converts between a vcardType object and a jCard string for serialization. The JCard class has the following public methods.

```
public static vcardType JCardStringToVCardType(string strJcard);
public static string XcardToJsonString(vcardType vcard);
```

When an application receives a jCard as a string, it can used the JCardStringToVCardType() method of the JCard class to convert the JSON string to a vcardType object. Then it can read the information about the entity represented in the vCard (xCard) using the properties and fields of the vcardType class.

When an application needs to create a jCard string, it performs the following steps.
1. Create a vcardType object
2. Set the properties and fields of the vcardType object
3. Call the XcardToJsonString() method of the JCard class to produce a string containing the jCard JSON object.


