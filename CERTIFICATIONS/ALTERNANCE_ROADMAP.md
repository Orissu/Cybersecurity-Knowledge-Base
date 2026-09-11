# 🎯 Alternance Roadmap — Basti's Personalized Plan

> **Last updated:** September 2026
> **Profile:** CPE Lyon / Lyon 1 double diploma (Réseau & Cybersécurité)
> Stage in California (IT support), returning to France August 2027.
> **Jury deadline:** End of March / April 2027
> **Alternance target:** September 2027, pentest focus

---

## 📋 TABLE OF CONTENTS

1. [Timeline Overview](#-timeline-overview)
2. [The Three Certifications](#-the-three-certifications)
3. [The Jury (CPE — March/April 2027)](#-the-jury-cpe--marchapril-2027)
4. [The Alternance Hunt](#-the-alternance-hunt)
5. [CV Template](#-cv-template)
6. [Target Companies](#-target-companies)
7. [Where to Find Offers](#-where-to-find-offers)
8. [Weekly Actions Tracker](#-weekly-actions-tracker)

---

## 📅 Timeline Overview

```
September 2026      Start Phase 0 (reboot) → Phase 1 (eJPT prep)
October 2026        eJPT EXAM ← first certification on CV
November 2026       Phase 2 begins (Security+)
December 2026       Security+ EXAM ← second certification on CV
January 2027        Phase 3 begins (PNPT prep — Active Directory focus)
February 2027       Full mock pentest + report writing practice
March 2027          PNPT EXAM ← third certification on CV
                    Push KB to GitHub with clean README ← portfolio visible
End March/April     CPE JURY — present your California training + 3 certs
May–August 2027     Active alternance hunt while continuing to train
August 2027         Return to France
September 2027      Alternance begins 🎯
```

---

## 🏆 The Three Certifications

### 🟢 eJPT — Target: October 2026

**What it is:** eLearnSecurity Junior Penetration Tester
**Format:** 100% practical lab. 3 days, 20 questions on a live network. No QCM.
**Price:** ~200$ for the exam voucher. Prep is free via INE Starter Pass.
**Prep time from your level:** 6-7 weeks (Phase 1 of the training plan)

**Why it's the right first cert:**
- It's practical, which means you study by doing — not memorizing
- It's recognized by French recruiters as a serious entry-level pentest cert
- It builds directly on THM/HTB skills you're developing anyway
- Passing it before November gives you something concrete to put on your CV for the rest of the hunt

**Key topics covered:**
- Network scanning and service enumeration
- Web application attacks (SQLi, XSS, authentication bypass)
- Network attacks and pivoting
- Metasploit and basic exploitation
- Post-exploitation basics

**Resources:**
- INE Starter Pass (free) → ine.com — create an account, access Penetration Testing Student course
- TryHackMe Jr Penetration Tester path (you have Premium ✅)
- Full prep details in `PENTEST_TRAINING_PLAN.md` Phase 1

**Action items:**
- [ ] Create INE account and access Starter Pass
- [ ] Start Jr Penetration Tester path on THM
- [ ] Book exam voucher before end of September (creates a deadline)
- [ ] Target exam window: October 20-25, 2026

---

### 🟡 Security+ — Target: December 2026

**What it is:** CompTIA Security+ (SY0-701)
**Format:** QCM + performance-based questions. 90 min. Pass mark: 750/900.
**Price:** ~380€ (book early — Pearson VUE, look for discount vouchers)
**Prep time:** 6-7 weeks after eJPT (Phase 2 of the training plan)

**Why it's the right second cert:**
- It's the most recognized cybersecurity cert by French HR departments and non-technical recruiters
- It covers concepts (risk, compliance, cryptography, network architecture) that complement your practical eJPT
- Doing it after eJPT means the theory maps to real experience — it sticks better
- Having two certs by January means your CV is competitive when offers start appearing in March

**Key domains covered:**
- General security concepts (12%)
- Threats, vulnerabilities & mitigations (22%)
- Security architecture (18%)
- Security operations (28%)
- Security program management (20%)

**Resources:**
- Professor Messer — professormesser.com (free, the definitive resource)
- ExamCompass.com — free practice questions by domain
- Full prep details in `PENTEST_TRAINING_PLAN.md` Phase 2

**Action items:**
- [ ] Start Professor Messer after eJPT exam
- [ ] Do 50 ExamCompass questions per domain before moving on
- [ ] Score >80% on full practice exam before booking
- [ ] Target exam window: December 15-20, 2026

---

### 🔴 PNPT — Target: March 2027

**What it is:** Practical Network Penetration Tester (TCM Security)
**Format:** 5-day lab + written professional report + 15-min oral presentation.
**Price:** ~400$
**Prep time:** 7-8 weeks of focused AD + reporting work (Phase 3 of the training plan)

**Why it's the right third cert — especially for your jury:**
The PNPT is the only entry/intermediate cert that requires you to write a professional report AND do an oral presentation. You'll arrive at your CPE jury having literally already done a professional oral presentation as part of a real certification. That's a unique angle.

Beyond the jury, the PNPT is well respected in the French pentest community because:
- It's a realistic pentest scenario, not a lab exercise
- The report requirement filters out candidates who can hack but can't communicate
- TCM Security (The Cyber Mentor) has strong credibility in the community

**Key topics covered:**
- Full Active Directory attack chain (enumeration → Kerberoasting → lateral movement → DCSync)
- Web application attacks
- Pivoting and tunneling
- Professional report writing (executive summary + technical findings + remediation)
- Oral presentation of findings

**Resources:**
- TCM Security Academy — Practical Ethical Hacking (PEH) course
- TCM Security Academy — Practical Active Directory Attacks
- Large free sections on YouTube: youtube.com/@TCMSecurityAcademy
- Full prep details in `PENTEST_TRAINING_PLAN.md` Phase 3

**Action items:**
- [ ] Start TCM Security PEH course after Security+ exam
- [ ] Set up AD lab (or use HTB Pro Lab / TCM lab)
- [ ] Write at least 2 full pentest reports before the real exam
- [ ] Target exam window: First week of March 2027

---

## 🎓 The Jury (CPE — March/April 2027)

### What the Jury Evaluates

The CPE / Lyon 1 double diploma jury evaluates your professional readiness and what you did during your California year. You need to show:
1. That you used the time productively and deliberately
2. That you're on a clear professional trajectory
3. That you have concrete, demonstrable skills — not just aspirations

### Your Story for the Jury

Here's the pitch to structure your presentation around:

> "I used my California stage to self-train intensively in cybersecurity, targeting a career in penetration testing. I obtained three certifications: eJPT (October 2026), CompTIA Security+ (December 2026), and PNPT (March 2027). The PNPT required me to conduct a complete penetration test against an Active Directory environment and deliver a professional written report, which I can share. In parallel, I built a public knowledge base documenting my techniques and methodology, available on GitHub. This gives me a strong foundation to contribute immediately in an alternance pentest role starting September 2027."

### What to Prepare Before the Jury

- [ ] KB pushed to GitHub with a clean, professional README (do this before March 1)
- [ ] PNPT report (or a sanitized version) ready to show or attach
- [ ] 3-5 HTB/THM writeups linked — demonstrate the practical work
- [ ] 2-minute spoken summary of your California experience linking IT support + cyber training
- [ ] Know your cert details: what each one covers, format, prep time — you'll be asked

### Connecting the Stage to the Cert Journey

Your IT support stage isn't unrelated to your cyber training — frame it deliberately:

- IT support exposed you to real infrastructure: AD environments, helpdesk tickets, network architecture, user management. This is the context a pentester needs to understand what they're attacking.
- Self-training in parallel shows initiative and autonomy — two qualities that matter enormously in consulting firms.
- Being in California adds an international dimension that differentiates you from candidates who did their stage at a local SME.

---

## 💼 The Alternance Hunt

### Timeline

| Period | Action |
|---|---|
| March 2027 | Jury done. CV finalized. LinkedIn updated. Alerts set. |
| March–April 2027 | First wave of applications (offers start appearing) |
| May–June 2027 | Main application push |
| July–August 2027 | Finalize and sign. Return to France. |
| September 2027 | Alternance begins |

### What Recruiters Look At (in order)

1. **Practical projects** — your GitHub KB, writeups, any scripts you built
2. **Platform profiles** — THM rank, HTB machines solved, activity visible
3. **Certifications** — confirm and credentialize the skills
4. **Diploma** — your CPE/Lyon 1 is recognized and sufficient for alternance

> Two candidates apply for the same role. One has Security+ and 20 THM rooms.
> The other has eJPT + Security+ + PNPT, an active HTB profile, a GitHub KB,
> and 3 written writeups. The second gets the interview every time.

### What to Have Ready Before Applying

- [ ] CV (see template below)
- [ ] GitHub KB with clean README — link in CV and LinkedIn
- [ ] LinkedIn profile updated with certs, projects, stage CA
- [ ] TryHackMe public profile visible (check your privacy settings)
- [ ] HackTheBox profile showing machines solved
- [ ] 3 clean writeups published (on GitHub or a simple blog)
- [ ] LinkedIn alert: `"alternance cybersécurité"`, `"alternance pentest"`, `"stage cybersécurité"`

---

## 📝 CV Template

Use this structure for your CV. Keep it 1 page max.

```
[Prénom NOM]
CPE Lyon / Université Lyon 1 — Ingénierie Réseaux & Cybersécurité
📍 Lyon (dispo sept. 2027) | 🔗 github.com/[username] | 🔗 linkedin.com/in/[username]

──────────────────────────────────────────────────────────
CERTIFICATIONS
  ✅ PNPT — TCM Security (mars 2027)
  ✅ CompTIA Security+ (déc. 2026)
  ✅ eJPT — INE (oct. 2026)

──────────────────────────────────────────────────────────
EXPÉRIENCE
  Support IT Intern — [Entreprise], Californie (2026-2027)
  • Administration systèmes Windows/Linux en environnement professionnel
  • Gestion AD, helpdesk, support réseau
  • Formation intensive en cybersécurité en parallèle (4-6h/jour)

──────────────────────────────────────────────────────────
PROJETS
  • Cybersecurity Knowledge Base (GitHub)
    → Documentation RED/BLUE Team, méthodologies pentest, writeups CTF
    → github.com/[username]/Cybersecurity-Knowledge-Base

  • [Si tu construis un script pendant le plan] Automated Recon Script (Python)
    → Automatisation nmap + ffuf + gobuster avec rapport markdown

  • Active Directory Lab (GOAD ou HTB)
    → Compromis un domaine de A à Z : Kerberoasting, ACL abuse, DCSync

──────────────────────────────────────────────────────────
PLATEFORMES
  • TryHackMe : Top X% — XX rooms complétées
  • HackTheBox : XX machines résolues (Easy/Medium/Hard)
  • PortSwigger Web Academy : XX labs complétés

──────────────────────────────────────────────────────────
COMPÉTENCES TECHNIQUES
  Pentest   : Nmap, Burp Suite, Metasploit, SQLMap, BloodHound, CrackMapExec
  Réseau    : TCP/IP, pivoting (Chisel, Ligolo-ng), Wireshark, Proxychains
  Windows   : Active Directory, PowerView, Impacket, Mimikatz (lab only)
  Linux     : Administration, privesc (LinPEAS, SUID/sudo/cron), bash
  Reporting : Rédaction de rapports pentest professionnels (format PNPT)
  Scripts   : Python, Bash

──────────────────────────────────────────────────────────
FORMATION
  Double Diplôme Ingénieur Réseaux & Cybersécurité
  CPE Lyon / Université Lyon 1 (2025-2027)
```

---

## 🏢 Target Companies

### Priority — Pentest-Focused (best for learning as a junior)

| Company | Why | Site |
|---|---|---|
| **Synacktiv** | Top pentest firm in France, very formateur | synacktiv.com/fr/jobs |
| **Quarkslab** | Research + pentest, strong technical culture | quarkslab.com/careers |
| **LEXFO** | Pentest specialist, known for quality | lexfo.fr |
| **Intrinsec** | SOC + pentest, active in alternance | intrinsec.com/recrutement |
| **Wavestone** | Large firm, structured alternance program | wavestone.com/fr/carrieres |
| **HarfangLab** | EDR / threat detection — if you want blue/purple | harfanglab.io/fr/carrieres |

### Secondary — Large Firms (more structured, less specialized)

- Orange Cyberdefense
- Thales / Airbus CyberSecurity
- Capgemini / Sopra Steria / Atos
- BNP Paribas, Société Générale (internal security teams)
- ANSSI (public sector, très sélectif but prestigious)

### Strategy: Apply to Priority First

The pentest-focused firms are more likely to give you real technical work as an alternant. Large firms can put you on compliance or documentation work — not what you want. Target Synacktiv, Intrinsec, and Wavestone first, then broaden if needed.

---

## 🔍 Where to Find Offers

| Platform | How to use |
|---|---|
| LinkedIn | Alert: `"alternance cybersécurité"` + `"alternance pentest"` |
| Welcome to the Jungle | welcometothejungle.com — good for startups and mid-size firms |
| APEC | apec.fr — more corporate, still worth checking |
| Company career pages directly | Check Synacktiv, Quarkslab, etc. every 2 weeks from March onward |
| CPE / Lyon 1 career portal | Your school likely has partnerships — ask the admin team |

**When to start actively applying:** March 2027, right after your jury. Offers for September tend to close in May/June.

---

## 📊 Profile Targets by March 2027

Set these as concrete goals to reach before you start applying:

| Metric | Minimum | Strong |
|---|---|---|
| Certifications | eJPT + Security+ | + PNPT |
| THM rooms completed | 30+ | 50+ |
| HTB machines solved | 5+ | 15+ |
| Writeups published on GitHub | 3 | 6+ |
| KB notes in the repo | 15+ | 30+ |
| GitHub KB README | Clean and readable | With screenshots |

---

## ✅ Weekly Actions Tracker

Check these off week by week during the alternance hunt phase (March–August 2027):

**March 2027 — Launch**
- [ ] CV finalized and proofread
- [ ] LinkedIn updated (certs, stage, projects, KB link)
- [ ] GitHub KB pushed with clean README
- [ ] Alerts set on LinkedIn, WTTJ, APEC
- [ ] First 5 applications sent to priority companies

**April 2027**
- [ ] 10+ applications sent total
- [ ] Followed up on applications from March
- [ ] Attended at least 1 cybersecurity event or meetup (Forum by Day, events CLUSIF, etc.)
- [ ] LinkedIn profile viewed 20+ times this month (means it's visible)

**May–June 2027**
- [ ] 20+ applications sent total
- [ ] At least 2 technical interviews completed
- [ ] Continued HTB training (2-3 machines/month minimum)
- [ ] At least 1 promise d'alternance signed or in progress

**July–August 2027**
- [ ] Alternance signed ✅
- [ ] Logistics sorted (housing in Lyon area)
- [ ] Return to France — August 2027

---

## 🧠 Mindset

Cybersecurity is one of the few fields where what you can demonstrate completely outweighs where you studied. Your CPE Lyon diploma opens the door. Your projects and certs make them keep it open.

Three things that matter more than anything else when applying for an alternance pentest:
1. Can you show that you've actually compromised machines — writeups, HTB profile, PNPT report
2. Can you communicate clearly — the PNPT oral presentation covers this
3. Are you consistently learning — the KB and THM/HTB activity show this

Everything in the training plan points to one of those three things. Trust the process.
