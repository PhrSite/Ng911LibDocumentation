# NG9-1-1 Event Logging
The following documents specify events that must be logged by NG9-1-1 functional elements.

>[NENA i3 Standard for Next Generation 9-1-1](https://cdn.ymaws.com/www.nena.org/resource/resmgr/standards/nena-sta-010.3b-2021_i3_stan.pdf) (NENA-STA-010.3b-2021).
>
> [NENA Standard for the Conveyance of Emergency Incident Data Objects (EIDOs) between Next Generation (NG9-1-1) Systems and Applications](https://cdn.ymaws.com/www.nena.org/resource/resmgr/standards/nena-sta-024.1a-2023_eidocon.pdf) (NENA-STA-024.1a-2023).

The [loggingservice.yaml](https://github.com/NENA911/Logging-Service/blob/main/loggingservice.yaml) file in the NENA/Logging-Service GitHub repository specifies the JSON schema definitions for NG9-1-1 log events.

The I3V3.LogEvents namespace contains model classes for all of the log events specified in the above standards.

The I3V3.LoggingHelpers namespace provides two classes that allow applications to easily send NG9-1-1 log events to logging servers. The I3LogEventClient class provides a client interface to a single logging server. The I3LogEventClientMgr class provides a single client interface to multiple logging servers. NENA-STA-010.3b-2021 specifies that all functional elements must be able to send log events to at least two loggers so it is best to use the I3LogEventClientMgr class.
# The I3LogEventClient Class
The [I3LogEventClient](~/api/I3V3.LoggingHelpers.I3LogEventClient.yml) class provides the functionality of an NG9-1-1 log event client. This class manages a permanent connection to a single log event server. Log events are queued and sent synchronously to the log event server in a permament worker thread.

This class periodically polls the log event server by sending a request to get the version information from the server. The polling period is five seconds.

The I3LogEventClient has two events that can be used to notify the user of this class when an error occurs or when the status of the connection to the logging server changes.

To use this class, create an instance of it, hook the events and then call the Start() method as follows.
```
using I3V3.LogEvents;
using I3V3.LoggingHelpers;
using System.Security.Cryptography.X509Certificates;

namespace MyNamespace;

public class MyClass
{

    public I3LogEventClient SetupLoggingClient()
    {
        string strUrl = "https://192.168.1.79:8080";  // For example
        X509Certificate2 clientCert = // Get the client certificate or set to null
        I3LogEventClient Client1 = new I3LogEventClient(strUrl, "LoggingServer1", clientCert);

        // Hook the events if desired
        Client1.LoggingServerStatusChanged += OnLoggingServerStatusChanged;
        Client1.LoggingServerError += OnLoggingServerError;

		// Start the logging client
        Client1.Start();
        return Client1;
    }

    private static void OnLoggingServerStatusChanged(string strLoggerName, bool Responding)
    {
        // Do something with the new status such as to log an error if Responding is false.
    }

	private void OnLoggingServerError(HttpResults Hr, string strLoggerName, string strLogEvent)
    {
    	// Do something with the error information such as log it.
    }
}
```

Keep a reference to the new logging client (Client1 in this example) so that you can use it to send log events to the logging server.

## Sending Log Events
Call the SendLogEvent() method to send a log event to the logging server.
```
    CallStartLogEvent Csle = new CallStartLogEvent();
    // Set the properties of the CallStartLogEvent object and its base class (LogEvent)
    Client1.SendLogEvent(Csle);
```
The SendLogEvent() method queues the log event and returns immediately.

This method is thread-safe so that it may be called from any thread in the application.

## Handling the Events
### The LoggingServerStatusChanged Event
The background worker thread of the I3LogEventClient class polls the logging server every five seconds by requesting the version information from the server. If the logging server fails to respond, then the I3LogEventClient fires the LoggingServerStatusChanged event.

The delegate declaration for this event is:I3LogEventClient Client1 = new I3LogEventClient(strUrl, "LoggingServer1", clientCert);
```
public delegate void LoggingServerStatusChangedDeletate(string strLoggerName, bool Responding);
```
The background worker thread continues to poll the logging server even if Responding is false. When the logging server responds to a versions request, the background worker thread fires the LoggingServerStatusChanged event again, this time with Responding set to true. Hence, the maximum frequency that the application will receive this event is once every five seconds.

When Responding is false, the I3LogEventClient class stops sending log events to the logging server. It also stops queuing log events when SendLogEvent() is called. The purpose of this behavior is to prevent the queue from consuming a large amount of memory over long periods of operation when a logging server is down or not functioning properly.

### The LoggingServerError Event
The I3LogEventClient class sends log events to the logging server using a HTTP POST request. The I3LogEventClient class fires the LoggingServerError event if an error occurs such as a timeout or if the logging server responds with an error code.

The application can use the information sent to the event handler to log the error.

The delegate definition of the LoggingServerError event is:
```
public delegate void I3LoggingErrorDelegate(HttpResults Hr, string strLoggerName, string strLogEvent);
```
The [HttpResults](~/api/HttpUtils.HttpResults.yml) class contains information about the error or the response that was received from the logging server.

## Getting the Logging Server's Supported Schema Versions
An application can use the GetVersionsArrayAsync() method of the I3LogEventClient class if it needs to find out which log event schema versions a logging server supports.

```
I3LogEventClient Client1 = new I3LogEventClient(strUrl, "LoggingServer1", clientCert);
VersionsArrayType Vat = await Client1.GetVersionsArrayAsync();
// Do something with the version information.
...
Client1.Start();
```
The [VersionsArrayType](~/api/Ng911Common.VersionsArrayType.yml) class contains a list of the schema versions that the logging server supports.

The GetVersionsArrayAsync() method must be called before calling the Start() method. Ths I3LogEventClient class will throw an InvalidOperationException if it is called after calling Start().

The GetVersionsArrayAsync() method returns null if the logging server does not respond to the request to get the version information or if it returns an error code.

# The I3LogEventClientMgr Class
The [I3LogEventClientMgr](~/api/I3V3.LoggingHelpers.I3LogEventClientMgr.yml) class allows applications to manage sending NG9-1-1 log events to multiple log event servers. The NENA NG9-1-1 Standard (NENA-STA-010.3b-2021) states that all functional elements that log events must support logging to at least two log event servers so this is the class that most applications should use.

Follow these steps when using this class.
1. Create an instance of it.
2. Create an I3LogEventClient object for each logging server
3. Add the I3LogEventClient class to the list of clients that the I3LogEventClientMgr will manage by calling the AddLoggingClient() method.
4. Hook the events of the I3LogEventClientMgr class
5. Call the Start() method of the I3LogEventClientMgr class

For example:
```
using I3V3.LogEvents;
using I3V3.LoggingHelpers;
using System.Security.Cryptography.X509Certificates;

namespace MyNamespace;

public class MyClass
{

    public I3LogEventClientMgr SetupLoggingClients()
    {
        string strUrl1 = "https://192.168.1.79:8080";  // For example
        string strUrl2 = "https://192.168.1.80:8080";

        X509Certificate2 clientCert = // Get the client certificate or set to null
        I3LogEventClient Client1 = new I3LogEventClient(strUrl1, "LoggingServer1", clientCert);
        I3LogEventClient Client2 = new I3LogEventClient(strUrl2, "LoggingServer2", clientCert);

        I3LogEventClientMgr ClientMgr = new I3LogEventClientMgr();
        ClientMgr.AddLoggingClient(Client1);
        ClientMgr.AddLoggingClient(Client2);

        // Hook the events if desired
        ClientMgr.LoggingServerStatusChanged += OnLoggingServerStatusChanged;
        ClientMgr.LoggingServerError += OnLoggingServerError;

        // Start all logging clients
        ClientMgr.Start();
        return ClientMgr;
    }

    private static void OnLoggingServerStatusChanged(string strLoggerName, bool Responding)
    {
        // Do something with the new status such as to log an error if Responding is false.
    }

	private void OnLoggingServerError(HttpResults Hr, string strLoggerName, string strLogEvent)
    {
    	// Do something with the error information such as log it.
    }
}

```
Hold on to a reference to the I3LogEventClientMgr object and use it for logging NG9-1-1 events. To log an I3V3 event, call the SendLogEvent() method of the I3LogEventClientMgr class and it will send the event to each of the logging servers.
