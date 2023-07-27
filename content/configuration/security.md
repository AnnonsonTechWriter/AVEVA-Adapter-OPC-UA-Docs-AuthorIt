---
uid: AVEVAAdapterForOPCUASecurityConfiguration
---

# Security

The OPC UA security standard is concerned with the authentication of client and server applications, the authentication of users, and confidentiality of their communication. Because the security model relies heavily on Transport Level Security (TLS) to establish a secure communication link with an OPC UA server, each client, including the adapter, must have a digital certificate deployed and configured. Certificates uniquely identify client applications and machines on servers, and allow for creation of a secure communication link when trusted on both sides.

The adapter generates a self-signed certificate when the first secure connection attempt is made. The adapter's certificates and those of the server are stored in the certificate store, which is shared between all adapter instances.

The adapter can be configured to use a custom certificate by adding the certificate and private key to the adapter's own certificate store and adding a configuration file to the adapter configuration directory.

When determining OPC UA security practices with regards to REST APIs, you should consider the following practice. To keep the adapter secure, only administrators should have access to machines where the adapter is installed. REST APIs are bound to localhost, meaning that only requests coming from within the machine will be accepted.

<<<<<<< HEAD
## Adapter Self-Signed Certificate

OPC UA connections may use certificates for identification and encryption. If needed, the OPC UA adapter will generate a self-signed certificate. The generated certificate will expire 10 years from the date of generation.

When the adapter certificate approaches expiration, warnings will be logged daily starting 30 days prior to expiration. After the certificate is expired, errors will be logged daily.

To generate a new self-signed certificate, move or delete the `OpcUa\Certificates\own` directory and restart the adapter.

### Configure OPC UA adapter security using the generated self-signed certificate

Complete the following steps to configure adapter security:

1. In your data source configuration, set `UseSecureConnection` to **true**. For more information, see [AVEVA Adapter for OPC UA data source configuration](xref:AVEVAAdapterForOPCUADataSourceConfiguration).
=======
## Configure OPC UA adapter security

Complete the following steps to configure adapter security:

1. In your data source configuration, set `UseSecureConnection` to **true**. For more information, see [PI Adapter for OPC UA data source configuration](xref:PIAdapterForOPCUADataSourceConfiguration).
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)

   The adapter verifies whether the server certificate is present in the [adapter trusted certificates](#adapter-trusted-certificates) and hence trusts it. In case the certificates were not exchanged before the first attempted connection, the adapter persists the server certificate within the [adapter rejected certificates](#adapter-rejected-certificates) folder. The following warning message about the rejected server certificate will be printed:

   ```bash
   ~~2019-09-08 11:45:48.093 +01:00~~ [Warning] Rejected Certificate: "DC=MyServer.MyDomain.int, O=OSIsoft, CN=Simulation
   ```

2. Manually move the server certificate from the [Adapter rejected certificates](#adapter-rejected-certificates) location to the [Adapter trusted certificates](#adapter-trusted-certificates) location using a file explorer or command-line interpreter.

   Linux example using command-line:

   ```bash
   sudo mv /usr/share/OSIsoft/Adapters/OpcUa/Certificates/rejected/certs/'SimulationServer [F9823DCF607063DBCECCF6F8F39FD2584F46AEBB].der' /usr/share/OSIsoft/Adapters/OpcUa/Certificates/trusted/certs/
   ```

   **Note:** Administrator or root privileges are required to perform this operation.

   Once the certificate is in the adapter trusted certificates folder, the adapter trusts the server and the connection attempt proceeds in making the connection call to the configured server.
  
3. Add the [certificate of the adapter](#certificate-of-the-adapter) to the server's trust store.

   The connection succeeds only when the adapter certificate is trusted on the server side. <br> For more details on how to make a client certificate trusted, see your OPC UA server documentation. <br> In general, servers work in a similar fashion to the clients, hence you can take a similar approach for making the client certificate trusted on the server side.

   When certificates are mutually trusted, the connection attempt succeeds and the adapter is connected to the most secure endpoint provided by the server.

## Adapter Custom Certificate

If desired, the adapter can be configured to use a custom certificate for security instead of the auto-generated self-signed certificate. A certificate, private key, and configuration file are necessary to configure the adapter. **It is important that the `SubjectName` and `ApplicationUri` parameters in the configuration file match the `Subject` and `SubjectAlternativeName` URL line in the certificate details.** For an example on how to create these files on a Ubuntu machine see [Generate a custom certificate and configuration file example](#generate-a-custom-certificate-and-configuration-file-example).

### Configure OPC UA adapter security using a custom certificate

Follow the steps below to configure the adapter using a custom certificate:

1. In your data source configuration, set `UseSecureConnection` to **true**. For more information, see [AVEVA Adapter for OPC UA data source configuration](xref:AVEVAAdapterForOPCUADataSourceConfiguration).

2. If the adapter has not connected to a data source previously using a secure connection, then the certificate folder locations will not have been generated. To do this, set the `endpointURL` data source parameter to **"opc.tcp://dummyserver"** as shown below. This step can be skipped if the certificate folder locations have been generated previously.
   ```json
   {
      "endpointURL": "opc.tcp://dummyserver",
      "useSecureConnection": true
   }
   ```

3. Delete the existing auto-generated public and private certificates in [certificate of the adapter location](#certificate-of-the-adapter).

4. Add the custom **.der** file and the **.pfx** certificate files to the [certificate of the adapter location](#certificate-of-the-adapter) public and private locations, respectively.

5. Add the **Application_Certificate.json** configuration file to the component configuration directory.

5. The adapter must be restarted for the change to take effect.

6. Repost the data source using the address for the primary server.
   ```json
   {
      "endpointURL": "opc.tcp://<IP-Address>:<Port>/<TestOPCUAServer>",
      "useSecureConnection": true
   }
   ```

7. Manually move the server certificate from the [Adapter rejected certificates](#adapter-rejected-certificates) location to the [Adapter trusted certificates](#adapter-trusted-certificates) location using a file explorer or command-line interpreter.

   Linux example using command-line:

   ```bash
   sudo mv /usr/share/OSIsoft/Adapters/OpcUa/Certificates/rejected/certs/'SimulationServer [F9823DCF607063DBCECCF6F8F39FD2584F46AEBB].der' /usr/share/OSIsoft/Adapters/OpcUa/Certificates/trusted/certs/
   ```

   **Note:** Administrator or root privileges are required to perform this operation.

   Once the certificate is in the adapter trusted certificates folder, the adapter trusts the server and the connection attempt proceeds in making the connection call to the configured server.
  
8. Add the [certificate of the adapter](#certificate-of-the-adapter) to the server's trust store.

### Generate a custom certificate and configuration file example

Follow the steps below to create the certificate, private key, and configuration file on a Ubuntu machine:

1. Install openssl
   ```bash
   sudo apt install openssl
   ```

2. Create a folder named **cert**. This will hold all of files necessary for generating a certificate, private key, and configuration file.

3. Add the file **config.txt** shown below to **cert**. The values in this file can be changed to match the need of the application. 
   ```
   [ req ]
   default_md = sha256
   prompt = no
   req_extensions = req_ext
   distinguished_name = req_distinguished_name

   [ req_distinguished_name ]
   commonName = TestCertificate
   countryName = US
   organizationName = Aveva
   DC = TestMachine

   [ req_ext ]
   keyUsage=critical,digitalSignature,keyEncipherment,nonRepudiation,dataEncipherment,keyCertSign
   extendedKeyUsage=critical,serverAuth,clientAuth
   basicConstraints=critical,CA:false
   subjectAltName = @alt_names

   [ alt_names ]
   URI.0 = urn:TestMachine:Aveva:OpcUa
   DNS.0 = TestMachine
   ```

4. Add the file **generate.sh** shown below to **cert**.
   ```shell
   #delete existing certs
   rm -rf out
   mkdir out

   #generate the RSA private key
   openssl genpkey -outform PEM -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out out/priv.key

   #create the CSR
   openssl req -new -nodes -key out/priv.key -config config.txt -nameopt utf8 -utf8 -out out/cert.csr

   #self-sign your CSR
   openssl req -x509 -nodes -in out/cert.csr -days 1826 -key out/priv.key -config config.txt -extensions req_ext -nameopt utf8 -utf8 -out out/cert.crt

   #make PFX
   openssl pkcs12 -export -out out/cert.pfx -inkey out/priv.key -in out/cert.crt

   #Export binary cert format
   openssl x509 -outform der -in out/cert.crt -out out/cert.der
   ```

5. Run the **generate.sh** script to create the **.der** and **.pfx** files. They will be located within the created **out** folder in **cert**.

6. Add the **Application_Certificate.json** shown below to **cert**. If the values in the `req_distinguished_name` and `alt_names` sections were changed, then the `SubjectName` and `ApplicationUri` need to be updated to match the changes generated in the certificate.
   ```json
   {
    "SubjectName": "CN=TestCertificate, C=US, O=Aveva, DC=TestMachine",
    "ApplicationUri": "urn:TestMachine:Aveva:OpcUa"
   }
   ```

## Certificate locations

### Adapter rejected certificates

Windows:

```filepath
%programdata%\OSIsoft\Adapters\OpcUa\Certificates\rejected\certs
```

Linux:

```filepath
/usr/share/OSIsoft/Adapters/OpcUa/Certificates/rejected/certs
```

### Adapter trusted certificates

Windows:

```filepath
%programdata%\OSIsoft\Adapters\OpcUa\Certificates\trusted\certs
```

Linux:

```filepath
/usr/share/OSIsoft/Adapters/OpcUa/Certificates/trusted/certs
```

### Certificate of the adapter

Windows:

```filepath
%programdata%\OSIsoft\Adapters\OpcUa\Certificates\own\certs
```

Linux:

```filepath
/usr/share/OSIsoft/Adapters/OpcUa/Certificates/own/certs
```

**Note:** Access to the private key of the adapter requires administrator permissions. The location of the private key is the following:

   Windows:

   ```filepath
   %programdata%\OSIsoft\Adapters\OpcUa\Certificates\own\private
   ```

   Linux:

   ```filepath
   /usr/share/OSIsoft/Adapters/OpcUa/Certificates/own/private
   ```
