---
theme: css/dracula.css
height: "1080"
width: "1920"
share_cop4600: "true"
site-folder: docs/Lecture Slides
---
# Lecture 23: Containerized Applications


---

## Learning Objectives


1. Explain the underpinning technologies that enable application containerization
2. Compare traditional and containerized application models
3. Differentiate between Snap, Flatpak, and AppImage approaches
---

## I. Introduction: The Software Distribution Problem

---

### A. Traditional Application Deployment Challenges

- **Dependency hell**: Library version conflicts (libssl 1.0 vs 3.0)
- **Distribution fragmentation**: Packaging for Debian, Fedora, Arch, etc.
- **Security concerns**: System-wide library installations affect all applications
- **Update management**: Breaking changes propagate system-wide

---
### B. Historical Context

- Early package managers (dpkg, RPM - 1990s)
- Repository-based distribution models
- The rise of "works on my machine" syndrome

Who has experienced a system break after a library update?

---

## II. Underpinning Technologies for Application Isolation

### A. Linux Namespaces

**What they are:** Kernel features that partition system resources

**Key namespace types:**

- **PID namespace**: Process isolation (process sees only its own tree)
- **Mount namespace**: Isolated filesystem views
- **Network namespace**: Separate network stacks
- **User namespace**: UID/GID mapping (security boundary)
- **IPC namespace**: Isolated inter-process communication
- **UTS namespace**: Separate hostname/domain names

---
### B. Control Groups (cgroups)

- Resource limiting (CPU, memory, I/O)
- Accounting and monitoring
- Process grouping and management
- Prevention of resource exhaustion attacks
- Modern Linux uses cgroups via `systemd` to apply restrictions to user processes and system services
---
### C. Union/Overlay Filesystems

- **OverlayFS**: Layered filesystem approach
    - Lower layer: Read-only base system
    - Upper layer: Read-write application layer
    - Merged view presented to application
- Copy-on-write semantics
- Efficient storage (shared base layers)

---
### D. Seccomp and AppArmor/SELinux

- **Seccomp**: System call filtering
    - Whitelist allowed syscalls
    - Reduces attack surface
- **AppArmor/SELinux**: Mandatory Access Control
    - Path-based (AppArmor) vs label-based (SELinux) policies
    - Confine application capabilities

---
### E. Bubblewrap (for Flatpak specifically)

- Userspace sandboxing tool
- Creates unprivileged containers
- Builds on namespaces without requiring root

---

## **Key takeaway:** 
- ### These technologies existed separately; containerization brings them together in userspace tools.

---

## III. Traditional vs. Containerized Applications

---
### A. Traditional Application Model

```
Application → System Libraries → Kernel
     ↓
Shared dependencies (/lib, /usr/lib)
System-wide installation (/usr/bin, /etc)
```

---

**Characteristics:**
- Global installation (affects entire system)
- Shared library dependencies
- Tight coupling to OS version
- Requires package manager or manual compilation
- Privileged installation (typically requires root)

---

**Advantages:**

- Minimal disk usage (shared libraries)
- System-wide updates benefit all apps
- Direct hardware access

---

**Disadvantages:**

- Dependency conflicts
- Difficult rollbacks
- Security: one compromised library affects everything
- Distribution-specific packaging needed

---

### B. Containerized Application Model

```
Application + Dependencies + Runtime → Isolation Layer → Kernel
                                            ↓
                               Namespaces, cgroups, sandboxing
```

---

**Characteristics:**

- Self-contained bundle (app + dependencies)
- Isolated from host system
- Defined permission model
- Often unprivileged installation
- Version-independent of host OS

---

**Advantages:**

- No dependency conflicts
- Developer controls entire stack
- Enhanced security through isolation
- Distribution-agnostic
- Atomic updates/rollbacks

---

**Disadvantages:**

- Larger disk footprint
- Duplicate dependencies across apps
- Overhead from isolation layers
- Potential performance penalties

---
## IV. The Three Major Containerization Approaches

---

### A. Snap (Canonical/Ubuntu)

**Architecture:**

- **snapd**: System daemon (privileged, controls installation)
- **Base snaps**: Shared runtime environments
- **Squashfs**: Compressed, read-only filesystem images
- **AppArmor**: Mandatory confinement

---

**Key Features:**

- Transactional updates (atomic operations)
- Delta updates (bandwidth efficient)
- Automatic background updates
- Strict/classic confinement modes
    - Strict: Full sandboxing
    - Classic: Traditional access (for legacy apps)
- Cross-distribution support (but requires snapd)

---

**Confinement Model:**

```
Application
    ↓
Snap interfaces (declarative permissions)
    ↓
snapd mediates access
    ↓
System resources
```

---

**Interfaces:** USB access, network, home directory, etc.

**Criticism:**

- Proprietary backend (Snap Store)
- Slower startup (squashfs mounting)
- Closed development model
- Opinionated update policy

---
### B. Flatpak (Open Source Community)

**Architecture:**

- **Runtimes**: Shared base platforms (GNOME, KDE, Freedesktop)
- **OSTree**: Git-like version control for filesystems
- **Bubblewrap**: Sandboxing mechanism
- **Portals**: Controlled host system access

**Key Features:**

- Decentralized (any repository)
- Desktop-focused (X11/Wayland integration)
- Fine-grained permissions (Flatseal tool)
- Runtime sharing reduces duplication
- Sandboxing by default

---

**Portal System:**

```
Flatpak App → XDG Desktop Portal → Host System
                    ↓
          User-mediated access (file picker, etc.)
```

---
**Permissions Model:**

- Filesystem access (home, host, custom paths)
- Device access (DRI for GPU, /dev)
- Socket access (X11, Wayland, PulseAudio)
- More transparent and user-controllable
---
**Advantages:**

- Open infrastructure (Flathub)
- Better desktop integration
- Community-driven governance
---
**Criticism:**

- Complex permission system for users
- Runtime bloat if multiple runtimes installed
---
### C. AppImage

**Philosophy:** "One app = one file"

**Architecture:**

- **Self-contained executable**: ELF binary + squashfs
- **No daemon required**: Double-click to run
- **FUSE-based**: Mounts itself at runtime
- **Optional sandboxing**: firejail, bubblewrap (not built-in)

**Key Features:**

- Distribution-agnostic (truly portable)
- No installation required
- No root privileges needed
- User controls updates (manual)
- Can run from removable media

---

**Execution Model:**

```
AppImage file → FUSE mount → chroot-like environment → Execute
```

---

**Advantages:**

- Ultimate portability
- No system integration required
- User has complete control
- Works on ancient distributions (with old enough base)

**Disadvantages:**

- No automatic updates
- No centralized repository (by design)
- Each AppImage bundles everything (largest disk usage)
- No sandboxing by default
- Desktop integration requires external tools (AppImageLauncher)

---
### Comparison Matrix

| Feature                 | Snap                     | Flatpak               | AppImage           |
| ----------------------- | ------------------------ | --------------------- | ------------------ |
| **Daemon required**     | Yes (snapd)              | Yes (flatpak)         | No                 |
| **Sandboxing**          | Built-in (AppArmor)      | Built-in (bubblewrap) | External           |
| **Updates**             | Automatic                | User/automatic        | Manual             |
| **Repository**          | Snap Store (centralized) | Decentralized         | None (distributed) |
| **Runtime sharing**     | Base snaps               | Runtimes (efficient)  | None               |
| **Desktop integration** | Good                     | Excellent             | Manual             |
| **Startup overhead**    | Higher                   | Medium                | Lower              |
| **Philosophy**          | Universal packages       | Desktop apps          | Portable apps      |

---
