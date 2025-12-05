# linux_IPV4_media_broadcast

### Introduction

This is a streaming media broadcast project which involves flow control, multithreading concurrency, inter-thread communication, and socket-based network programming.

---

### Software Architecture

This project is a multithreaded, multiprocess, socket-based network streaming broadcast system, consisting of a **server**, a **client**, and **global configuration**.

#### 1. Server (`/src/server`)

The server is responsible for reading the channel list from the media library and broadcasting it to a multicast address using UDP sockets.  
Each channel contains a description file and one or more media files.  
A dedicated thread (channel ID = 0) sends the overall channel list (including descriptions and channel numbers).  
Each channel also has its own thread for sending media data, with rate control applied during file transmission.

**Modules:**

- **medialib:** Reads all channel information under `/share/media/`.
- **thr_list:** Creates a thread to send the channel list.
- **thr_channel:** Creates threads for each channel to send media data.
- **mytbf:** Implements a token bucket algorithm for flow control.
- **server:** The main program entry point.

**Default paths:**

- Media library: `/share/media/`  
- Description file type: `desc.text`

**Directory structure:**
```
|-- share
|-- media
    |-- ch1
        |-- desc.text
        |-- 1.mp3
    |-- ch2
        |-- desc.text
        |-- 2.mp3
```

---

#### 2. Client (`/src/client`)

The client joins the multicast group, receives the channel list, allows the user to choose a channel, and then receives and plays the selected channel’s stream.  
It uses two processes: a **parent process** and a **child process**.  
The parent process receives network packets and sends them to the child process through an anonymous pipe.  
The child process handles playback.

**Modules:**

- **client:** Main program entry point.

---

#### 3. Global Configuration Header (`/src/include/`)

**Default multicast group address:** `224.2.2.2`  
(valid range: 224.0.0.0 ~ 239.255.255.255)

---

### Installation

Using Git:
```bash
git clone https://gitee.com/cgbfg/IPV4_media_broadcast.git
```

---

### Usage

1. **Start the server**

   Go to `/src/server`, run:
   ```bash
   make
   ./server -[option] [arg]
   ```

   **Options:**
   ```
   -M  Specify multicast group
   -P  Specify port
   -F  Run in foreground
   -D  Specify media library path
   -I  Specify network interface
   -H  Display help
   ```

2. **Start the client**

   Go to `/src/client`, run:
   ```bash
   make
   ./client -[option] [arg]
   ```

   **Options:**
   ```
   -M --mgroup Specify multicast group
   -P --port   Specify receiving port
   -H --help   Display help
   -p --player Specify media player
   ```

---

### Screenshots

1. **Global Configuration**

   ![Global Configuration](./public/images/config.png "config")

2. **Server Running**

   ![Server Running](./public/images/server.png "server")

3. **Client Running**

   ![Client Running](./public/images/client.png "client")

---

### Contribution Guide

1. Fork this repository  
2. Create a new branch (e.g. `Feat_xxx`)  
3. Commit your changes  
4. Submit a Pull Request  
