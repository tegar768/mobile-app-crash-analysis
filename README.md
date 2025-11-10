# Jetsam-Event-Investigation-for-iOS

## Introduction 💡  
When I was playing *Duet Night Abyss* on my iPad 9, the game suddenly force-quit.  
At first, I thought it was a random bug — until I checked the system log and found a **Jetsam Event**.  

This repository documents my QA-style investigation on that event:  
how iOS memory management works, why Jetsam kills apps,  
and how to analyze such logs as a part of *performance testing*.  

---

### Table of Contents
1. [What is Jetsam?](#what-is-jetsam)
2. [The Investigation Process](#the-investigation-process)
3. [Memory Pressure Explained](#memory-pressure-explained)
4. [Test Environment](#test-environment)
5. [Findings](#findings)
6. [Conclusion](#conclusion)
7. [Resources](#resources)

---

### What is Jetsam?  
Jetsam is iOS’s internal process that monitors memory usage.  
When available memory drops too low, Jetsam terminates background or foreground apps  
to keep the system stable — it’s not a crash, it’s a controlled kill.  

> In simple terms: your app didn’t “bug out”, it got *sacrificed* to save iOS.  

---

### The Investigation Process  
Here’s how I approached it as a QA:  
1. Extracted the `.ips` Jetsam log from the iPad.  
2. Filtered process name and memory footprint.  
3. Identified total device memory usage at kill moment.  
4. Correlated with gameplay condition — high GPU + RAM load.  

All raw logs are in `/logs/`, and analysis in `/analysis/jetsam.md`.  

---

### Memory Pressure Explained  
When memory is heavily consumed, iOS divides it into pressure levels:
- **Normal** → plenty of free memory.  
- **Warning** → system starts compressing inactive pages.  
- **Critical** → Jetsam starts killing the largest memory consumer.  

In this case, the *Duet Night Abyss* process exceeded safe limits during rendering,  
triggering Jetsam with reason `"per-process-limit"`.  

---

### Test Environment  
| Device | OS Version | Game Version | Free RAM | Status |
|--------|-------------|--------------|----------|--------|
| iPad 9 (2021) | iPadOS 17.5 | Duet Night Abyss 1.0.6 | ~1.2 GB | Jetsam killed process |

---

### Findings  
- The log shows **high resident memory (≈1.4 GB)** before termination.  
- No crash signatures detected, confirming it was **system-level kill**, not bug.  
- GPU + physics engine peak coincides with memory spike.  

✅ **Conclusion:** The game didn’t crash due to bug,  
but due to **iOS memory pressure** leading Jetsam to end it.  

---

### Resources  
- [Apple Docs – Understanding Jetsam Events](https://developer.apple.com/forums/thread/678372)  
- [WWDC 2020 – Memory Deep Dive](https://developer.apple.com/videos/play/wwdc2020/10163/)  
- [iOS System Diagnostics – Reading .ips Files](https://support.apple.com/en-us/HT202759)  

---

back to [Intro Page](/README.md)
