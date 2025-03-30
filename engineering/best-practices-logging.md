# Event Logging Best Practices

- **Include a human readable message on every line**
- **Use clear key-value pairs**: One of the most powerful features of
  the Splunk platform is its ability to extract fields from events
  when you search, creating structure out of unstructured data. To
  make sure field extraction works as intended, use the following
  string syntax (using spaces and commas is fine). If your values
  contain spaces, wrap them in quotes (for example, username="bob
  smith").
```
key1=value1, key2=value2, key3=value3 . . .
```
- **Create events that humans can read**: Avoid using complex encoding
  that would require lookups to make event information
  intelligible. For example, if logs are in a binary format, provide
  tools to easily convert them to a human-readable(ASCII)
  format. Don't use a format that requires an arbitrary code to
  decipher it. And, don't use different formats in the same file—split
  them out into individual files instead.
- **Use timestamps for every event**:  The correct time is critical to understanding the proper sequence of events. Timestamps are critical for debugging, analytics, and deriving transactions. The Splunk platform will automatically timestamp events that don't include them using a number of techniques, but it's best that you do it.
- **Use the most verbose time granularity possible**: Put the timestamp at the beginning of the line. The farther you place a timestamp from the beginning, the more difficult it is to tell it's a timestamp and not other data.
    - Include a four-digit year.
    - Include a time zone, preferably a GMT/UTC offset.
    - Time should be rendered in microseconds in each event. The event could become detached from its original source file at some point, so having the most accurate data about an event is ideal.
- **Use unique identifiers (IDs)**: Unique identifiers such as transaction IDs and user IDs are tremendously helpful when debugging, and even more helpful when you are gathering analytics. Unique IDs can point you to the exact transaction. Without them, you might only have a time range to use. When possible, carry these IDs through multiple touch points and avoid changing the format of these IDs between modules. That way, you can track transactions through the system and follow them across machines, networks, and services.

Log in text format
Avoid logging binary information because the Splunk platform cannot meaningfully search or analyze binary data. Binary logs might seem preferable because they are compressed, but this data requires decoding and won't segment. If you must log binary data, place textual meta-data in the event so that you can still search through it. For example, don't log the binary data of a JPG file, but do log its image size, creation tool, username, camera, GPS location, and so on.

Use developer-friendly formats
Developers like the ability to receive a stream of data over HTTP/S when possible, and with data structured so that it can be easily processed.

Developer-friendly formats like JavaScript Object Notation (JSON) are readable by humans and machines. Searches through structured data are even easier with the spath search command. Structured data formats can be easily parsed by most programming languages right in your browser, often with no server needed. Using structured key-value pairs is great, especially when a hierarchy is needed or when objects have members, like the following:

{ "sender" : "michael" "recipient": { "name" : "michael", "name" : "andrea", "name" : "itay" } subject:"I heart logs" }
Here, JSON and even XML are right at home. However, XML can be a bit more noisy for humans to read than something as clean as JSON.

For some interesting reading about logging in JSON, read this entry in Paul's Journal.

Log more than just debugging events
Put semantic meaning in events to get more out of your data. Log audit trails, what users are doing, transactions, timing information, and so on. Log anything that can add value when aggregated, charted, or further analyzed. In other words, log anything that is interesting to the business.

Use categories
Categorize the event. For example, use the severity values INFO, WARN, ERROR, and DEBUG.

Identify the source
Include the source of the log event, such as the class, function, or filename.

Keep multi-line events to a minimum
Multi-line events generate a lot of segments, which can affect indexing and search speed, as well as disk compression. Consider breaking multi-line events into separate events.

Operational best practices
These operational best practices apply to the way you do logging:

Log locally to files
If you log to a local file, it provides a local buffer and you aren't blocked if the network goes down.

Use Splunk forwarders
Use Splunk forwarders to help log data. Forwarders collect logging data and then send this information to the indexers.

Use rotation policies
Logs can take up a lot of space. Maybe compliance regulations require you to keep years of archival storage, but you don't want to fill up your file system on your production machines. So, set up good rotation strategies and decide whether to destroy or back up your logs.

Collect events from everything, everywhere
Collect all the data you can, because the more data you capture, the more visibility you have. For example, collect these when you can:

Application logs
Database logs
Network logs
Configuration files
Performance data (iostat, vmstat, ps, etc.)
Anything that has a time component
Using Splunk universal forwarders, you can access log events that are saved to files and broadcast over network ports. But you aren't limited to files or streams. If you have log data that is buried in an application, device, or system, you can get to the data if you make it accessible via a transport, protocol, or API. Here are some examples of liberating your log data:

HTTP: For an easy way to access data, provide standard HTTP GET or HTTP POST access to log events. And if you return an ID with each request, it's easier to determine which events are new. For an example servlet that can retrieve this information, see Log POST or GET request parameters on Splunkbase.
Queues: Publish log events to accessible queues. Then, you can create clients to take these events and index them within the Splunk platform. Examples of well-known queuing mechanisms are JMS and MQ Series. For more on this, see Nimish's post Indexing data into Splunk remotely on Splunk's Dev blog.
Multicast: As a way to collect heartbeats, listen on a multicast port for events. The lack of events for a threshold of time may indicate that there is something wrong with the infrastructure, and then you can set up an alert within the Splunk platform to take corrective action. For an example implementation, see Nimish's post Indexing events delivered by multicast on Splunk's Tips & Tricks blog.
Web service: Use a web service to expose log events or time-series data. The web service should be accessible, provide a Web Services Description Language (WSDL), and include examples showing how to use it. For more discussion about this, see Nimish's post Splunk as a SOA consumer on Splunk's Tips & Tricks blog.
Database: To get data out of a database, provide a transport and use a credential-based API. The simplest example is having the data within a relational database management system (RDBMS), using something like Java Database Connectivity (JDBC) to access the data. You also need the name of the table and the minumum parameters for JDBC access.
Do not log sensitive data
Do not log sensitive data or personally identifiable information (PII), including social security numbers and credit card numbers. Logging sensitive data creates the risk of this information being used maliciously.

If you must log sensitive data, encrypt this data at the source. You can use an encryption tool to mask sensitive values prior to logging. Encrypting sensitive data allows you to discover correlations between events without jeopardizing security.
