# UAP P2P Network: Communication and File Sharing

Course: CSE 433 – Blockchain & Distributed Security Lab  
University: University of Asia Pacific, Department of CSE  
Student: Tousif Mahmood Sani  
Student ID: 22201035  
Section: A

---

## 1. Overview

UAP P2P Network is a lightweight peer-to-peer (P2P) application developed in Python using TCP sockets. Each running instance acts as both a server and a client:

- **Server:** listens on a selected port and accepts incoming peer connections.
- **Client:** connects to another peer by using its IP address and port.

Peers communicate directly without relying on a central server. Once connected, they can exchange text messages, transfer files, and maintain connections with multiple peers. The graphical interface, built with Tkinter, displays connected peers and a live event log.

### Main Features

| Feature | Description |
|---|---|
| Peer startup | The user chooses a peer name and listening port. |
| Peer connection | A peer connects to another peer using its IP address and port. |
| Peer identification | A `hello` / `hello_ack` handshake exchanges `peer_id`, `peer_name`, and `port`. |
| Multiple connections | A separate thread handles each connection. |
| Message framing | Messages use a 4-byte length header followed by a JSON payload. |
| Text communication | Text messages are sent to the peer selected in the interface. |
| File transfer | File metadata is followed by raw file bytes sent in 64 KB chunks. |
| Received files | Files are stored in `downloads/`; existing files are not overwritten. |
| Error handling | Connection, input, and transfer errors are reported in the log instead of crashing the application. |

The application can transfer ordinary files such as text documents, images, audio, video, PDFs, and ZIP archives.

---

## 2. Requirements

- **Python 3.9 or newer**
- **Tkinter** for the graphical interface
  - Windows and macOS: generally included with the standard Python installer.
  - Ubuntu/Debian: install it with `sudo apt install python3-tk`.
- **Operating system:** Windows, Linux, or macOS
- **Network:** no additional network setup is needed for testing on one computer. For a multi-computer demonstration, all computers should be connected to the same LAN or Wi-Fi network.
- **Third-party packages:** none required. The project uses Python standard-library modules: `socket`, `threading`, `json`, `struct`, `os`, `queue`, and `tkinter`.

---

## 3. Project Files

The project directory is organized as follows:

```text
P2P_Network/
├── main.py            # Tkinter interface and user interaction
├── p2p_node.py        # Sockets, threads, handshake, messaging, and file transfer
├── protocol.py        # Message framing and protocol message builders
├── requirements.txt   # No third-party packages are required
├── README.md          # Project documentation
└── downloads/         # Files received from other peers
```

---

## 4. Installation and Setup

1. Download and install Python 3.9 or newer from [python.org](https://www.python.org/downloads/).
2. On Windows, select **Add Python to PATH** during installation.
3. Open Command Prompt or a terminal and check the installation:

   ```bash
   python --version
   ```

   On Linux or macOS, you may need to use `python3 --version`.

4. Extract the submitted ZIP archive. The `P2P_Network` folder should appear.
5. No `pip install` command is needed.
6. If Tkinter is missing on Linux, install it with:

   ```bash
   sudo apt install python3-tk
   ```

---

## 5. Launching a Peer

Open a terminal in the `P2P_Network` directory and run:

```bash
python main.py
```

The **UAP P2P Network** window will open. To run multiple peers on the same computer, open a separate terminal for each instance and run the command again.

In every window:

1. Enter a peer name, such as `Alice`.
2. Enter a listening port, such as `5000`. Each peer on the same computer must use a different port.
3. Click **Start Peer**.

The interface displays the peer's name, ID, and port. For example:

```text
Alice | ID: a83f21c4 | Port: 5000
[SYSTEM] Peer started: Alice [a83f21c4] on port 5000
```

**Screenshot:** `screenshots/01_start_peer.png` — show the peer name, port, ID, and startup log.

---

## 6. Connecting Peers

### A. Running two peers on one computer

Use the following example configuration:

| Peer | Name | Port |
|---|---|---:|
| A | Alice | 5000 |
| B | Bob | 5001 |

1. Start Alice on port `5000` and Bob on port `5001` in separate terminals.
2. In Bob's window, find **Connect to Another Peer**.
3. Enter IP address `127.0.0.1` and port `5000`.
4. Click **Connect**.

Both windows should list the other peer under **Connected Peers**. The log should show messages similar to `[SYSTEM] Connected to Alice` and `[SYSTEM] Connected to Bob`.

**Screenshots:**
- `screenshots/02_peerA_connected.png` — Alice's window showing Bob.
- `screenshots/03_peerB_connected.png` — Bob's window showing Alice.

### B. Connecting two computers on the same LAN/Wi-Fi

1. Find Computer A's local IP address:
   - Windows: run `ipconfig` and locate **IPv4 Address** (for example, `192.168.1.10`).
   - Linux/macOS: run `ip a` or `ifconfig`.
2. Start a peer on each computer, using a suitable port on each one.
3. On Computer B, enter Computer A's LAN IP address and port, then click **Connect**.
4. If the connection fails or times out, check the IP address and port, confirm that both computers are on the same network, and allow Python through the firewall if necessary. On Windows, check **Windows Defender Firewall → Allow an app**.

**Screenshots:**
- `screenshots/04_two_computers_A.png` — Computer A with Computer B in its peer list.
- `screenshots/05_two_computers_B.png` — Computer B with Computer A in its peer list.

### C. Connecting more than two peers

Start a third peer named Charlie on port `5002`. From Charlie's window, connect to Alice at `127.0.0.1:5000` and Bob at `127.0.0.1:5001`.

The example network can be represented as:

```text
       Alice (5000)
        /       \
 Bob (5001) --- Charlie (5002)
```

Each peer can communicate directly with the peers to which it is connected.

**Screenshots:**
- `screenshots/06_multi_alice.png` — Alice connected to two peers.
- `screenshots/07_multi_bob.png` — Bob connected to two peers.
- `screenshots/08_multi_charlie.png` — Charlie connected to two peers.

---

## 7. Sending Text Messages

1. Select the intended recipient from the **Connected Peers** list.
2. Type the message in the **Send Text** box.
3. Click **Send** or press Enter.

For example, Alice's log may show `Alice -> Bob: Hello Bob!`, while Bob's log displays the received message. Messages can also be sent in the opposite direction.

**Screenshots:**
- `screenshots/09_text_A_to_B.png` — show the message from Alice to Bob in both logs.
- `screenshots/10_text_B_to_A.png` — show the message from Bob to Alice in both logs.

---

## 8. Transferring Files

1. Select the peer that should receive the file.
2. Click **Choose File & Send**.
3. Select a file in the file dialog. Supported examples include images, audio, video, text documents, PDFs, and ZIP archives.
4. Check the logs on both computers. The sender and receiver should see file-transfer status messages.
5. The received file is saved in the recipient's `downloads/` directory.

If a file with the same name already exists, the application adds a number to the new filename—for example, `photo_1.jpg`—instead of overwriting the existing file.

### How file transfer works

1. The sender sends a framed JSON metadata message containing information such as the filename and file size:

   ```json
   {"type": "file", "filename": "photo.jpg", "filesize": 2456789}
   ```

2. The sender transmits the file's raw bytes in **64 KB chunks**, avoiding the need to load the entire file into memory at once.
3. The receiver reads exactly the number of bytes specified by `filesize` and writes them to the `downloads/` folder.

The metadata's file size determines when the transfer is complete; a single `recv()` call is not assumed to return the whole file.

**Suggested evidence for the report:**
- `screenshots/11_image_A_to_B.png` — image transfer from A to B, with both logs visible.
- `screenshots/12_audio_B_to_A.png` — audio transfer from B to A, with both logs visible.
- `screenshots/13_video_A_to_B.png` — video transfer from A to B, with both logs visible.
- `screenshots/14_pdf_or_zip.png` — optional PDF or ZIP transfer.
- `screenshots/15_downloads_folder.png` — received files shown in File Explorer, with a check that they open or play correctly.

---

## 9. Communication Across Multiple Peers

When Alice, Bob, and Charlie are connected, select the relevant peer before sending a message or file. Example communication pairs include:

- Alice ↔ Bob
- Bob ↔ Charlie
- Charlie ↔ Alice

For demonstration, have Charlie send a text message to both Alice and Bob. Then transfer a file from Alice or Bob to Charlie, and send another file from Charlie to a different peer.

**Screenshots:**
- `screenshots/16_peerC_text.png` — text communication involving Charlie.
- `screenshots/17_peerC_file.png` — file transfer involving Charlie.

---

## 10. Error Handling

The application reports errors in the event log and status bar rather than intentionally stopping when common problems occur.

| Situation | Example response or behavior |
|---|---|
| Invalid port | `[ERROR] Invalid port (must be 1-65535)` |
| Invalid IP address | `[ERROR] Invalid IP address: ...` |
| Peer is not running or connection is refused | `[ERROR] Connection failed: ... Connection refused` |
| No peer selected before sending | `[ERROR] Select a peer from the Connected Peers list first` |
| Peer not started | `[ERROR] Start your peer first` |
| Selected file does not exist | `[ERROR] File does not exist: ...` |
| Invalid file size in metadata | The transfer is rejected and the connection is closed safely. |
| Peer disconnects, including during transfer | The peer is removed from the list and an incomplete file is deleted. |

**Screenshots:**
- `screenshots/18_error_connection_refused.png` — attempt to connect to a port where no peer is listening.
- `screenshots/19_peer_disconnected.png` — show the disconnection message and updated peer list after stopping a peer.

---

## 11. Communication Protocol

Every framed message begins with a 4-byte length field, followed by the JSON payload:

```text
+----------------+-----------------------+
| 4-byte length  |     JSON payload      |
+----------------+-----------------------+
```

The receiver reads the 4-byte length first, then reads exactly that many bytes to obtain the JSON message.

| Message type | Purpose | Main fields |
|---|---|---|
| `hello` | Handshake sent by the connecting peer | `peer_id`, `peer_name`, `port` |
| `hello_ack` | Response from the accepting peer | `peer_id`, `peer_name`, `port` |
| `text` | Text communication | `sender_id`, `sender_name`, `message` |
| `file` | File metadata followed by raw file bytes | `sender_id`, `sender_name`, `filename`, `filesize` |

### Basic communication flow

```text
Peer A
  -> IP address and port
  -> TCP socket connection
  -> HELLO handshake
  -> Application protocol
  -> Text message or file data
  -> Peer B
```

---

## 12. Troubleshooting

| Problem | Possible solution |
|---|---|
| `Could not start peer: ... address already in use` | Another program or peer is using the port. Choose a different port. |
| `Connection failed: ... Connection refused` | Make sure the remote peer is running and the IP address and port are correct. |
| `127.0.0.1` works, but two computers cannot connect | Use the remote computer's LAN IP, not `127.0.0.1`. Check that both computers share the same Wi-Fi/LAN and that the firewall permits Python. |
| Send button does nothing or asks to select a peer | Select a peer in the **Connected Peers** list first. |
| `No module named tkinter` on Linux | Run `sudo apt install python3-tk`. |
| The bottom of the window is not visible | Use the latest `main.py`, which sizes the window to fit its controls. |

---

## 13. Project Scope and Limitations

This is an educational P2P networking project. It demonstrates direct peer connections, text communication, file transfer, message framing, and concurrent connection handling.

The application does **not** implement blockchain, encryption, authentication, NAT traversal, a distributed hash table (DHT), or a central server. It is intended to operate on one computer or within a local network. Communication between peers on separate networks would require additional functionality, such as NAT traversal.

---

## 14. Screenshot Checklist

Create a `screenshots/` directory beside this README and save the demonstration images using the filenames below.

| No. | Filename | Required content |
|---:|---|---|
| 1 | `01_start_peer.png` | Started peer, name, port, peer ID, and startup log |
| 2 | `02_peerA_connected.png` | Peer A showing Peer B in the connected list |
| 3 | `03_peerB_connected.png` | Peer B showing Peer A in the connected list |
| 4 | `04_two_computers_A.png` | Computer A showing Computer B's LAN connection |
| 5 | `05_two_computers_B.png` | Computer B showing Computer A's LAN connection |
| 6 | `06_multi_alice.png` | Alice connected to two peers |
| 7 | `07_multi_bob.png` | Bob connected to two peers |
| 8 | `08_multi_charlie.png` | Charlie connected to two peers |
| 9 | `09_text_A_to_B.png` | Alice-to-Bob message, with both logs |
| 10 | `10_text_B_to_A.png` | Bob-to-Alice message, with both logs |
| 11 | `11_image_A_to_B.png` | Image transfer, sender and receiver logs |
| 12 | `12_audio_B_to_A.png` | Audio transfer, sender and receiver logs |
| 13 | `13_video_A_to_B.png` | Video transfer, sender and receiver logs |
| 14 | `14_pdf_or_zip.png` | Optional PDF or ZIP transfer |
| 15 | `15_downloads_folder.png` | Received files in the `downloads/` folder |
| 16 | `16_peerC_text.png` | Text communication involving Charlie |
| 17 | `17_peerC_file.png` | File transfer involving Charlie |
| 18 | `18_error_connection_refused.png` | Connection-refused error in the log |
| 19 | `19_peer_disconnected.png` | Disconnection message and updated peer list |

