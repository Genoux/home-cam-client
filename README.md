# Home Cam Streaming Client (Linux/Raspberry Pi)

This repository contains the client-side scripts and service configuration for streaming a webcam feed to the [Home Cam Server](https://github.com/YOUR_USER/home-cam-server) (replace with your actual server repo link).

It is designed primarily for Linux systems like Raspberry Pi OS or Ubuntu Server.

## Features

*   Streams webcam video using FFmpeg to an RTMP server.
*   Runs as a systemd service for automatic startup and restarts.
*   Automatically detects available V4L2 camera devices (uses `/dev/video0` by default).
*   Handles network connectivity checks.
*   Uses hardware acceleration (h264_omx) on Raspberry Pi if available, otherwise falls back to software encoding (libx264).
*   Configurable via environment variables loaded from `/etc/webcam-stream.conf`.

## Directory Structure

```
home-cam-client/ (Repository Root)
├── config/
│   └── webcam-stream.service  # Systemd service definition
├── scripts/
│   └── stream-webcam.sh     # Main streaming script
├── install.sh                 # Installation script
└── README.md                  # This file
```

## Prerequisites

*   A Linux system (e.g., Raspberry Pi OS, Ubuntu Server)
*   A connected webcam compatible with V4L2 (most USB webcams)
*   `git` installed (`sudo apt update && sudo apt install git -y`)
*   `ffmpeg` installed (`sudo apt update && sudo apt install ffmpeg -y`)
*   `v4l-utils` installed (`sudo apt update && sudo apt install v4l-utils -y`)

## Installation

These steps install the streaming script and set it up as a systemd service.

1.  **Clone the Repository:**
    Open a terminal on your Raspberry Pi/Linux device and clone this repository:
    ```bash
    # Replace <your-repo-url> with the actual HTTPS or SSH URL
    git clone <your-repo-url> ~/home-cam-client 
    cd ~/home-cam-client
    ```

2.  **Run the Installer:**
    Execute the installation script with `sudo`:
    ```bash
    # Ensure the script is executable
    chmod +x install.sh
    
    # Run the installer
    sudo ./install.sh
    ```
    This script will:
    *   Copy `stream-webcam.sh` to `/usr/local/bin/`.
    *   Copy `webcam-stream.service` to `/etc/systemd/system/`.
    *   Set appropriate permissions.
    *   Create a default configuration file at `/etc/webcam-stream.conf` (if it doesn't exist).
    *   Create the log file `/var/log/webcam_stream.log`.
    *   Reload the systemd daemon, enable, and start the `webcam-stream` service.

## Configuration

The service is configured primarily through the environment file located at `/etc/webcam-stream.conf`. The `install.sh` script creates a default version of this file.

1.  **Edit the Configuration File:**
    ```bash
    sudo nano /etc/webcam-stream.conf
    ```

2.  **Set Required Values:**
    *   **`STREAM_KEY`**: **IMPORTANT:** Change this to a unique identifier for this specific camera/client (e.g., `STREAM_KEY=pi_living_room`).
    *   **`RTMP_HOST`**: Verify this is the correct IP address of your Home Cam Server.

3.  **Optional Values:**
    You can uncomment and modify other variables like `RESOLUTION`, `FRAMERATE`, `BITRATE`, etc., if needed.

4.  **Restart the Service:**
    After saving changes to `/etc/webcam-stream.conf`, you **must** restart the service for the changes to apply:
    ```bash
    sudo systemctl restart webcam-stream.service
    ```

## Usage

Once installed and configured, the service will run automatically on boot.

*   **Check Service Status:**
    ```bash
    sudo systemctl status webcam-stream.service
    ```
*   **View Logs:**
    ```bash
    tail -f /var/log/webcam_stream.log
    ```
*   **Start/Stop Manually:**
    ```bash
    sudo systemctl stop webcam-stream.service
    sudo systemctl start webcam-stream.service
    ```

## Updating

To update the client to the latest version from the repository:

1.  **Navigate to the Cloned Directory:**
    ```bash
    cd ~/home-cam-client 
    ```
2.  **Pull Latest Changes:**
    ```bash
    git pull origin main # Or your default branch name
    ```
3.  **Re-run the Installer:**
    This will copy the updated files and restart the service.
    ```bash
    # Ensure install script is executable (if permissions were lost)
    chmod +x install.sh 
    
    # Re-run the installer
    sudo ./install.sh
    ```
    *Note: This process will **not** overwrite your settings in `/etc/webcam-stream.conf`.* 