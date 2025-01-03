# A Local DNS Server (with iterative search approach)

## Functions and Design
This local DNS Server is implemented using Python 3.9 with socket and dnslib packages. It can be run in Ubuntu 20.04.

**Client**  
A client can send one DNS query at a time to the server using `dig`, and set a flag to indicate whether to ask a public server (0) or search iteratively (1).

**Basic Design of the Server**  
 Once the server gets a query, if it is to ask the public server, the server will get DNS response from `223.5.5.5` and send the answer back to the client. If it is required to search iteratively, the server will call the function `iterative_searching(qname,final_response_record)`. In each iteration, an instance of `dnslib.DNSRecord` will be created to send query to a domain server and parse the data received from the domain server. At the beginning, a query will be sent to a root server. The root server will tell which top-level domain server(s) to ask next. Then, after sending query to the corresponding top-level domain server, the iteration will go on until the authoritative server return the final IP address of a DNS query. If the authoritative server return a `CNAME` record, the local DNS server will do iterative searching again using this CNAME to get the final IP address. The server will print out the IP of all servers it has passed by before getting the final answer.  

**Timeout Handling**  
If a server (root/TLD/authoritative server) does not response within two seconds and there exists another available server at the same level, we will switch to that  server for another attempt.

**Cache**   
A cache is set to maintain the existing records until the server is shut down. The cache will return the response record if a query is already in cache.

## How to Execute
1. **Start the server**: In a terminal, run the server using command:
    ```
    python dns.py
    ```
2. **Choose a flag**: 0 - ask a public server; 1 - ask for iterative searching.
![image-1](https://github.com/user-attachments/assets/4f849ff2-9ff5-4f65-9a38-7261a432600f)


3. **Send a DNS query from client**: Open another terminal as a client. Using `dig` to send a query to 127.0.0.1, port 1234.
    ```
    dig www.example.com @127.0.0.1 -p 1234
    ```
4. **Get response**: After sending a query to the server, the client will receive an answer. Server will print out IP of each server it has passed by.
![image-2](https://github.com/user-attachments/assets/c1a4fab2-8f0f-433b-9887-aecb63784640)

5. **Shut down the server**: The client can keep sending one query at a time to the server using `dig` command until the server is shut down. To close the server, use Keyboard Interrupt (type `Ctrl C`) in the server terminal.
6. **Timeout**: Even with timeout handling implemented, the client may receive timeout message if too many timeout errors occurs during searching. In this case, it is recommended to restart the server and try again.

## Demo
![image-3](https://github.com/user-attachments/assets/787353f4-9f23-45ed-8906-c304e7585f6c)
![image-4](https://github.com/user-attachments/assets/adb4fbfc-3e8b-412c-85c9-33588f45c1ac)
![image-5](https://github.com/user-attachments/assets/441e31ae-58a4-4db2-936d-6c5cc906935f)


