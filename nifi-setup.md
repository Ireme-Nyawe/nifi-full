# Apache NiFi Setup on Windows --- `nifi-setup`

This guide walks through installing **Java JDK 21**, downloading the
**Apache NiFi binary distribution**, configuring NiFi to use Java 21,
starting NiFi, and opening its web interface in a browser.

The steps use the following example setup:

-   Operating system: Windows 10/11
-   Apache NiFi: **2.12.0**
-   Java: **JDK 21**
-   NiFi installation folder: `C:\nifi\nifi-2.12.0`
-   Web interface: `https://localhost:8443/nifi`

> **Important:** Download the file named `nifi-2.12.0-bin.zip`. Do not
> download `nifi-2.12.0-source-release.zip`. The source archive contains
> project source code and will not provide the ready-to-run `bin` folder
> expected by these steps.

Official references: - NiFi downloads:
https://nifi.apache.org/download/ - NiFi 2.12.0 binary directory:
https://dlcdn.apache.org/nifi/2.12.0/ - Official getting-started guide:
https://nifi.apache.org/nifi-docs/getting-started.html

------------------------------------------------------------------------

## 1. Install Java JDK 21

Apache NiFi 2.x requires Java 21. A JDK installation includes the Java
runtime needed to launch NiFi.

### 1.1 Download the JDK

1.  Open the Eclipse Adoptium website: https://adoptium.net/
2.  Choose **Temurin 21 (LTS)** for Windows.
3.  Select the correct architecture for your PC, usually **Windows
    x64**, and download the JDK installer (`.msi`).
4.  Open the downloaded installer.
5.  Follow the installation wizard and complete the installation.

You can also use an existing JDK 21 installation if it is already
installed.

### 1.2 Find the installation folder

A typical folder might be:

``` text
C:\Program Files\Eclipse Adoptium\jdk-21...
```

In this environment, the existing JDK 21 path has been:

``` text
C:\Program Files\Java\jdk-21.0.12.1
```

Use the actual folder on your PC. The folder assigned to `JAVA_HOME`
must contain directories such as `bin` and files such as `bin\java.exe`.

### 1.3 Verify Java

Open a **new Command Prompt** and run:

``` bat
java -version
```

Then check the compiler:

``` bat
javac -version
```

These commands should report Java 21. If they show another version, do
not assume NiFi will use Java 21 automatically. We will configure NiFi
to use the correct JDK directly in Step 4.

To inspect where Windows finds Java, run:

``` bat
where java
```

Windows may have more than one Java installation. That is okay as long
as NiFi is configured to use JDK 21.

------------------------------------------------------------------------

## 2. Download the Apache NiFi binary ZIP

### 2.1 Open the official download directory

Open this official Apache directory:

https://dlcdn.apache.org/nifi/2.12.0/

Download:

``` text
nifi-2.12.0-bin.zip
```

The binary ZIP is large, so allow time for the download to finish.

You may also download the corresponding checksum file:

``` text
nifi-2.12.0-bin.zip.sha512
```

Apache recommends verifying release downloads. For a basic Windows
checksum check, open Command Prompt in the download folder and run:

``` bat
certutil -hashfile nifi-2.12.0-bin.zip SHA512
```

Compare the displayed hash with the contents of the official `.sha512`
file. They should match exactly.

### 2.2 Know which file to choose

-   **Correct:** `nifi-2.12.0-bin.zip` --- ready-to-run binary
    distribution.
-   **Not for this installation:** `nifi-2.12.0-source-release.zip` ---
    source code, mainly for building NiFi.

------------------------------------------------------------------------

## 3. Extract NiFi to `C:\nifi`

1.  Create this folder if it does not already exist:

    ``` text
    C:\nifi
    ```

2.  Right-click `nifi-2.12.0-bin.zip`.

3.  Choose **Extract All...** or use an extraction tool.

4.  Extract the archive so the final folder structure is:

    ``` text
    C:\nifi\nifi-2.12.0\
    ```

5.  Open the extracted folder and check that it contains folders such
    as:

    ``` text
    C:\nifi\nifi-2.12.0\bin
    C:\nifi\nifi-2.12.0\conf
    C:\nifi\nifi-2.12.0\lib
    ```

The `bin` folder should contain NiFi launch scripts, including
`nifi.cmd`.

> **Troubleshooting:** If there is no `bin` folder and you see
> source-code folders or files instead, you probably downloaded or
> extracted the source release. Download `nifi-2.12.0-bin.zip` and
> extract that file instead.

------------------------------------------------------------------------

## 4. Configure NiFi to use JDK 21

If your PC also uses Java 17 for another project, you do not have to
change your whole computer's Java configuration. Configure NiFi to use
JDK 21 specifically.

### 4.1 Open the NiFi environment script

Open this file in Notepad or another text editor:

``` text
C:\nifi\nifi-2.12.0\bin\nifi-env.cmd
```

If Windows asks which application to use, select Notepad. If the file is
not present in your extracted distribution, confirm that you extracted
the binary ZIP and inspect the files in the `bin` folder.

### 4.2 Set `JAVA_HOME`

Add or update the `JAVA_HOME` setting so it points to the JDK 21 folder.
For the known installation path, the line is:

``` bat
set "JAVA_HOME=C:\Program Files\Java\jdk-21.0.12.1"
```

Important: - Use your actual JDK 21 path if it differs. - Point to the
JDK root folder, not to its `bin` subfolder. - Do not add quotation
marks inside the path itself; the shown `set "NAME=value"` syntax is
intentional. - If the script already defines `JAVA_HOME`, update that
setting rather than leaving conflicting assignments.

Save the file.

### 4.3 Confirm the Java executable exists

In Command Prompt, run:

``` bat
dir "C:\Program Files\Java\jdk-21.0.12.1\bin\java.exe"
```

If your JDK is installed in another folder, substitute your actual path.
The command should list `java.exe`.

------------------------------------------------------------------------

## 5. Start Apache NiFi

### 5.1 Open Command Prompt

Open **Command Prompt** (CMD). Move into NiFi's `bin` directory:

``` bat
cd /d C:\nifi\nifi-2.12.0\bin
```

The `/d` option allows `cd` to change the drive as well as the
directory.

### 5.2 Start NiFi

Run:

``` bat
nifi.cmd start
```

Give NiFi time to initialize the first time. It can take several
minutes, depending on your computer.

To check its status, run:

``` bat
nifi.cmd status
```

If you need to stop NiFi later, run:

``` bat
nifi.cmd stop
```

Keep the installation folder in place while NiFi runs. Do not close or
delete files in the NiFi directory.

------------------------------------------------------------------------

## 6. Find the generated username and password

On a fresh installation, NiFi generates a single-user username and
password and writes them to its application log.

Open another Command Prompt and run:

``` bat
findstr /I /C:"Generated Username" /C:"Generated Password" "C:\nifi\nifi-2.12.0\logs\nifi-app.log"
```

Look for log entries similar to:

``` text
Generated Username [generated-username]
Generated Password [generated-password]
```

Use the actual values from your log; the values above are only examples.
Keep the credentials private.

If the file or entries are not found:

1.  Confirm NiFi has started.

2.  Check that this log exists:

    ``` text
    C:\nifi\nifi-2.12.0\logs\nifi-app.log
    ```

3.  Open the log in Notepad and search for `Generated Username` and
    `Generated Password`.

4.  If startup failed, inspect the latest error messages in the log.

------------------------------------------------------------------------

## 7. Open NiFi in your browser

Open Chrome, Edge, or another browser and navigate to:

**https://localhost:8443/nifi**

Use the generated username and password from Step 6 to sign in.

### Browser certificate warning

On a local first-time installation, NiFi may use a self-signed TLS
certificate. Your browser may display a certificate/privacy warning.

For a local learning environment, verify that the address is exactly
`https://localhost:8443/nifi` before proceeding through the browser's
advanced option. Do not treat this as a general instruction to ignore
certificate warnings on unfamiliar websites or production systems.

If the page does not open, check that NiFi is running and that port
`8443` is not blocked or already in use.

------------------------------------------------------------------------

## 8. Confirm the installation worked

After logging in, you should see the Apache NiFi web interface and a
canvas where you can build a dataflow.

You have successfully launched NiFi when:

-   `nifi.cmd status` reports that NiFi is running.
-   The browser opens `https://localhost:8443/nifi`.
-   You can log in using the generated credentials.
-   The NiFi canvas appears.

The web interface is where you add processors, connect relationships,
configure services, and start your dataflow.

------------------------------------------------------------------------

## 9. First steps inside the NiFi canvas

To begin a basic data pipeline:

1.  Add a **GenerateFlowFile** processor to generate test FlowFiles.
2.  Add an **UpdateAttribute** processor to create or update FlowFile
    attributes.
3.  Add an **EvaluateJsonPath** processor to extract fields from JSON
    content.
4.  Add a **RouteOnAttribute** processor to route records based on
    conditions.
5.  Add a destination processor, such as **PutDatabaseRecord**, when you
    are ready to configure a database connection.

For example, a route condition can check whether an extracted `age`
attribute is greater than 30:

``` text
${age:gt(30)}
```

This condition works only after `age` has been extracted into a FlowFile
attribute and is available in the expected format.

For `PutDatabaseRecord`, you will also need a database connection
service, a record reader, the appropriate JDBC driver, and a destination
table with compatible columns.

------------------------------------------------------------------------

## 10. Common problems and solutions

### Problem: `nifi.cmd` is not recognized

**Cause:** The terminal is not in the NiFi `bin` directory, or the
binary ZIP was not extracted correctly.

**Solution:**

``` bat
cd /d C:\nifi\nifi-2.12.0\bin
dir nifi.cmd
```

If the file is missing, recheck Step 2 and Step 3.

### Problem: NiFi cannot find Java or uses the wrong version

**Cause:** The configured JDK path is incorrect, or NiFi is not using
the expected `JAVA_HOME`.

**Solution:** - Check that `bin\java.exe` exists under your JDK 21
folder. - Recheck the `JAVA_HOME` line in `bin\nifi-env.cmd`. - Inspect
the NiFi application and bootstrap logs for Java startup errors.

### Problem: The browser cannot connect

**Cause:** NiFi may still be starting, may have failed, or may not be
listening on the expected port.

**Solution:** - Run `nifi.cmd status`. - Wait a few minutes after the
first start. - Inspect `C:\nifi\nifi-2.12.0\logs\nifi-app.log`. - Check
the configured web port in `C:\nifi\nifi-2.12.0\conf\nifi.properties`.
The default HTTPS port is `8443`.

### Problem: Login credentials do not work

**Cause:** The username or password was copied incorrectly, or you are
using credentials from a different installation.

**Solution:** - Read the generated credentials from the current
installation's `logs\nifi-app.log`. - Copy the values carefully, without
adding extra characters.

### Problem: Port 8443 is already in use

**Cause:** Another application may be listening on the port.

**Solution:** - Check whether another NiFi instance is running. - If
needed, inspect `nifi.web.https.port` in `conf\nifi.properties` and
configure an available port, then restart NiFi. If you change the port,
use that port in the browser URL.

------------------------------------------------------------------------

## 11. Useful commands to remember

Run these from:

``` bat
cd /d C:\nifi\nifi-2.12.0\bin
```

  Command             Purpose
  ------------------- -----------------------------------------
  `nifi.cmd start`    Start NiFi
  `nifi.cmd status`   Check NiFi status
  `nifi.cmd stop`     Stop NiFi
  `java -version`     Check the Java version found by Windows
  `where java`        Show Java executables found on PATH

**Browser URL:** `https://localhost:8443/nifi`

**Installation folder:** `C:\nifi\nifi-2.12.0`

**Java requirement:** JDK 21 for NiFi 2.x.

**Remember:** Download the **binary** ZIP, extract it, configure NiFi's
Java 21 path, start NiFi from `bin`, retrieve the generated login
credentials from the logs, and open the HTTPS URL in your browser.
