<img src="https://github.com/mytechnotalent/windows-kernel-debugging/blob/main/Windows-Kernel-Debugging.png?raw=true">

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/Reverse-Engineering-Tutorial)

<br>

# Windows Kernel Debugging
A guide to get you started with Windows Kernel Debugging walking you through the complete setup and usage of WinDbg to trace Windows process creation at the kernel level, from boot to PspCreateProcess, using VMware Workstation.

---

## 🧰 Environment Overview

### Host Machine
- Windows OS (any version)
- WinDbg Preview (from Microsoft Store)
- VMware Workstation

### Guest VM
- Windows 10 x64
- Configured for COM-based kernel debugging via named pipe

---

## ⚙️ Configure the Guest VM

1. **Shut down the VM**
2. **Add Serial Port in VMware**:
   - VM Settings → Add → Serial Port
   - Output to named pipe
   - Pipe name: `\\.\pipe\com1`
   - This end is the server ✅
   - The other end is an application ✅

3. **Enable Kernel Debugging in Guest OS**:
   Open Command Prompt as Administrator inside the VM and run:
   - `bcdedit /debug on`
   - `bcdedit /dbgsettings serial debugport:1 baudrate:115200`

4. **Reboot the VM**

---

## 🧠 Launch WinDbg on Host

1. Run WinDbg as Administrator (on the host machine)
2. Go to `File → Kernel Debug → COM`
3. Set the following:
   - Port: `\\.\pipe\com1`
   - Baud: `115200`
   - Pipe: ✅
   - Reconnect: ✅
   - Break on Connection: optional
   - Resets: `0`

Click **OK**. WinDbg will say:
`Waiting to reconnect...`

---

## 🚦 Establish the Debug Connection

1. Reboot the guest VM.
2. WinDbg on the host should automatically connect:
- `Connected to target Windows 10...`
- `Kernel Debugger connection established.`
3. Load symbols:
- `.reload /f`

---

## 🎯 Trace Process Creation

1. Set a breakpoint on process creation:
- `bp nt!PspCreateProcess`
- `g` to continue

2. Inside the VM, launch a user-mode process (e.g. `notepad.exe`)

3. WinDbg will pause in `PspCreateProcess` — the kernel is actively creating a new `EPROCESS`.

---

## 🔍 Inspect Process Internals

- Dump the current process:
- `!process 0 0`
- Check image name:
- `dx ((nt!_EPROCESS*) @rcx)->ImageFileName`
- View call stack:
- `k`
- View command line:
- Inspect `RSP` → extract `_RTL_USER_PROCESS_PARAMETERS`

---

## 🧪 Troubleshooting Tips

- **WinDbg won’t connect**:
- Ensure VM pipe and debugger port match (`\\.\pipe\com1`)
- Verify bcdedit settings inside the guest
- Reboot VM with WinDbg already listening

- **VM hangs on boot**:
- Kernel is paused — type `g` in WinDbg to continue

- **Symbols don’t resolve**:
- Use `.symfix` followed by `.reload /f`

---

## 🧠 Extras

- Add breakpoints to `PspCreateThread`, `NtCreateUserProcess`
- Use `!handle`, `!token`, or `!object` for kernel object insight
- Explore `EPROCESS.ActiveProcessLinks` for the full process list

---

<br>

## License
[MIT](https://github.com/mytechnotalent/windows-kernel-debugging/blob/master/LICENSE.txt)
