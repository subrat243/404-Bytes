## What is Browsh?

Browsh is a modern, text-based browser that renders in a terminal environment. Unlike traditional text-based browsers like Lynx or w3m, which can only handle basic HTML, Browsh can render anything a modern graphical browser can. This includes HTML5, CSS3, JavaScript, videos, and even WebGL content.

It achieves this by using a real browser engine (Mozilla Firefox) in headless mode to render a web page. It then converts the graphical output into UTF-8 characters and ANSI escape codes that can be displayed in a terminal. This makes it an incredibly powerful tool for accessing the modern web from environments without a graphical user interface (GUI).

The primary goal of Browsh is to significantly reduce bandwidth and improve browsing speeds on slow internet connections while still providing access to the full complexity of today's websites.

## Key Features

* **Full Modern Web Support**: Renders complex, JavaScript-heavy sites, videos, and WebGL.
* **Bandwidth Saver**: Ideal for slow, unreliable, or expensive internet connections as only text data is sent to the terminal client.
* **Remote Access**: Use it over SSH to browse the web on a remote machine without needing X11 forwarding, VNC, or RDP.
* **Reduced Distractions**: Presents web content in a simplified, text-focused format.
* **Low Memory Usage**: The client-side terminal component is very lightweight.

## How It Works

Browsh acts as a client that communicates with a web extension running inside a headless Firefox instance.

1.  Firefox, running in the background, loads and renders the requested web page.
2.  The Browsh web extension converts the rendered page into a simplified text-based representation.
3.  This text data is sent to the terminal client, which displays it.
4.  User interactions (keystrokes, mouse clicks) are sent back to the headless Firefox instance to interact with the page.

## Installation

The simplest way to install Browsh is to download the appropriate binary for your system from the official website.

**Example for Linux (64-bit):**

```sh
# Browsh
wget [https://github.com/browsh-org/browsh/releases/download/v1.6.4/browsh_1.6.4_linux_amd64.rpm](https://github.com/browsh-org/browsh/releases/download/v1.6.4/browsh_1.6.4_linux_amd64.rpm)
# Install using your package manager (example for .rpm)
sudo rpm -i browsh_1.6.4_linux_amd64.rpm

# For Debian/Ubuntu (.deb)
wget [https://github.com/browsh-org/browsh/releases/download/v1.6.4/browsh_1.6.4_linux_amd64.deb](https://github.com/browsh-org/browsh/releases/download/v1.6.4/browsh_1.6.4_linux_amd64.deb)
sudo dpkg -i browsh_1.6.4_linux_amd64.deb
sudo apt-get install -f # To install dependencies
```

_Note: You must have a recent version of Mozilla Firefox installed for Browsh to function._

## Usage

### Basic Command

To start Browsh and navigate to a specific URL, use the following command:

Bash

```
browsh <url>
```

**Example:**

Bash

```
browsh [https://www.google.com](https://www.google.com)
```

If you run `browsh` without a URL, it will open to your default homepage.

### Keybindings

Browsh uses keybindings that are similar to those found in standard graphical browsers.

|Key|Action|
|---|---|
|`Ctrl` + `q`|Quit Browsh|
|`Ctrl` + `l`|Focus the URL bar|
|`Backspace`|Go back in history|
|`Ctrl` + `r`|Reload the page|
|`Arrow Keys`|Scroll the page|
|`PageUp`|Scroll up a full page|
|`PageDown`|Scroll down a full page|
|`Tab`|Cycle through links and inputs|
|`Shift` + `Tab`|Cycle backwards|
|`Enter`|Click a link or button|
|`Ctrl` + `t`|Open a new tab|
|`Ctrl` + `w`|Close the current tab|
|`Ctrl` + `Tab`|Switch to the next tab|

### Example Scenario: Browsing on a Remote Server

Imagine you are connected to a remote server via SSH and need to look up documentation on a website to solve a problem. You don't have a GUI on the server.

1. **SSH into your server:**
    
    Bash
    
    ```
    ssh user@your-server-ip
    ```
    
2. **Start Browsh and search for the information:**
    
    Bash
    
    ```
    browsh [https://duckduckgo.com](https://duckduckgo.com)
    ```
    
3. Once the page loads, press `Tab` until the search bar is highlighted.
    
4. Type your search query, for example, "linux file permissions", and press `Enter`.
    
5. Use the `Arrow Keys` or `PageDown` to scroll through the search results.
    
6. Use `Tab` to navigate to the link you want to open and press `Enter` to view the page.
    
7. When you are finished, press `Ctrl` + `q` to exit Browsh and return to your shell prompt.