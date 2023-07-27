---
uid: AVEVAAdapterForOPCUAPrinciplesOfOperation
---

# Principles of operation

This adapter's operations focus on data collection and stream creation.

## Adapter configuration

For the OPC UA adapter to start data collection, configure the following:

- Data source: Provide the data source from which the adapter should collect data.
- Data selection: Select the OPC UA items to which the adapter should subscribe for data.
- Logging: Set up the logging attributes to manage the adapter logging behavior.

For more information, see [AVEVA Adapter for OPC UA data source configuration](xref:AVEVAAdapterForOPCUADataSourceConfiguration) and [AVEVA Adapter for OPC UA data selection configuration](xref:AVEVAAdapterForOPCUADataSelectionConfiguration).

## Connection

The OPC UA adapter uses the binary opc.tcp protocol to communicate with the OPC UA servers. As part of the OPC UA server and client establishing a secure connection, each one sends its X.509-type certificate to the other for verification. Upon verification of the certificates, the server and client establish a secure connection.

For more information on secure connections, see [AVEVA Adapter for OPC UA security configuration](xref:AVEVAAdapterForOPCUASecurityConfiguration).

## Data collection

The OPC UA adapter collects time-series data from selected OPC UA dynamic variables through OPC UA subscriptions (unsolicited reads). The adapter supports the Data Access (DA) and Historical Data Access (HDA) parts of OPC UA specification. For more information, see [Data Access](https://opcfoundation.org/developer-tools/documents/view/165) and [Historical Data Access](https://opcfoundation.org/developer-tools/documents/view/168).

### Data types

The following table lists OPC UA variable types that the adapter collects data from and types of streams that will be created. Types not listed below are currently unsupported. 

| OPC UA data type | Stream data type |
|------------------|------------------|
| Boolean          | Boolean          |
| SByte            | Int16            |
| Int16            | Int16            |
| UInt16           | UInt16           |
| Int32            | Int32            |
| UInt32           | UInt32           |
| Int64            | Int64            |
| UInt64           | UInt64           |
| Double           | Float64          |
| Decimal          | Float32          |  
| Float            | Float32          |
| DateTime         | DateTime         |
| UtcTime          | DateTime         |
| String           | String           |
| Number           | variable, depending on the actual value |
| Integer          | Integer          |
| UInteger         | UInteger         |
| Enumeration      | Int16            |

AVEVA Adapter for OPC UA attempts to verify the data type for each data selection item before adding the item to the subscription on the OPC UA server. Verified data selection items with supported types and data selection items for which the type cannot be verified are added to the subscription. Data selection items with unsupported data type are not added to the subscription and a message including the **NodeId** and **TypeId** is logged.

## Enumeration types

AVEVA Adapter for OPC UA supports the following enumeration types:

- MultiStateDiscreteType
- MultiStateValueDiscreteType
- TwoStateDiscreteType
- Any object that has a data type referenced as enumeration type

The adapter reads the enumeration mapping for data selection items that point to any of the enumeration types and sends these items as enums to the OMF endpoints.

## Stream creation

The OPC UA adapter creates a stream with three properties for each selected OPC UA item. The properties are described in the following table:

| Property name | Data type | Description |
|---------------|-----------|-------------|
| Timestamp     | DateTime  | Timestamp of the given OPC UA item value update. |
| Value         | Based on type of incoming OPC UA value | Value of the given OPC UA item update, which includes multiple properties in addition to the data value. **Note:** For OPC UA items that support EURange, the additional **Minimum**/**Maximum** properties in OCS and the **Zero**/**Span** properties in AVEVA Web API are populated. For OPC UA items that support EngineeringUnits, such as AnalogItem, the additional **UOM** property in OCS and the **Eng Units** property in AVEVA Web API are populated. 1  |
| Quality | Unsigned integer | Data quality of the given OPC UA item update.  |

1  **Note:** `Null` values with `Good` quality are discarded. `Null` values with `Bad` or `Questionable` quality send the default value `0` or `null` to the destination.

The OPC UA adapter sends metadata with each stream it creates. Metadata common for every adapter type are:

- **ComponentId**: Specifies the data source. For example, _OpcUa1_
- **ComponentType**: Specifies the type of adapter. For example, _OpcUa_

Metadata specific to the OPC UA adapter are:

- **BrowseName**: The browse name as provided by the OPC UA server
- **SourceId**: The NodeId provided by the OPC UA server

**Note:** A configured metadata level allows you to set the amount of metadata for the adapter. Specify the metadata level in the [General configuration](xref:GeneralConfiguration). For the OPC UA adapter, the following metadata is sent for the individual level:

- `None`: No metadata
- `Low`: AdapterType (ComponentType) and DataSource (ComponentId)
- `Medium`: AdapterType (ComponentType), DataSource (ComponentId), BrowseName, and DisplayName
- `High`: AdapterType (ComponentType), DataSource (ComponentId), BrowseName, DisplayName, and SourceId

Each stream created by  the adapter for a given OPC UA item has a unique identifier (Stream ID). If you specify a custom stream ID for the OPC UA item in data selection configuration, the OPC UA adapter uses that stream ID to create the stream. Otherwise, the adapter uses the OPC UA item node ID to construct the stream ID, as shown below.

```code
<AdapterComponentID>.<NamespaceIndex>.<Identifier>
```
NamespaceIndex refers to the number specified in the `ns` keyword in the **NodeId** parameter of the data selection configuration item. For more information, see [AVEVA Adapter for OPC UA data source configuration](xref:AVEVAAdapterForOPCUADataSourceConfiguration#opc-ua-data-source-parameters).

**Note:** The naming convention is affected by StreamPrefix and DefaultStreamIdPattern settings in the data source configuration.

## ServiceLevel and the maintenance sub-range

The OPC UA adapter will use the ServiceLevel ranges defined in the OPC UA specification in order to facilitate failover and to reduce load on a server that is in maintenance. For more information about how ServiceLevel is used to faciliate failover, see [Server Failover](#server-failover). 

When an OPC UA server's ServiceLevel indicates maintenance, the adapter will disconnect and will wait until the server's EstimatedReturnTime before trying to reconnect. If the server does not provide the adapter with an EstimatedReturnTime, then the adapter will increase the ReconnectDelay by doubling the value configured in the client settings configuration. 

## Server Failover

The OPC UA adapter supports server failover, also known as non-transparent server redundancy. To enable this feature, the `ServerFailoverEnabled` property in the adapter component's DataSource must be set to `true`. For more information on setting this property, see [PI Adapter for OPC UA data source configuration](xref:AVEVAAdapterForOPCUADataSourceConfiguration#opc-ua-data-source-parameters).

Upon successful connection to the primary OPC UA Server that is defined in the Data Source configuration, the adapter will read 3 node ID's that hold server redundancy related information:

| Node ID | How it's used |
|---------|---------------|
| `i=3709`: Server redundancy mode support | This value will be used to determine the redundancy mode the adapter will follow. Currently, the supported modes are `None`, `Cold`, `Warm`, and `Hot`. The adapter will only read this property from the primary OPC UA Server that is defined in the Data Source configuration. |
| `i=11314`: Server URI array | This value will be used to determine all of the servers in the redundancy set. This should include the primary server as well as any additional backup servers. The adapter will only read this property from the primary OPC UA Server that is defined in the Data Source configuration. **For failover to work successfully, the Server URI array must only be populated with the URL of the servers in the redundancy set.** Some OPC UA servers use URNs instead of URLs, which is not currently supported by this adapter. |
| `i=2267`: Service level | This value will be used to track each server's health and determine if a failover should occur. The adapter will subscribe to this value on every server in the redundancy set. |

**Note:** The adapter does not currently support a runtime change to the server redundancy mode or server URI array. A user must restart the adapter if they wish to change either the server redundancy mode or the server URI array.

### Supported Redundancy Modes

The following sections outline how the adapter behaves in each Server Redundancy Mode. For more information on server redundancy, see the [OPC UA Online Reference part 4 - 6.6.2](https://reference.opcfoundation.org/v104/Core/docs/Part4/6.6.2/)

#### None

If the Server Redundancy Mode Support value on the primary OPC UA server is `None`, the adapter will operate as if server failover is not enabled. The adapter will not attempt to connect or failover to any backup servers.

#### Cold

If the servers in the redundancy set are operating in Cold mode, the adapter will take the following steps upon startup:

* Make a connection to each server defined in the primary OPC UA server's Server URI Array.
* Read the Service Level of each server to determine which server is the healthiest.
* Disconnect from all servers except for the healthiest.

The adapter will only maintain a connection with a single OPC UA server after its initial startup. The secondary servers will not be connected to unless a server failover occurs.

In Cold server failover, a failover only occurs if the adapter loses connection to the primary server or if the primary server's Service Level drops to 1 or 0. When this occurs, the adapter will connect to all secondary servers, read the service level of each, then disconnect from all servers except for the one with the highest service level. The adapter will maintain a connection with this new server until another failover event occurs.

#### Warm

If the servers in the redundancy set are operating in Warm mode, the adapter will take the following steps upon startup:

* Make a connection to each server defined in the primary OPC UA server's Server URI Array.
* Read the Service Level of each server to determine which server is the healthiest.
* Activate publishing and sampling for the data subscription on the healthiest server.
* Disable publishing and sampling for the data subscription on the rest of the servers.

The adapter will maintain a connection to every OPC UA server in the redundancy set. However, publishing and sampling will only be active on the healthiest server.

In Warm server failover, a failover occurs if the adapter loses connection to the primary server or if the primary server's Service Level drops below 200. When this occurs, the server is eligible for failover. Whenever any of the secondary servers have a higher service level than the current primary, a failover will occur. At this point, the adapter will disable publishing and sampling on the current primary server and enable them on whichever secondary server has the highest service level. This healthy server becomes the new primary.

#### Hot

If the servers in the redundancy set are operating in Hot mode, the adapter will take the following steps upon startup:

* Make a connection to each server defined in the primary OPC UA server's Server URI Array.
* Read the Service Level of each server to determine which server is the healthiest.
* Activate publishing and reporting for the data subscription on the healthiest server.
* Disable publishing and activate sampling for the data subscription on the rest of the servers.

The adapter will maintain a connection to every OPC UA server in the redundancy set. However, the secondary servers will have their data subscriptions set to Sampling only. This means the data from these servers will not be sent to the adapter. Instead, it will be held in the buffers that the server maintains for each monitored item until it becomes the primary server. The size of this buffer can be configured with the `MonitoredItemQueueSize` property in the [Client Settings](xref:AVEVAAdapterForOPCUAClientSettingsConfiguration) configuration. Ensure that the buffer size is set to an appropriate amount so that data will not be lost during a server failover in Hot mode. This means that the buffer has to be large enough to hold all of the samples of data during a publishing interval, and that it is configured to have extra space to hold additional samples in the case of a failover event.

In Hot server failover, a failover occurs if the adapter loses connection to the primary server or if the primary server's Service Level drops below 200. When this occurs, the server is eligible for failover. Whenever any of the secondary servers have a higher service level than the current primary, a failover will occur. At this point, the adapter will disable publishing and activate sampling on the current primary server. It will then enable publishing and reporting on whichever secondary server has the highest service level. This healthy server becomes the new primary. If the `MonitoredItemQueueSize` property in the Client Settings configuration is large enough to hold all of the data that occurred during the failover period, there will be no data loss.

### Redundancy Server Set Cache

On startup, when the adapter connects to the initial primary server and reads the Server URI array, it will store the results in a json file in the following directories:

* Windows: `%ProgramData%\OSIsoft\Adapters\OpcUa\Configuration\<ComponentId>_RedundantServerSet.json`
* Linux: `/usr/share/OSIsoft/Adapters/OpcUa/Configuration/<ComponentId>_RedundantServerSet.json`

On startup, if the adapter is unable to connect to the primary server defined in the Data Source configuration, it will attempt to connect to each server in the cached server redundancy set. If the adapter is able to connect to one of the cached servers, it will then read and use the redundancy set configured on that server. The current redundancy set configuration can be found by following the steps in the [Retrieve Redundant Server Set](xref:RetrieveRedundancySet) section.

## Client Failover
The OPC UA adapter also supports client failover. Two adapters can be configured as part of a redundant group, so that, in the event of a connection loss to the failover endpoint or data source, the secondary adapter may take the place of the primary. There are three client failover modes that the adapters can be configured to use: cold, warm and hot. These modes are detailed below. For more information about configuring client failover see [Client failover configuration](xref:AVEVAAdapterForOPCUAClientSettingsConfiguration). 

### Cold
If the adapters are operating in cold mode, the secondary adapter is configured but not started. Once a failover event occurs, the secondary will become primary and will then begin to collect and egress data.

### Warm 
When the adapters are configured in warm mode, the component is started and connected to the data source, but it is not collecting or egressing any data. Once a failover event occurs, the secondary will become primary and begin to collect and egress data.

### Hot
In hot mode, both adapters are configured and started. They both collect and buffer data, but only the primary egresses data to the endpoint. When the secondary adapter becomes primary, it will send its buffered data to the endpoint.
