Contributing to the Linux kernel has evolved lately from a discovery into an important part of my software engineering journey. This post is to reflect and share a few milestones on my recent work as a contributor.

---

### 🔹 Upstream Video Driver Fixes

I worked on fixing bugs in the Qualcomm Venus video driver regarding how the packet parser tracked firmware response payloads. Without proper consumption tracking, the parser could drift and lose track of response frames. My patches were accepted upstream and backported into the 5.10, 5.15, 6.1, 6.6, and 6.12 stable trees.

---

### 🔹 Security Hardening (credentials and keys)

I contributed a hardening patch to cred_jar, which is the slab cache managing Linux credential objects. By enforcing SLAB_NO_MERGE, this change prevents this security-sensitive cache from merging with generic caches of an identical size. Even though this patch did not solve a real vulnerability issue but it provided a stronger layer of defensive isolation. The patch was reviewed by the security maintainer and merged into lsm/dev.

---

### 🔹 The Evolution of My Workflow

When I first started debugging complex memory issues reported by Syzbot, I didn’t use reproducers. I would attempt to compile heavy kernel debug configurations, loaded with KASAN and KMSAN, directly on my personal laptop.

It was painfully slow. I spent entire Sunday evenings waiting for builds to finish, only for them to fail or freeze due to a lack of local hardware resources. 

To solve this hardware bottleneck, I decided to rent a high-performance remote dedicated server, set up an SSH workflow, and use QEMU to reproduce bugs locally.

My kernel compilation times dropped drastically from hours to minutes! With the right infrastructure in place, I was able to deeply analyze complex memory bugs.

---

### 🔹 Reporting and fixing Memory Leaks in tmpfs Casefolding

While testing casefolding (case-insensitive file names) on tmpfs, which is a filesystem that runs entirely in RAM, I noticed a memory leak.

When a user repeatedly changed the mount options, the kernel allocated memory for tracking string encodings but forgot to free the old memory during the replacement.

By running a testing loop in QEMU, I watched active memory objects grow steadily from 660 to 50,574 objects without ever being reclaimed.

I wrote a fix to free the old structures correctly, but during the review process, we realized an identical patch had been submitted by another developer a month prior and was recently merged by the Memory Management maintainer.

While my patch was dropped as a duplicate, it was a good sign that my logic and debugging approach matched the solution chosen for the codebase. It also taught me a valuable lesson that I should thoroughly check all the active development trees first before diving into a fix.

---

### 🔹 Noticing architectural design choices while fixing a Syzbot bug report

Digging into a Syzbot kernel panic inside page_alloc, I investigated a watermark overflow constraint. The discussion with core maintainers was instructive and taught that before writing code to fix a perceived bug, it pays to take the time to thoroughly understand the ecosystem workflow (Syzbot root config bugs) and verify what the subsystem's architectural rules actually intended.

---

### 🎯 What’s Next?

Moving forward, my goal is to focus deeply on the Linux Memory Management (MM) subsystem. I want to dedicate time to testing, reproducing, and fixing more bugs to thoroughly understand how the system operates. Long-term, I plan to specialize in a specific area of MM, propose new features, and eventually maintain them upstream.
