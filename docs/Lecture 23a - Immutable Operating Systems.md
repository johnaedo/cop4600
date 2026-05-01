---
share_cop4600: "true"
---

## V. Immutable/Atomic Operating Systems

### A. The Paradigm Shift

**Traditional OS:**

```
Mutable /usr, /lib, /bin → Package manager modifies → System state changes
```

Problems: Unpredictable state, difficult rollbacks, broken updates

**Immutable OS:**

```
Read-only /usr, /lib → Atomic image-based updates → System state A or B
```

### B. Examples and Approaches

**1. Ubuntu Core**

- Everything is a snap (kernel, OS, apps)
- Transactional updates
- Designed for IoT/embedded
- Full system rollback capability

**2. Fedora Silverblue/Kinoite**

- OSTree-based (rpm-ostree)
- Immutable /usr
- Layering model: base image + layered packages
- Toolbox containers for development
- Designed for desktop workstations

**3. Bazzite**

- Gaming-focused (based on Fedora Atomic)
- SteamOS-like experience
- Demonstrates specialized immutable derivatives

**4. NixOS** (Special case - covered next section)

### C. Why Containerized Apps + Immutable OS?

**The Perfect Marriage:**

- **Immutable OS**: Handles system components
    - Kernel, drivers, core utilities
    - Image-based updates
- **Containerized apps**: Handle applications
    - User-facing software
    - Independent lifecycle

**Benefits:**

- Clear separation of concerns
- OS updates don't break apps
- Apps don't break OS
- Reliable rollbacks at both levels
- Reproducible systems

**Example workflow (Silverblue):**

```
Base OS image (read-only)
    ↓
rpm-ostree overlay (system modifications)
    ↓
Flatpak apps (user applications)
    ↓
Toolbox/Distrobox (development environments)
```

---

## VI. NixOS: A Unique Approach (10 minutes)

### A. Functional Package Management

**Core Concept:** Packages as functions with inputs/outputs

```
package = function(dependencies, configuration)
```

**Key principles:**

1. **Purely functional**: Same inputs → Same output
2. **Immutable store**: `/nix/store/hash-package-version/`
3. **Declarative configuration**: Entire system defined in configuration.nix

### B. The Nix Store

```
/nix/store/
  ├── a1b2c3d4-glibc-2.38/
  ├── e5f6g7h8-python-3.11.5/
  └── i9j0k1l2-myapp-1.0-depends-on-e5f6g7h8/
```

**Characteristics:**

- Content-addressed (hash includes all dependencies)
- Multiple versions coexist peacefully
- Atomic upgrades (symlink switching)
- Garbage collection for unused packages

### C. Reproducible Builds

**The Promise:** Build the same configuration on any machine → identical result

**How it works:**

1. Lock file (flake.lock) pins all input versions
2. Pure evaluation (no network, no system state during build)
3. Sandboxed builds (namespaces, no /tmp access)
4. Bit-for-bit reproducibility (when possible)

**Example:**

```nix
{
  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-23.11";
  };
  
  outputs = { self, nixpkgs }: {
    nixosConfigurations.mySystem = nixpkgs.lib.nixosSystem {
      system = "x86_64-linux";
      modules = [ ./configuration.nix ];
    };
  };
}
```

This configuration will produce the same system anywhere.

### D. Atomic Rollbacks

```
/nix/var/nix/profiles/system-1-link → build A
/nix/var/nix/profiles/system-2-link → build B (current)
/nix/var/nix/profiles/system-3-link → build C
```

Boot menu shows all generations; select to rollback instantly.

### E. NixOS vs. Traditional Containerization

**Similarities:**

- Isolation philosophy
- Reproducibility goals
- Declarative configuration

**Differences:**

- NixOS: System-level immutability through functional paradigm
- Containers: Application-level isolation through kernel features
- NixOS doesn't need containers (but can use them)
- Each Nix package is somewhat "containerized" via unique paths

**Relation to containerized apps:**

- NixOS can build container images reproducibly
- `dockerTools.buildImage` creates Docker images from Nix expressions
- Same principles: Dependencies bundled, isolated, reproducible

---

## VII. The Future: Speculation and Trends (5 minutes)

### A. Convergent Trends

**1. Universal App Format?**

- OCI (Open Container Initiative) images for desktop?
- Wasm components as universal binaries?
- Cross-platform (Linux, Windows, macOS) single format

**2. Declarative Everything**

- Following NixOS/Nix model
- Infrastructure as Code principles moving to desktop
- GitOps for personal computers

**3. Security as Default**

- Sandboxing becomes expected, not optional
- Zero-trust application model
- eBPF-based runtime security monitoring

**4. Edge Computing Influence**

- Immutable systems for reliability
- Container-first design
- Minimal attack surface

### B. Operating System Evolution

**From:** OS as kitchen (everything installed, messy)  
**To:** OS as hotel (transient guests, isolated rooms)

**Possible futures:**

**1. The Minimal Kernel OS**

- Kernel + container runtime
- Everything else is containerized
- OS becomes a "container scheduler"
- Examples: Flatcar Container Linux, k3OS

**2. The Declarative OS**

- Entire system as code (NixOS model wins)
- Version-controlled infrastructure
- Reproducible personal/enterprise environments
- Time-travel debugging (rollback to any state)

**3. The Unikernel Approach**

- Each app brings its own minimal kernel
- No shared OS layer
- Ultimate isolation
- Examples: MirageOS, Unikraft

**4. WebAssembly as Universal Runtime**

- WASI (WebAssembly System Interface) matures
- Apps compile to Wasm, run anywhere
- True "write once, run anywhere"
- OS provides Wasm runtime + hardware access

### C. Challenges Ahead

**Technical:**

- Performance overhead from isolation
- Disk space (deduplication needed)
- GPU/hardware access in sandboxes
- Real-time requirements

**Social:**

- User education (permissions, updates)
- Developer adoption
- Backward compatibility
- Centralization concerns (app stores)

**Philosophical:**

- Control: Who decides what runs? (automatic updates)
- Freedom: Locked-down vs. open systems
- Complexity: Are we making things harder?

---

## VIII. Conclusion and Takeaways (5 minutes)

### Key Insights

1. **Containerization leverages kernel features** (namespaces, cgroups) that existed for years—userspace tools made them accessible
    
2. **Different approaches serve different needs:**
    
    - Snap: Enterprise, IoT, controlled updates
    - Flatpak: Desktop, user freedom, sandboxing
    - AppImage: Portability, simplicity, user control
3. **Immutable OSes are the natural partner** to containerized apps—separation of concerns between system and applications
    
4. **NixOS represents a radical rethinking**—reproducibility through functional programming rather than containers
    
5. **The future is declarative, isolated, and reproducible**—trends point toward systems as code, apps as isolated units
    

### Discussion Questions

- Is the overhead of containerization worth the benefits?
- Will declarative configuration (NixOS-style) become mainstream?
- Can we have both user control and security?
- What role will AI play in managing these complex systems?

### Further Exploration

- Set up a Fedora Silverblue VM, install Flatpaks
- Try NixOS with flakes
- Build an AppImage from a simple application
- Compare Docker containers to system-level containers

---

## Lab Assignment Suggestion

**Build and Compare:**

1. Package a simple application (e.g., a Python script) as:
    
    - Traditional DEB/RPM
    - Flatpak
    - AppImage
2. Compare:
    
    - Build complexity
    - Runtime size
    - Startup time
    - Isolation effectiveness (try escaping the sandbox)
3. Bonus: Create a NixOS configuration that declaratively sets up a development environment
    

---

**End of Lecture**

This represents a fundamental shift in how we think about operating systems—from monolithic, mutable entities to composable, immutable platforms. The principles you've learned here extend beyond Linux to influence cloud computing, embedded systems, and even how we think about software engineering itself.