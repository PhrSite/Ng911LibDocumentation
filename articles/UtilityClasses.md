# Introduction
The Ng911Lib.Utilities namespace contains several classes to assist in serializing and deserializing XML and JSON data.
## XmlHelper
The [XmlHelper](~/api/Ng911Lib.Utilities.XmlHelper.yml) class is a static class that provides methods for serialization and deserialization of XML data.

The following methods of the XmlHelper class are recommended for general purpose deserialization of C# objects from strings containing XML documents.

```
public static T DeserializeFromString<T>(string str);
public static T DeserializeFromString<T>(string str, out Exception Excpt);
```

For example, to convert an XML string containing a Subscriber Information block of additional data, use the following method.

~~~
using Ng911Lib.Utilities;
...

SubscriberInfoType subInfo = XmlHelper.DeserializeFromString<SubscriberInfoType>(strSubInfo);
~~~

Both of the deserialization methods catch all exceptions and return a value of null if an exception occurs, so be sure to check the return value before using it.

The override of the DeserializeFromString that takes an Exception object as an output parameter can be used for trouble shooting and logging purposes. If this method returns a null value, then the Excpt output parameter will not be null. You can call the ToString() method of the Excpt parameter to get the details of the problem that was encountered.

The following method can be called to serialize any C# object to a string containing an XML document.

```
public static string SerializeToString(object Obj);
```

This method will return a value of null if an exception occurred.

## JsonHelper
The [JsonHelper](~/api/Ng911Lib.Utilities.JsonHelper.yml) class is a static class that provides for serialization and deserialization of JSON data. This classes uses functions from the System.Text.Json and the System.Text.Json.Serialization namespaces.

The following methods of the JsonHelper class can be used for general purpose deserialization of C# objects from strings containg JSON data.

```
public static T DeserializeFromString<T>(string str);
public static T DeserializeFromString<T>(string str, out Exception Excpt);
```

These methods operate in the same manner as the corresponding methods of the XmlHelper class. See above.

The following method can be called to serialize any C# object to a string containing a JSON document.

```
public static string SerializeToString(object Obj);
```

This method returns a value of null if an exception occurred.

## The PurposeTypes Class
In NG9-1-1 applications, the SIP Call-Info header is used to pass information about additional data and other types of data passed with a SIP INVITE request. The purpose parameter of the Call-Info header indicates the type, or the schema of the data being passed. For example:

```
Call-Info: https://www.example.com/23sedde3; purpose="EmergencyCallData.ProviderInfo"
```

The [PurposeTypes](~/api/Ng911Lib.Utilities.PurposeTypes.yml) class provides constant string tokens for each schema that will be passed in the purpose parameter of a Call-Info header.

## The ContentTypes Class
The [ContentTypes](~/api/Ng911Lib.Utilities.ContentTypes.yml) class provides constant string tokens for each MIME type passed in a Content-Type SIP or HTML request or response that will be used in NG9-1-1 applications.




