# Secure-Socket-Programming-project
## Objective

The objective of this SSL client-server project is to establish a secure communication channel between a client and a server using SSL/TLS protocols in Java. The project demonstrates how to configure SSL contexts, manage keystores containing private keys and certificates, and implement trust managers for certificate validation. Through this implementation, the client sends a message to the server, which responds back, showcasing encrypted data transmission and the fundamental principles of secure networking. This project serves as a practical introduction to secure communications in Java applications.

### Skills Learned

- Understanding SSL/TLS: Gained knowledge of secure communication protocols and their importance.
- Java Networking: Learned how to create client-server applications using Java sockets.
- SSL Context Configuration: Understood how to configure SSL contexts for secure connections.
- KeyStore Management: Learned how to load and manage keystores containing private keys and certificates.
- Trust Management: Explored trust managers and their role in certificate validation.
- Input/Output Streams: Developed skills in handling input and output streams for network communication.
- Exception Handling: Improved exception handling techniques specific to network and security operations.
- Basic Cryptography Concepts: Gained insight into basic cryptographic principles related to secure communications.
  
### Tools Used

- Java Development Kit (JDK): Required for compiling and running Java applications.
- SSL/TLS Libraries: Utilized for secure socket communication (javax.net.ssl).
- KeyStore: Used to manage keys and certificates for the server.
- TrustManager: Manages trusted certificates for the client.
- BufferedReader & PrintWriter: For reading from and writing to input/output streams.
- FileInputStream: Used to read the keystore file.
- SSLContext: Configures SSL settings for secure communication.
- SSLSocket & SSLServerSocket: This is used to establish secure client-server connections.

## Code and Implementation

SSLClient:

```java
package SSLClient;
```
Declares the package name for the client code.

```java
import javax.net.ssl.*;
import java.io.*;
import java.security.*;
import java.security.cert.CertificateException;
import java.security.cert.X509Certificate;
```
Imports necessary classes for SSL, I/O operations, and security.

```java
public class SSLClient {
```
Declares the SSLClient class.

```java
    private static final String SERVER_HOST = "localhost";
    private static final int SERVER_PORT = 1234;
```
Defines constants for the server's host and port.

```java
    public static void main(String[] args) {
        try {
```
Main method starts, with a try block to handle exceptions.

```java
            TrustManager[] trustManagers = new TrustManager[]{
                new X509TrustManager() {
                    @Override
                    public void checkClientTrusted(X509Certificate[] x509Certificates, String s)
                            throws CertificateException {
                    }

                    @Override
                    public void checkServerTrusted(X509Certificate[] x509Certificates, String s)
                            throws CertificateException {
                    }

                    @Override
                    public X509Certificate[] getAcceptedIssuers() {
                        return new X509Certificate[0];
                    }
                }
            };
```
Creates a trust manager that trusts all certificates (not recommended for production use).

```java
            SSLContext sslContext = SSLContext.getInstance("TLS");
            sslContext.init(null, trustManagers, new SecureRandom());
```
Creates and initializes an SSL context with the trust manager.

```java
            SSLSocketFactory sslSocketFactory = sslContext.getSocketFactory();
```
Creates an SSL socket factory from the SSL context.

```java
            try (SSLSocket socket = (SSLSocket) sslSocketFactory.createSocket(SERVER_HOST, SERVER_PORT);
                 BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                 PrintWriter out = new PrintWriter(socket.getOutputStream(), true)) {
```
Creates an SSL socket and sets up input/output streams.

```java
                out.println("Hello, Server!");
```
Sends a message to the server.

```java
                String response = in.readLine();
                System.out.println("Received from server: " + response);
```
Reads and prints the server's response.

```java
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
Closes resources and handles exceptions.

SSLServer:

```java
package SSLServer;

import javax.net.ssl.*;
import java.io.*;
import java.security.*;
```
Package declaration and imports.

```java
public class SSLServer {
    private static final int PORT = 1234;
```
Declares the SSLServer class and defines the port constant.

```java
    public static void main(String[] args) {
        try {
```
Main method starts with a try block.

```java
            char[] password = "hector".toCharArray();
            KeyStore keyStore = KeyStore.getInstance("JKS");
```
Sets up the keystore password and creates a KeyStore instance.

```java
            try (FileInputStream fis = new FileInputStream("Keystore")) {
                keyStore.load(fis, password);
            }
```
Loads the keystore from a file.

```java
            KeyManagerFactory kmf = KeyManagerFactory.getInstance("SunX509");
            kmf.init(keyStore, password);
```
Creates and initializes a KeyManagerFactory.

```java
            SSLContext sslContext = SSLContext.getInstance("TLS");
            sslContext.init(kmf.getKeyManagers(), null, null);
```
Creates and initializes an SSL context with the key managers.

```java
            SSLServerSocketFactory ssf = sslContext.getServerSocketFactory();
```
Creates an SSL server socket factory.

```java
            try (SSLServerSocket serverSocket = (SSLServerSocket) ssf.createServerSocket(PORT)) {
                System.out.println("Server started. Waiting for client...");
```
Creates an SSL server socket and starts listening.

```java
                try (SSLSocket clientSocket = (SSLSocket) serverSocket.accept()) {
                    System.out.println("Client connected.");
```
Accepts a client connection.

```java
                    try (BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                         PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true)) {
```
Sets up input/output streams for the client connection.

```java
                        String message;
                        while ((message = in.readLine()) != null) {
                            System.out.println("Received from client: " + message);
                            out.println("Server received: " + message);
                        }
```
Reads messages from the client, prints them, and sends a response.

```java
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
Closes resources and handles exceptions.

## Results

Server Console

![Screenshot 2025-01-02 160419](https://github.com/user-attachments/assets/303efead-1842-45f0-a7c9-95eea5914979)



Client Console

![Screenshot 2024-11-12 230956](https://github.com/user-attachments/assets/74d7682b-af17-4921-b086-f13ca64c37c6)

