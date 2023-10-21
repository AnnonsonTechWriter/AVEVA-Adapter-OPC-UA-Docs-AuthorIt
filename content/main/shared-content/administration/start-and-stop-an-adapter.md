---
uid: StartAndStopAnAdapter
---

# Start and stop an adapter

Complete the procedure appropriate for your operating system to start or stop an adapter service:

## Windows

1. Open Windows services.

<<<<<<< HEAD
<<<<<<< HEAD
2. Select **[!include[product-name](../_includes/inline/product-name.md)]**.
=======
1. Select **PI Adapter for \<AdapterName\>**.
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)
=======
2. Select **AVEVA Adapter for \<AdapterName\>**.
>>>>>>> 6e64e0fc7b4fd589ee7219791ac8da0fdf5a2306

3. Depending on whether your adapter is running or not, click either **Start** or **Stop**.

## Linux

1. Open command line.

2. Depending on whether your adapter is running or not, type one of the following commands:

<<<<<<< HEAD
    Example:

<<<<<<< HEAD
    _Start_ [!include[product-name](../_includes/inline/product-name.md)]
=======
 To **Start** PI Adapter for \<AdapterName\>:
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)

    ```cmdline
    sudo systemctl start aveva.adapter.opcua
=======
    _Start_ AVEVA Adapter for \<AdapterName\>

    ```cmdline
    sudo systemctl start aveva.adapter.<adapterName>
>>>>>>> 6e64e0fc7b4fd589ee7219791ac8da0fdf5a2306
    ```

<<<<<<< HEAD
    Example:

<<<<<<< HEAD
    _Stop_ [!include[product-name](../_includes/inline/product-name.md)]
=======
 To **Stop** PI Adapter for \<AdapterName\>:
>>>>>>> parent of fe84c03 (Merge pull request #5 from osisoft/main)
  
      ```cmdline
      sudo systemctl stop aveva.adapter.opcua
=======
    _Stop_ AVEVA Adapter for \<AdapterName\>
  
      ```cmdline
      sudo systemctl stop aveva.adapter.<adapterName>
>>>>>>> 6e64e0fc7b4fd589ee7219791ac8da0fdf5a2306
      ```
  
3. Press Enter.
