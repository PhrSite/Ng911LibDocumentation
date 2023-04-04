# Introduction
All location information in NG9-1-1 applications uses the Presence Information Data with Format Location Object (PIDF-LO) XML schemas for location information. Many functional elements within an NG9-1-1 network need to handle PIDF-LO XML location data. For example, PIDF-LO XML documents may be passed by-value or by-reference within a SIP INVITE message when a call is delivered to a Public Service Answering Point (PSAP). The PSAP system needs to deserialize the PIDF-LO XML document into appropiate classes so that it may extract location information that can be displayed to a call taker. An Emergency Call Routing Function (ECRF) receives PIDF-LO XML data in a LoST request so it can determine how to route calls. A NG9-1-1 originating service provider must determine the location of a caller and build a PIDF-LO XML document that it can provide with the call that it delivers to the emergency services network.

The Pidf namespace contains classes for parsing and generating PIDF-LO XML documents.

The following table lists the RFCs that must be supported by NG9-1-1 applications. The "Level of Support" column indicates which portions of each RFC are supported by the classes in the Pidf namespace.

| Document | Title | Description | Level of Support |
|----------|-------|-------------|------------------|
| RFC 3863 | Presence Information Data Format (PIDF) | Defines the basic presence schema | Full |
| RFC 4119 | A Presence-based GEOPRIV Location Object Format | Defines the civic address and basic geopriv element schemas. Specifies use of the element and extends RFC 3863. | Full |
| OGC 06-142r1 | GML 3.1.1 PIDF-LO Shape Application Schema for use by the Internet Engineering Task Force (IETF) | Defines the application schema for the Geography Markup Language (GML). Contains definitions two dimentional shapes (point, polygon, circle, ellipse and arc band) and three dimensional shapes (sphere, ellipsoid and prism). | Full Support for both 2D and 3D profiles |
| RFC 4479 | A Data Model for Presence | Defines the data model for the device and person elements of the presence XML document. | Full |
| RFC 5139 | Revised Civic Location Format for Presence Information Data Format Location Object (PIDF-LO) | Updates RFC 4119 and revises the civic address schema. | Full Support |
| RFC 5491 | GEOPRIV Presence Information Data Format Location Object (PIDF-LO) Usage Clarification, Considerations, and Recommendations | Updates RFC 4119. | Full support except that this RFC states that there may be an unbounded number of tuple, device or person elements in a presence XML document. This class library assumes that there will be only one of each type of subelement. This class library does support multiple locations. |
| RFC 5962 | Dynamic Extensions to the Presence Information Data Format Location Object (PIDF-LO) | Defines the Dynamic element in the location-info element that provide heading, orientation and elevation angle of the object whose location is specified in the XML document. | Full Support |
| RFC 6848 | Specifying Civic Address Extensions in the Presence Information Data Format Location Object (PIDF-LO) | Defines extensions for the following elements: PN (Post Number), MP (Milepost), STP (Street Type Prefix) and HNP (House Number Prefix). | Supports the defined extensions for the following elements: PN (Post Number), MP (Milepost), STP (Street Type Prefix) and HNP (House Number Prefix). Also provides support for future extensions via an XML extension point. |
| RFC 7459 | Representation of Uncertainty and Confidence in the Presence Information Data Format Location Object (PIDF-LO) | Defines an element called confidence for the location-info element. | Full Support |

The actual structure of a PIDF-LO XML document is quite complicated because a complete document can contain elements defined in a large number of standards spanning many different XML namespaces. For example, the Circle element is part of the pidflo namespace (http://www.opengis.net/pidflo/1.0), but pos sub-elements of a Circle are part of the gml namespace (http://www.opengis.net/gml). Failure to correctly specify the XML namespaces when building a presence XML document will result in incompatibilites across different applications. The classes in the Pidf namespace can help application developers manage these complexities.
# Structure of PIDF-LO Location Data
The following figure shows the basic structure of a PIDF-LO XML document. This figure shows a simplified version of the XML document with many elements and all attributes omitted.

```
<presence>
    <tuple>
        <status>
            <geopriv>
                <location-info></location-info>
            </geopriv>
        </status>
    </tuple>
    <device>
        <geopriv>
            <location-info></location-info>
        </geopriv>
    </device>
    <person>
        <geopriv>
            <location-info></location-info>
        </geopriv>
    </person>
</presence>

```
The root element of a PIDF-LO XML document is the presence element. The presence element may contain tuple, device and person elements. A tuble element is a general purpose container for location information that was defined in RFC 3863. In addition to the status sub-element, the tuple element contains an "id" attribute, a timestamp element and a contact element. The "id" attribute uniquely identifies the tuple element. The timestamp element specifies the time of the location information and the contact element may contain a URI which may be used to contact the entity that the location pertains to.

RFC 4479 defines the device and the person elements, which also contain location information. The device element contains location information about the device from which the emergency call was made and the person element contains location information for the person making the call, which may be a different location in certain cases described in RFC 5491.

RFC 5491 states that there may be an unbounded number of tuple, device or person elements in a presence XML document. This class library assumes that there will be only one of each type of subelement. This class library does support multiple locations. See [Handling Multiple Locations](#handling-multiple-locations), below.

The location-info element contains the actual location data and it has the following structure.

```
<location-info>
    <civicAddress></civicAddress>
    <Point></Point>
    <Circle></Circle>
    <Ellipse></Ellipse>
    <ArcBand></ArcBand>
    <Polygon></Polygon>
    <Sphere></Sphere>
    <Ellipsoid></Ellipsoid>
    <Prism></Prism>
    <confidence></confidence>
    <Dynamic></Dynamic>
</location-info>
```

One or more of the subelements of a location-info will be present. When more than one element is present, the location-info element will typically contain a civicAddress element and one of the geodetic location elements such as Point, Circle, etc.

The [locInfoType](~/api/Pidf.locInfoType.yml) class maps to the location-info element (see below). If a subelement of the location-info element is not present then the corresponding property of the locInfoType will be null.

# Mapping Class Names to Element Names
Each element in a PIDF-LO XML document maps to a C# class. In general, class names match or nearly match the element names. The following table shows the class name to element name mappings for several of the key elements.

| Element Name | Class Name | Description |
|--------|--------|--------|
| presence | [Presence](~/api/Pidf.Presence.yml) | This is the root element of a PIDF-LO XML document |
| tuple | [Tuple](~/api/Pidf.Tuple.yml) | Container element of a status element |
| status | [Status](~/api/Pidf.Status.yml) | Container element for a geopriv element |
| geopriv | [GeoPriv](~/api/Pidf.GeoPriv.yml) | Contains the location-info element which has the actual location information. Also has elements called usage-rules, method and provided-by. The usage-rules element specifies basic policy for how the location information may be used. The method subelement specifies the type of technology used to determine the location information. The provided-by element may contain additional data about the call. |
| location-info | [locInfoType](~/api/Pidf.locInfoType.yml) | Contains civic address location and/or geodetic location information. |
| civicAddress | [CivicAddress](~/api/Pidf.CivicAddress.yml) | Contains street address, city, county, state and country of the location |
| Point | [Point](~/api/Pidf.Point.yml) | Specifies the location as a point using latitude and longitude |
| Circle | [Circle](~/api/Pidf.Circle.yml) | Describes the locaton as a circle with a latitude and longitude that specify the center and a radius in meters. |
| Ellipse | [Ellipse](~/api/Pidf.Ellipse.yml) | Specifies the location as an ellipse. |
| ArcBand | [ArcBand](~/api/Pidf.ArcBand.yml) | Specifies the location as an arc band. |
| Sphere | [Sphere](~/api/Pidf.Sphere.yml) | Specifies the location as a three dimensional sphere. |
| Ellipsoid | [Ellipsoid](~/api/Pidf.Ellipsoid.yml) | Specifies the location as a three dimensional ellipsoid. |
| Prism | [Prism](~/api/Pidf.Prism.yml) | Specifies the location as a three dimensional prism. The base of the prism is a polygon. |
| confidence | [Confidence](~/api/Pidf.Confidence.yml) | Provides a measure of the certainty or confidence that the location information is accurate. |
| Dynamic | [DynamicType](~/api/Pidf.DynamicType.yml) | Provides the heading (direction of motion), orientation (direction that the object is pointing) and elevation angle of the object whose location is provided in the location-info element |

Subelements of an element are represented as C# properties of the class associated with the element. Attributes of an element are also represented as C# properties of the class associated with the element.


# The pos Element and the Position Class
The pos element represents individual points in other location subelements of the location-info element. For example, a Point element contains a single pos element. A Circle element contains a pos element that defines the location of the center of a circle.

The pos element is defined as a string list of double precision numbers in the XML schema defination. Values are separated by a single space. The string contains latitude, longitude and potentially elevation information. A pos element for a point in two dimensions will look like:

```
<pos>42.5463 -73.2512</pos>
```

A pos element for a point in three dimensions will look like:

```
<pos>42.5463 -73.2512 100</pos>
```

The first value is the latitude, the second value is the longitude and the third value is tha altitude above sea level in meters (by default for NG9-1-1 applications).

Since this schema presents an awkward programming interface, the Pidf namespace provides a class called [Position](~/api/Pidf.Position.yml) that provides helper properties called Latitude, Longitude and Altitude. This class implements a custom serializer and deserializer (via an implementation of the System.Xml.Serialization.IXmlSerializable interface) and handles the converstion between a string list of double values and the Latitude, Longitude and Altitude properties. All geodetic element types in the location-info (i.e., Point, Circle, etc.) element use the Position class.


# Parsing a PIDF-LO XML Document
A PIDF-LO XML document may be delivered with an NG9-1-1 call as part of the SIP INVITE message body (by-value) or it may be obtained from a request to a Location Information Server (LIS) using the HELD protocol. In either case, the XML document may be received as a C# string object or converted to a C# string object. The XmlHelper class can then be used to deserialize the string into a Presence class as follows.
```
Presence presence = XmlHelper.DeserializeFromString<Presence>(strData);
```
The DeserializeFromString function will return a Presence object if the input string is a valid presence XML document. If the input string is not a valid presence XML document then this function will return null, so be sure to check if the return value is not null before attempting to use it.

If you have a need to troubleshoot problems with deserialization, you can use the following override of the DeserializeFromString function.

```
Exception Ex;
Presence presence = XmlHelper.DeserializeFromString<Presence>(strData, out Ex);
```
If the above function returns null, the value of the Ex variable will be non-null. You call Ex.ToString() to get the entire exception data including the call stack that can be written to a log file for troubleshooting purposes.

# Generating and Serializing a PIDF-LO XML Document

The following code creates a Presence object with a Tuple element.
```
Presence pres = Presence.CreateTuplePresence("12345");
pres.tuple.status.geopriv.LocationInfo.Circle = new Circle(42.5463, -73.2512, 100);

```
The Presence can then be serialized to a string containing a properly formed XML document as follows.
```
string strPres = XmlHelper.SerializePidfToString(pres);
```
The SerializePidfToString() method of the [XmlHelper](~/api/Ng911Lib.Utilities.XmlHelper.yml) class adds the XML element prefix tags appropriate to all of various namespaces the encompass the elements of a PIDF-LO XML document. The resulting XML will be as follows.
```
<?xml version="1.0" encoding="utf-8"?>
<presence xmlns:gs="http://www.opengis.net/pidflo/1.0" xmlns:gml="http://www.opengis.net/gml" xmlns:ca="urn:ietf:params:xml:ns:pidf:geopriv10:civicAddr" xmlns:gp="urn:ietf:params:xml:ns:pidf:geopriv10" xmlns:dm="urn:ietf:params:xml:ns:pidf:data-model" xmlns:gbp="urn:ietf:params:xml:ns:pidf:geopriv10:basicPolicy" xmlns:con="urn:ietf:params:xml:ns:geopriv:conf" xmlns:dyn="urn:ietf:params:xml:ns:pidf:geopriv10:dynamic" xmlns:cae="urn:ietf:params:xml:ns:pidf:geopriv10:civicAddr:ext" xmlns="urn:ietf:params:xml:ns:pidf">
  <tuple id="12345">
    <status>
      <gp:geopriv>
        <gp:location-info id="71a65425e2b54304a8551fb671cf3073" profile="geodetic-2d">
          <gs:Circle srsName="urn:ogc:def:crs:EPSG::4326">
            <gml:pos>42.5463 -73.2512</gml:pos>
            <gs:radius uom="urn:ogc:def:uom:EPSG::9001">100</gs:radius>
          </gs:Circle>
        </gp:location-info>
        <gp:usage-rules>
          <gbp:retransmission-allowed>true</gbp:retransmission-allowed>
          <gbp:retention-expiry>2023-12-13T15:18:54.6695109-08:00</gbp:retention-expiry>
        </gp:usage-rules>
      </gp:geopriv>
    </status>
    <timestamp>2022-12-13T15:18:54.665592-08:00</timestamp>
  </tuple>
</presence>
```
If you do not wish to add the element prefix tags, you can use the SerializeToString method of the XmlHelper class to get the following result.
```
<?xml version="1.0" encoding="utf-8"?>
<presence xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="urn:ietf:params:xml:ns:pidf">
  <tuple id="12345">
    <status>
      <geopriv xmlns="urn:ietf:params:xml:ns:pidf:geopriv10">
        <location-info id="71a65425e2b54304a8551fb671cf3073" profile="geodetic-2d">
          <Circle srsName="urn:ogc:def:crs:EPSG::4326" xmlns="http://www.opengis.net/pidflo/1.0">
            <pos xmlns="http://www.opengis.net/gml">42.5463 -73.2512</pos>
            <radius uom="urn:ogc:def:uom:EPSG::9001">100</radius>
          </Circle>
        </location-info>
        <usage-rules>
          <retransmission-allowed xmlns="urn:ietf:params:xml:ns:pidf:geopriv10:basicPolicy">true</retransmission-allowed>
          <retention-expiry xmlns="urn:ietf:params:xml:ns:pidf:geopriv10:basicPolicy">2023-12-13T15:18:54.6695109-08:00</retention-expiry>
        </usage-rules>
      </geopriv>
    </status>
    <timestamp>2022-12-13T15:18:54.665592-08:00</timestamp>
  </tuple>
</presence>
```

# Handling Multiple Locations
The location-info element may contain a civic address (street, city, state, etc.) and one of eight different shapes describing a geodetic location. The Point, Circle, Ellipse, ArcBand and Polygon elements describe location in two dimensions and the Sphere, Ellipsoid and Prism describe location in three dimensions. The location-info element may contain a single element describing location or it may contain two or more elements containing  location information. The XML schemas described in the RFCs do not impose any restrictions on the combination of location sub-elements within the location-info element. For instance, the location-info element might contain a civicAddress element and a Point element.

In addition, a presence XML document can have three sub-elements and each of these sub-elements can contain one or more locations. The three elements that can contain location are the tuple, device and person elements.

When an application receives a location included with a call in a SIP INVITE request, or when it receives location from a Location Information Server (LIS), it must deserialize the string into a Presence object and then it must determine what types of location are present and then it must decide which location to use (say for call routing or for display purposes).

The Presence class has the following methods that seach the Presence document for location information.

| Method Name | Returns | Description |
|--------|--------|--------|
| GetFirstCivicAddress | CivicLocation | Gets the first civic location found in the document. Returns null if no civic location is found. |
| GetFirstCivicAddressLocation | locInfoType | Gets the first locInfoType (location-info element) containing a civic address. Returns null if no civic location is available. |
| GetAllGeoPrivObjects | List of GeoPriv objects | Returns a list of all GeoPriv objects in the Presence object. Returns an empty list if there are no GeoPrive objects. |
| GetFirstGeolocation | locInfoType | Returns the first locInfoType object that has a geodetic type of location. Returns null if no geodedic information is present. |
| GetFirstGeoPriv | GeoPriv | Gets the first geopriv object. Returns null if no geodetic location is available. |
| GetFirstGeoGeoPriv | GeoPriv | Gets the first GeoPriv object that has a geodetic shape in it. Returns null if no geodetic location is available. |
| GetFirstCivicGeoPriv | GeoPriv | Gets the first GeoPriv object that contains a civic location. Returns null if no civic location information is available. |

# The Geopriv provided-by Element
RFC 4119 originally defined a sub-element of the geopriv element called provided-by. Its original purpose was to contain information about the entity or organization that provided the location information.

RFC 7852 redefined the schema of the provided-by sub-element of a geopriv element so that it can contain additional data about the call. Additional call data may be included either by-value and/or by-reference within the provided-by element. RFC 7852 defined five different types of additional data.

| Additional Data Type | Description |
|--------|--------|
| Comment | Contains human readable comments that may be provided by a data provider. |
| Device Information | Provided information about the device that originated the emergency call. |
| Provider Information | Contains information about the entity or organization that provide one or more of the other additional data blocks. |
| Service Information | Contains information about the type of service (wireline, wireless, etc.) |
| Subscriber Information | Contains information about the individual subscriber to the service and device that placed the emergency call. |

This class library supports the schema of the provided-by element from RFC 7852. This is the schema that is used in NG9-1-1 applications.

The AdditionalData namespace contains the classes for additional data.

The following figure shows a simplified example of the provided-by element.
```
<provided-by>
    <EmergencyCallDataValue>
        <EmergencyCallData.Comment></EmergencyCallData.Comment>
        <EmergencyCallData.DeviceInfo></EmergencyCallData.DeviceInfo>
        <EmergencyCallData.ProviderInfo></EmergencyCallData.ProviderInfo >
        <EmergencyCallData.ServiceInfo></EmergencyCallData.ServiceInfo>
        <EmergencyCallData.SubscriberInfo></EmergencyCallData.SubscriberInfo>
    </EmergencyCallDataValue>
    <EmergencyCallDataReference purpose="EmergencyCallData.ServiceInfo" ref="https://example.com/ref1"/>
    <EmergencyCallDataReference purpose="EmergencyCallData.ProviderInfo" ref="https://example.com/ref2"/>
    <EmergencyCallDataReference purpose="EmergencyCallData.Comment" ref="https://example.com/ref3"/>
    <EmergencyCallDataReference purpose="EmergencyCallData.ServiceInfo" ref="https://example.com/ref4"/>
    <EmergencyCallDataReference purpose="EmergencyCallData.DeviceInfo" ref="https://example.com/ref5"/>
</provided-by>
```

In the provided-by element, additional data may be provide by-value, by-reference or both. The EmergencyCallDataValue element contains additional data blocks by-value. Additional data by-reference is included in an array of EmergencyCallDataReference elements.

There will be at most one EmergencyCallDataValue element within a provided-by element. This element includes up to five elements, one for each type of additional data. Each element for a specific type of additional data is an array with possibly more than one element in the array. For example, there may be multiple EmergencyCallData.ProviderInfo sub-elements.

The EmergencyCallDataReference element contains two attributes. The purpose attribute identifies the type of additional data that it applies to (for example, EmergencyCallData.ServiceInfo). The ref attribute contains a HTTP URI that can be used to dereference (by performing an HTTP GET request) to obtain the actual data. There may be multiple elements for each type of additional data.

The following table shows the element name to class name mapping in the EmergencyCallData namespace.

| Element Name | AdditionalData Class |
|--------|--------|
| provided-by | [ProvidedByType](~/api/AdditionalData.ProvidedByType.yml) |
| EmergencyCallDataValue | [EmergencyCallDataValueType](~/api/AdditionalData.EmergencyCallDataValueType.yml) |
| EmergencyCallData.Comment | [CommentType](~/api/AdditionalData.CommentType.yml) |
| EmergencyCallData.DeviceInfo | [DeviceInfoType](~/api/AdditionalData.DeviceInfoType.yml) |
| EmergencyCallData.ProviderInfo | [ProviderInfoType](~/api/AdditionalData.ProviderInfoType.yml) |
| EmergencyCallData.ServiceInfo | [ServiceInfoType](~/api/AdditionalData.ServiceInfoType.yml) |
| EmergencyCallData.SubscriberInfo | [SubscriberInfoType](~/api/AdditionalData.SubscriberInfoType.yml) |
| EmergencyCallDataReference | [ByRefType](~/api/AdditionalData.ByRefType.yml) |

