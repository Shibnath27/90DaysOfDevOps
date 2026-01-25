# Linux Process Management – Notes

## What is a Process

* A process is a running instance of a program
* Each process has a unique PID (Process ID)
* Processes are managed by the Linux kernel

---

## Process States (Common)

* **Running (R)**

  * Process is actively using CPU

* **Sleeping (S)**

  * Waiting for I/O, network, or timer
  * Most processes stay in this state

* **Uninterruptible Sleep (D)**

  * Waiting for disk or hardware
  * Cannot be killed easily

* **Stopped (T)**

  * Process paused manually (Ctrl+Z or debugger)

* **Zombie (Z)**

  * Process completed but parent didn’t collect exit status
  * No CPU or memory usage

---

## Daily-Use Commands

* **ps aux**

  * List all running processes

* **top / htop**

  * Real-time CPU and memory monitoring

* **systemctl status <service>**

  * Check service state and health

* **kill / kill -9 <PID>**

  * Stop unresponsive processes

* **journalctl -u <service>**

  * View service logs for troubleshooting

---

## Practical Usage

* High CPU usage → check with `top`
* Service not working → use `systemctl` + `journalctl`
* Zombie processes → usually parent process issue
* Avoid killing critical system processes

