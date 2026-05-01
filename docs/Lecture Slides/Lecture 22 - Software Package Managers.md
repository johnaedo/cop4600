---
share_cop4600: "true"
site-folder: docs/Lecture Slides
---

## Questions Answered in this Lecture
- How do we add new software to a system?
- How do packages help with software management?
- What role does the repository play in software management?
- What issues remain to be solved?
- What's the future of software management?

---

### Software Installation, The Old Way
#### UNIX
`tar -xvf software.tar /usr/local/bin`
- tar file : *TAPE Archive*
- Files delivered uncompressed, "taped" together
- Later we got Z and GZip compression

---
### Software Installation, The Old Way
#### MS-DOS
```
MKDIR C:\MYPROGRAM
A:
COPY *.* C:\PROGRAM
```
Often conveniently automated via Batch Files/Batch Scripts

---
## Software Installers
- Software gets more complex, more files, more target directories.
	- /usr/local/bin
	- /usr/local/lib
	- /etc/myconfig.conf
- Software also needs initial configuration
	- Where do we store this configuration?
- What about dependent libraries?  Where do these go?

---
## The Mess on Windows
- C:\Program Files
- C:\My Documents

- **The Registry**

- %APPDATA%
- C:\ProgramData
---
## Uninstalling!
- No consistent method for removing software
- Deleting files isn't enough!
	- Registry entries
	- Configuration files
	- User data?
	- Libraries?
- Windows has tried (and largely failed) to standardize software installations
	- Legacy software remains a problem
---
## Packages... What If?...
- We had standardized destination locations for software?
- We could handle dependencies in a rational manner?
- We had a mechanism that could properly uninstall software?
- This mechanism was well-documented and openly available to ALL developers for a platform?
Packages can do all of this because it fully standardizes the entire software installation, update, and uninstall lifecycle.

---
## Components of a Package  
- Application / library files
	- These can either be binary or source code files, depending on the package manager.  
- Metadata
	- Contains information about the package itself, including its author, version,  dependencies, etc.  
- Cryptographic keys
	- Used to ensure that this package was created by the listed author, and that the  
installed files have maintained its integrity.  

---
## Package Repositories  
- Storage Location for software packages  
- Official repositories are often protected against malware
- Official packages often have requirements to ensure quality, and cryptographic keys verify integrity and authentication.  
- Official repositories are often split by package type (core vs. extra) or version (stable vs. experimental).  
- Official repositories often have many mirrors for fast downloads.

---
## Package Repositories (unofficial)
- Unofficial repositories are community-managed and open to submission.
	- Arch User Repository (AUR)
	- Personal Package Archives (PPA, Ubuntu)
	- Community Projects (CoPr, Fedoraa)
- Available software is much more vast (esp. AUR)
- Packages are not vetted as well, may contain malware.
- Often not supported by the original developer

---
## Dependency Management  
- Software packages have dependencies, other packages that need to be installed for a package to function properly.  
- These dependencies often have version requirements that ensure that the required package functions as expected.
---
## Other Relationships  
- **Conflict**: Two packages do not function around each other. One will need to be removed for the other to work properly.  
- **Provide**: A package that is installed by another package for use by a user. Virtual packages (a package that has no files) often implements this relationship.  
- **Suggest**: A package that works well with or improves the functionality of the package.  
- **Replace**: A package that provides the functionality of another package without providing it. 
---
## Version Management  
- The version number of each package is kept in its metadata. The format of this value is usually kept consistent within a repository.
- Used to ensure that the the user can access latest version of the software, and that packages can access the version of its requirements that allows it to work.  
- It's up to the package maintainer to ensure that the package works with the most recent version of its dependencies
---
## Package Manager Usage  
- **Install**  
- **Search** (locally installed packages or packages available on the repository)  
- **Update**  (individual packages or the entire system)
- **Remove** (uninstall)
- **Purge** (more thorough Remove, deletes config files and user data)

---
## Package Manager vs Installer  
- Application installer is similar to a package, as it handles the installation of a piece of software, its dependencies, and its configuration, and often sets up methods to upgrade and uninstall its application.  
- However, it is developed for a specific application, limits its scope to that specific application, and often does not share libraries with other applications.

---
## Package Manager vs. App Store  
- App stores are similar to package managers, but are often designed by software vendors, include methods to accept payment, and often only provide applications.

---
## Package Manager Risks  
- Official repositories often use different forms of quality control to  
ensure that the software packages it provides are safe to use,  
but broken software and malware can still creep in.  
- An example of this is the ethers-provider2 npm package1, which  
was recently discovered to contain a reverse shell (attackers  
can remotely execute code on host machine)  

---
## Advantages of Package Managers  
- Provides a service to easily and quickly install applications and libraries.  
- Allows for updating multiple applications at once.  
- Allows installed libraries to be shared, reducing applications spacial cost.  
- Allows for the ability to automate the installation of applications.  

---
## Disadvantages of Package Managers  
Difficult to use for people with less computer experience.  
Packages may interfere with one another, affecting their ability to function.  
Depending on the package manager, improper usage may impede the computer’s ability to function.  
Repository may not be comprehensive, may be missing some software.

---
## Dependency Hell
- Packages don't completely solve the dependency problems since software development isn't synchronized.
	- Package X requires Library Y version 2.0, but upgrading Library Y will break Package Z
- Package and Repository Maintenance is labor-intensive
- Ordinary packages don't run in isolation, often having access to the entire system
---
## Containerized Applications
- Storage is cheap, why don't we ship applications with ALL of their dependencies?
- Modern systems have containerization mechanisms allowing applications to run in isolation with select privileges.
- Current popular formats:
	- Flatpaks
	- Snaps
	- AppImages
- More next lecture!
---
## The Future of Operating Systems?
- Immutable/Atomic Operating Systems
	- Core system software is read-only and atomically updated.
	- Each update creates a snapshot, failed updates can be rolled back
	- User Applications run in containers
- Examples: Fedora Silverblue, Bazzite, NixOS, VanillaOS, Ubuntu Core