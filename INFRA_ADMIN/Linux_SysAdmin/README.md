# 🐧 Linux System Administration

> **Scope:** Essential management of Linux systems, focusing on file transfers, process control, and service management—skills required for both system reliability and offensive pivoting.

---

## 📂 File Transfer & Web Hosting

In a security context, moving files efficiently is crucial for exfiltration or uploading exploit payloads.

### Secure Copy (SCP)
Uses the SSH protocol to provide encryption and authentication.
* **Syntax:** `scp <source_file> <user>@<remote_ip>:<destination_path>`
* **Common Flags:**
    * `-P <port>`: Specify a non-standard SSH port.
    * `-r`: Recursively copy entire directories.
    * `-C`: Enable compression for faster transfers over slow links.

### Instant Web Server (Python3)
Ideal for quickly hosting a directory to download files on a target machine or sharing tools in a lab.
* **Command:** `python3 -m http.server <port>`
* **Default Port:** 8000 (if not specified).
* **Usage Tip:** Always run this in a dedicated "Public" or "Transfer" folder to avoid exposing sensitive home directory files.

---

## ⚙️ Service Management (systemd)

Most modern Linux distributions use **systemd** as the init system to manage background processes (daemons).

### Command: `systemctl`
| Action | Description |
| :--- | :--- |
| `start <svc>` | Initiates the service immediately. |
| `stop <svc>` | Terminations the service execution. |
| `restart <svc>` | Shuts down and restarts (useful after config changes). |
| `status <svc>` | Shows if the service is running, failed, and recent logs. |
| `enable <svc>` | Configures the service to start automatically at **boot**. |
| `disable <svc>` | Prevents the service from starting at boot. |

---

## 🚦 Process Control & Signals

Signals are software interrupts sent to a program to indicate a specific event.



### Common Signals
When using the `kill` command, you are actually sending a specific signal to a Process ID (PID).

1.  **SIGTERM (15)**: The "Soft Kill." It requests the process to terminate gracefully, allowing it to save its state and close files. Always try this first.
2.  **SIGKILL (9)**: The "Hard Kill." It forces the process to stop immediately. The process cannot catch or ignore this signal. Use when a process is "zombie" or unresponsive.
3.  **SIGSTOP (19)**: The "Pause." It suspends the process without killing it. It can be resumed later using **SIGCONT (18)**.

### Practical Commands
* `ps aux`: List all running processes with user and CPU usage.
* `kill -9 <PID>`: Immediately terminate a process.
* `top` or `htop`: Real-time interactive process monitoring.

---

## 💡 Expert Tips

* **SCP for Exfiltration:** If you have SSH access, `scp` is much stealthier than downloading files via `wget` or `curl`, as it blends in with legitimate administrative traffic.
* **Service Persistence:** As a Red Teamer, look for "Disabled" but "Running" services—this is a common sign of manual intervention. As a Blue Teamer, always audit `systemctl list-unit-files --type=service` for suspicious enabled services.
