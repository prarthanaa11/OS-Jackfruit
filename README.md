🧠 OS Jackfruit — Lightweight Container Runtime
A minimal, Linux-based container runtime built using low-level system programming concepts like namespaces, kernel modules, and IPC.

👥 Team Information
PRARTHANA D ACHARYA- PES2UG24AM119
NIDHI- PES2UG24AM102



Setup Instructions:
Follow these steps to set up the project:

# Clone the repository
git clone https://github.com/shivangjhalani/OS-Jackfruit.git

# Navigate to project
cd OS-Jackfruit/boilerplate

# Build the project
make clean
make

# Load kernel module
sudo insmod monitor.ko

# Verify device creation
ls /dev/container_monitor

# (If needed) Fix /proc mount
sudo mount -t proc proc /proc



Run Instructions:
🔹 Start Supervisor
sudo ./engine supervisor ../rootfs-base
🔹 Start Containers
sudo ./engine start alpha ../rootfs-alpha /bin/sh
sudo ./engine start beta ../rootfs-beta /bin/sh
🔹 List Running Containers
sudo ./engine ps
🔹 View Logs
sudo ./engine logs alpha
💥 Memory Stress Test
cp memory_hog ../rootfs-alpha/
chmod +x ../rootfs-alpha/memory_hog

sudo ./engine start alpha ../rootfs-alpha /memory_hog
🔍 Check Kernel Logs
sudo dmesg | grep monitor
🛑 Stop Containers
sudo ./engine stop alpha
sudo ./engine stop beta
🧹 Cleanup Verification
ps aux | grep defunct
🔻 Unload Kernel Module
sudo rmmod monitor



📸 Screenshots (Execution Proof)
ScreenshotS:	

Kernel Module Loaded	/dev/container_monitor created successfully
<img width="1167" height="515" alt="image" src="https://github.com/user-attachments/assets/2b38491a-f265-447e-bcf2-a0448623b56d" />

Containers Running	Supervisor + multiple containers active
<img width="1427" height="443" alt="image" src="https://github.com/user-attachments/assets/6420791c-c8bb-40a8-936a-61fbc28e22ca" />


engine ps Output	Shows container IDs and PIDs
<img width="1419" height="427" alt="image" src="https://github.com/user-attachments/assets/ed258d4e-63b0-4f57-9585-4f189bdb8d9a" />

Logs Output	Container execution logs
<img width="1414" height="447" alt="image" src="https://github.com/user-attachments/assets/3d45dc39-b60c-49f3-aeb3-9c0647878994" />

Soft Limit Trigger	Warning in kernel logs

Hard Limit Trigger	Container killed due to limit
<img width="947" height="732" alt="image" src="https://github.com/user-attachments/assets/2a7fb622-a3f3-4fe1-80d2-eb25f9b478b0" />

Stop Command	Container termination confirmation
<img width="1031" height="295" alt="image" src="https://github.com/user-attachments/assets/663c33c3-9f17-495f-ad61-305344f93dae" />

No Zombie Processes	No <defunct> processes present
<img width="1026" height="379" alt="image" src="https://github.com/user-attachments/assets/c077befa-7362-45ba-9b76-70f9bc33bff3" />





Engineering Analysis:
🔹 Namespace Isolation
PID Namespace (CLONE_NEWPID) → Independent process IDs
UTS Namespace (CLONE_NEWUTS) → Custom hostname per container
Mount Namespace (CLONE_NEWNS) → Isolated filesystem

➡️ Containers behave like independent systems.




Inter-Process Communication (IPC):
Method: UNIX Domain Sockets
Path: /tmp/mini_runtime.sock
Flow: CLI ↔ Supervisor

Advantages:

Low latency
No network overhead
Secure local communication



Memory Monitoring (Kernel Module):
Uses get_mm_rss() for memory tracking

Enforcement:

Soft Limit → Warning logged
Hard Limit → Process killed (SIGKILL)

➡️ Enables real-time resource control.




Process Scheduling Analysis:
Tested using cpu_hog
Priority controlled via nice values

Observations:

Lower nice → Higher priority → Faster
Higher nice → Lower priority → Slower



Design Decisions:
Component	Choice	Reason
IPC	UNIX Domain Socket	Efficient & simple
Process Creation	clone()	Lightweight
Filesystem Isolation	chroot()	Secure
Memory Tracking	Kernel Module	Low-level accuracy
Container Storage	Linked List	Dynamic management
Process Cleanup	SIGCHLD	Prevent zombies



Scheduling Results:
Process	Nice Value	Behavior
cpu_hog (default)	0	Normal execution
cpu_hog (low priority)	10	Slower
cpu_hog (high priority)	-5	Faster



Analysis:
Linux scheduler favors lower nice values
Demonstrates fairness and CPU sharing
Confirms scheduling impact on performance



Conclusion:

This project successfully demonstrates:

Linux containerization using namespaces
IPC via UNIX domain sockets
Kernel-level memory monitoring
Process scheduling behavior
Proper lifecycle management (no zombies)

➡️ Combines user-space and kernel-space to simulate a real container runtime.

Outcomes
Linux namespaces (PID, UTS, Mount)
Kernel module development
System programming with clone()
Memory & process management
IPC and synchronization
