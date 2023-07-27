---
uid: StartAndStopAnAdapter
---

# Start and stop an adapter

Complete the procedure appropriate for your operating system to start or stop an adapter service:

## Windows

1. Open Windows services.

<<<<<<< HEAD
2. Select **[!include[product-name](../_includes/inline/product-name.md)]**.
=======
1. Select **PI Adapter for \<AdapterName\>**.
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)

3. Depending on whether your adapter is running or not, click either **Start** or **Stop**.

## Linux

1. Open command line.

2. Depending on whether your adapter is running or not, type one of the following commands:

<<<<<<< HEAD
    Example:

    _Start_ [!include[product-name](../_includes/inline/product-name.md)]
=======
 To **Start** PI Adapter for \<AdapterName\>:
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)

    ```cmdline
    sudo systemctl start aveva.adapter.opcua
    ```

<<<<<<< HEAD
    Example:

    _Stop_ [!include[product-name](../_includes/inline/product-name.md)]
=======
 To **Stop** PI Adapter for \<AdapterName\>:
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)
  
      ```cmdline
      sudo systemctl stop aveva.adapter.opcua
      ```
  
3. Press Enter.
