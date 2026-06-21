# TryHelpMe TryHackMe Checklist

> A structured, beginner-friendly checklist to help you approach TryHackMe machines **methodically**, avoid common mistakes, and build a real hacker mindset.

[![TryHackMe Profile](https://img.shields.io/badge/TryHackMe-Loaymorad-red?logo=tryhackme&logoColor=white)](https://tryhackme.com/p/Loaymorad)
[![Repo Stars](https://img.shields.io/github/stars/loaymorad/TryHelpMe-TryHackMe-Checklist?style=social)](https://github.com/loaymorad/TryHelpMe-TryHackMe-Checklist/stargazers)
[![Last Commit](https://img.shields.io/github/last-commit/loaymorad/TryHelpMe-TryHackMe-Checklist)](https://github.com/loaymorad/TryHelpMe-TryHackMe-Checklist/commits/main)

---

## About

When I started solving TryHackMe machines, I kept forgetting steps, going in circles, or missing obvious things. So I built this checklist a living document I update every time I solve a machine and discover a new trick or approach worth remembering.

This is not a room list or a writeup collection. It's a **step-by-step methodology** to make sure you never skip a critical phase when attacking a machine.

> **Note:** This repo is updated continuously as I solve new machines and discover unique techniques worth documenting.

---

## Checklist Structure

The checklist is divided into 6 phases that mirror a real penetration testing workflow:

| # | Phase | What it covers |
|---|-------|----------------|
| 1 | [Recon & Enumeration](./(1)%20Recon%20%26%20Enumeration.md) | Port scanning, service detection, directory brute-forcing, subdomain enumeration |
| 2 | [Web Exploitation](./(2)%20Web%20Exploitation.md) | SQLi, XSS, LFI/RFI, IDOR, upload bypasses, common web vulnerabilities |
| 3 | [Credentials & Cracking](./(3)%20Credentials%20%26%20Cracking.md) | Hash cracking, brute-force, credential spraying, wordlist strategies |
| 4 | [Getting & Stabilising a Shell](./(4)%20Getting%20%26%20Stabilising%20a%20Shell.md) | Reverse shells, bind shells, TTY stabilisation, shell upgrades |
| 5 | [Binary Exploitation (RE)](./(5)%20Binary%20Exploitation%20(RE).md) | simple reverse engineering techniques |
| 6 | [Privilege Escalation](./(6)%20Privilege%20Escalation.md) | Linux/Windows privesc techniques, SUID, sudo misconfigs, cron jobs, tokens |
| 7 | [Finding Flags](./(7)%20Finding%20Flags.md) | Where to look, common flag locations, search commands |

This solves: ~85–90% Easy Rooms, ~60–70% Medium Rooms, ~30–40% Hard Rooms

---

## How to Use This Checklist

1. **Start a machine** on TryHackMe.
2. **Open the relevant phase** from the list above.
3. **Work through the checklist top to bottom** don't skip steps just because something "seems unlikely."
4. **Got stuck?** Check the next phase; sometimes you already have enough to move forward.
5. **Finished a machine?** Come back and see if a technique you used is missing from the list feel free to open an issue!

---

## Who Is This For?

- **Beginners** who feel lost when starting a new machine and don't know where to begin.
- **Intermediate players** who want a solid reference to avoid forgetting common techniques under pressure.
- **Anyone preparing for OSCP / eJPT** who wants to build good enumeration habits.

---

## About Me

Hi, I'm **Loay Morad** a 3rd-year Computer Science student and junior penetration tester.

I built this checklist from real experience solving TryHackMe machines. Every item in here comes from a machine I actually got stuck on, or a technique that helped me break through.

🔗 Check my [TryHackMe profile](https://tryhackme.com/p/Loaymorad) to see the machines I've solved that inspired this list.

---

<p align="center">
  Made with ❤️ by <a href="https://tryhackme.com/p/Loaymorad">Loay Morad</a> • Keep hacking, keep learning
</p>
