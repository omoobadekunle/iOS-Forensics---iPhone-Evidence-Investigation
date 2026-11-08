# SBT-DF205 Case 2: iOS Forensics Examination Report

![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
![Evidence](https://img.shields.io/badge/Evidence-iPhone%20SE%20(iOS%2013.4.1)-000000?logo=apple&logoColor=white)
![Module](https://img.shields.io/badge/Module-SBT--DF205-blue)
![Status](https://img.shields.io/badge/Status-Completed%20(partial%20artefacts%20outstanding)-yellow)

A forensic examination of a supplied iPhone SE dataset running iOS 13.4.1, carried out as part of the **Mobile and IoT Forensics** module (Module 2: iOS Forensics) at ICDFA.

| | |
|---|---|
| **Module** | SBT-DF205 Mobile and IoT Forensics, Module 2: iOS Forensics |
| **Case reference** | SBT-DF205-C2 |
| **Case** | iPhone SE, iOS 13.4.1 |
| **Student** | Adekunle Ogunyemi |
| **Environment** | Kali Linux |
| **Evidence source** | Joshua Hickman's iOS 13.4.1 iPhone SE dataset, Digital Corpora |
| **Time standard** | UTC, unless otherwise stated |

---

## Table of Contents

1. [Overview](#overview)
2. [Executive Summary](#executive-summary)
3. [Objectives](#objectives)
4. [Tools and Environment](#tools-and-environment)
5. [Evidence Handling and Methodology](#evidence-handling-and-methodology)
   - [Evidence Integrity](#evidence-integrity)
   - [Working Directory Structure](#working-directory-structure)
6. [Device Information, Accounts and Settings](#device-information-accounts-and-settings)
7. [Messages, Contacts, Calls and Attachments](#messages-contacts-calls-and-attachments)
8. [Safari and Calendar Examination](#safari-and-calendar-examination)
9. [Photographs and Media](#photographs-and-media)
10. [KnowledgeC Activity](#knowledgec-activity)
11. [Health and Location Evidence](#health-and-location-evidence)
12. [Timeline of Events](#timeline-of-events)
13. [Key Correlations and Alternative Explanations](#key-correlations-and-alternative-explanations)
14. [Limitations and Outstanding Work](#limitations-and-outstanding-work)
15. [Repository Structure](#repository-structure)
16. [Screenshot Index](#screenshot-index)
17. [Key Learning Outcomes](#key-learning-outcomes)
18. [Disclaimer](#disclaimer)
19. [Author](#author)

---

## Overview

This repository documents my iOS forensic examination of a supplied iPhone SE dataset associated with iOS 13.4.1. The exercise required me to investigate different iOS artefacts and use their contents to reconstruct a supported sequence of device-related activity.

I approached the examination by preserving the source evidence, creating a separate working area, identifying relevant databases and property lists, and extracting records using command-line and database tools. I also paid close attention to interpreting timestamps correctly and to distinguishing information stored directly in an artefact from conclusions that need further evidence.

---

## Executive Summary

In this laboratory exercise, I carried out a forensic examination of a supplied iPhone SE dataset associated with iOS 13.4.1. I used Kali Linux to examine the available digital evidence, extract relevant records and compare information from different artefacts. My main objective was to understand what the evidence could establish about device setup, messages, attachments, Health activity and location-related events.

I began by checking the integrity of the supplied evidence archive. I used 7-Zip to test the ZIP archive, and the result was "Everything is Ok." I also recorded its size and SHA-256 hash. I then worked with extracted files in a separate working directory so that my examination would not intentionally alter the original evidence.

During the examination, I analysed an Apple setup property list, the Messages database, recovered message attachments, the Health database and the location-related database.

**Key findings**

- The setup property list recorded that setup had been completed, with a `SetupLastExit` date of **21 March 2020**.
- The Messages database contained records dated between **12 and 16 April 2020**.
- Two messages referred to PDF documents that I identified in the recovered attachment directory.
- I identified **six distinct workout records** dated between 27 March and 3 April 2020.
- The location database contained visit records with entry and exit times, coordinates, uncertainty values and data-point counts. Some message timestamps fell within a recorded location interval, but this alone cannot prove continuous presence at one precise location or who sent the messages.

My conclusions are limited to the records I examined, and further corroboration is needed before making stronger claims about user identity, exact location or the circumstances surrounding individual events.

---

## Objectives

- Preserve the supplied evidence and record its integrity.
- Examine device setup information, settings and account-related artefacts.
- Extract messages and trace their associated attachments.
- Examine Safari history and Calendar records.
- Investigate photographs and media metadata.
- Examine KnowledgeC activity records.
- Analyse Health and location-related records.
- Construct a timeline of at least 12 events from four or more artefact families.
- Identify correlations, consider alternative interpretations and explain the limitations of the findings.

---

## Tools and Environment

| Tool | Purpose |
|---|---|
| Kali Linux | Examination environment |
| 7-Zip | Archive integrity testing |
| `sha256sum` | SHA-256 hashing of evidence and recovered files |
| Python | Parsing property-list (plist) data |
| SQLite | Querying iOS databases |
| Timestamp conversion | Expressing Apple epoch values as UTC |

---

## Evidence Handling and Methodology

### Evidence Integrity

I tested the evidence archive with 7-Zip, and it passed the tool's integrity test.

| Property | Result |
|---|---|
| Filename | `ios_13_4_1.zip` |
| File size | 9,629,286,492 bytes |
| SHA-256 | `761854a669c38da6ac0a908afe77763d54ccb83044ff123350890d408c154551` |
| 7-Zip test | Everything is Ok |
| Working directory | `$HOME/SBT-DF205-Case-2-notes/` |

I kept my analysis material separate from the original evidence and used extracted working files for database queries and metadata examination. The hash above belongs to the **ZIP archive**. The source **TAR** hash and its post-examination recheck must be recorded in the final handling log, and the ZIP hash must not be used in its place.

### Working Directory Structure

```
$HOME/SBT-DF205-Case-2-notes/
├── task3/Volumes/JOSH/NoTar-13-4-1/   # extracted file-system material
├── task7/
│   ├── health/healthdb_secure.sqlite  # Health records
│   └── routined/Cache.sqlite          # location-related records
└── task8/
    ├── setup_evidence/                # setup property-list material
    ├── recovered_attachments/         # recovered message attachments
    └── reports/                       # examination output and reports
```

**Screenshots for this section**

> 📸 **Figure 01: Case Directory**
> `![Figure 01 - Case Directory](screenshots/Figure_01_Case_Directory.png)`
> *Insert a screenshot of the case working directory structure here.*

> 📸 **Figure 02: Evidence Integrity**
> `![Figure 02 - Evidence Integrity](screenshots/Figure_02_Evidence_Integrity.png)`
> *Insert a screenshot of the 7-Zip test result and the SHA-256 hash here.*

---

## Device Information, Accounts and Settings

### Setup property list

I examined `private/var/mobile/Library/Preferences/com.apple.purplebuddy.plist`.

| File | SHA-256 |
|---|---|
| `com.apple.purplebuddy.plist` | `117247c482c236203929acce605ddf7389cb8b6b337f2662c87d7f7d5056515d` |
| `com.apple.purplebuddy.notbackedup.plist` | `b0246ab32fdd3716af03df269790f9cdcf6342a8771021136fcb41306dbeb086` |

| Property | Recorded value | Interpretation |
|---|---|---|
| SetupState | SetupUsingiTunes | Setup-state value stored in the plist |
| RestoreChoice | true | A setup choice is recorded, but it does not identify the backup source |
| SetupDone | true | Setup is recorded as completed |
| SetupFinishedAllSteps | true | Completion of the setup steps is recorded |
| SetupLastExit | 21 March 2020, 21:49:08.216927 | Recorded setup exit date |
| SetupVersion | 11 | Stored setup version |
| Language | en-US | Stored language setting |
| Locale | en_US | Stored locale |
| GuessedCountry | US | A stored guess, not proof of physical location or nationality |
| Build version field | 17D50 | Build value in the examined plist |
| Hardware model field | N69AP | Hardware-model identifier |

The property list records a completed setup process and a setup exit date of 21 March 2020. I did **not** treat `RestoreChoice = true` as proof that a particular backup was restored. A product-version value of `13.3.1` appeared under `chronicle.features`, but I did not use that single value to establish the installed iOS version at acquisition. The iOS version and build should be corroborated with a system configuration or log artefact.

### Account associations and settings

> ⚠️ **Outstanding:** `Accounts3.sqlite` and the relevant property lists still need to be queried so that at least one account association can be corroborated with an independent source. Two supported settings (for example location services or backup-related information) also still need verifying. No account has been attributed to the device in this report.

**Screenshots for this section**

> 📸 **Figure 03: Setup Property List**
> `![Figure 03 - Setup Property List](screenshots/Figure_03_Setup_Property_List.png)`
> *Insert a screenshot of the parsed `com.apple.purplebuddy.plist` output here.*

---

## Messages, Contacts, Calls and Attachments

### Messages database

I examined `private/var/mobile/Library/SMS/sms.db`. I interpreted `message.date` as nanoseconds relative to Apple's epoch (1 January 2001) and converted values to UTC with:

```sql
datetime(m.date / 1000000000 + 978307200, 'unixepoch')
```

| Message ID | UTC time | Direction | Recorded content or event |
|---|---|---|---|
| 34 | 12 Apr 2020, 12:54:21 | Outgoing | "Test iMessage. I hope this is a blue bubble!" |
| 35 | 12 Apr 2020, 12:55:23 | Incoming | Reply referring to generating a green bubble |
| 36 | 12 Apr 2020, 12:56:45 | Outgoing | Message associated with `IMG_0008.JPG` |
| 37 | 12 Apr 2020, 12:58:01 | Incoming | Message associated with `FullSizeRender.jpeg` |
| 38 | 12 Apr 2020, 13:16:19 | Outgoing | "Here is Heather's paper from DFIR Review." |
| 39 | 12 Apr 2020, 13:21:15 | Incoming | "Here is the sysdiagnose paper. Enjoy!" |
| 40 | 12 Apr 2020, 16:14:26 | Incoming | Message from a phone-number handle referring to a green message |
| 41 | 12 Apr 2020, 16:15:00 | Outgoing | Reply confirming the green message |
| 42 | 12 Apr 2020, 16:16:15 | Incoming | Further message from the same phone-number handle |
| 43 | 15 Apr 2020, 16:20:41 | Outgoing | "This is a test message for Siri and CarPlay" |
| 44 | 15 Apr 2020, 17:19:51 | Incoming | Facebook friend-suggestion notification from handle 32665 |
| 45 | 16 Apr 2020, 11:45:50 | Outgoing | Message containing an iCloud Drive link |
| 46 | 16 Apr 2020, 12:17:58 | Outgoing | "This is a test message for Siri dictation" |
| 47 | 16 Apr 2020, 12:56:49 | Incoming | Reply referring to Siri dictation |
| 48 | 16 Apr 2020, 12:59:29 | Incoming | Second reply referring to Siri dictation |

I treated stored message text as evidence of what the database records, not as proof of the real-world identity of the sender or recipient.

### Recovered attachments

Source: `$HOME/SBT-DF205-Case-2-notes/task8/recovered_attachments/`

| Message ID | Message content | Recovered file |
|---|---|---|
| 38 | Refers to Heather's paper from DFIR Review | `How was an iPhone set up?.pdf` |
| 39 | Refers to a sysdiagnose paper | `iOS_Bug_Reporting_for_Forensic_Purposes_1.2.pdf` |

| Filename | Size | SHA-256 |
|---|---|---|
| `How was an iPhone set up?.pdf` (8 pages) | 846,528 bytes | `82118655004fa94dc9257988cab0e02b7efda5fada929cbd92d04def11a136b6` |
| `iOS_Bug_Reporting_for_Forensic_Purposes_1.2.pdf` (74 pages) | 7,073,725 bytes | `255ac0cac61a3f701852bdd76a31f39e95b3760f04d53eb9e10a45cc2464cb3e` |

| Image | Dimensions | Size | SHA-256 |
|---|---|---|---|
| `IMG_0008.JPG` | 1125 × 1156 | 676,542 bytes | `df817f1c02f2c57c749a6a2d86e6beaafeb2186703745650c6bd784075757edf` |
| `FullSizeRender.jpeg` | 2212 × 1306 | 368,536 bytes | `1eb7fcc25d433fe5bf2783fec140c0088b2fae117bc15473ce7c6ca1e1827b4d` |

File modification times were not treated as original capture times, since they can reflect copying or extraction.

### Contacts and call history

> ⚠️ **Outstanding:** `AddressBook.sqlitedb` and `CallHistory.storedata` still need to be queried so that at least two message or call identifiers can be matched to stored contacts and at least three call records inspected. I have not assigned any contact name to a phone number.

**Screenshots for this section**

> 📸 **Figure 04: Messages Database**
> `![Figure 04 - Messages Database](screenshots/Figure_04_Messages_Database.png)`
> *Insert a screenshot of the `sms.db` query output with raw and converted timestamps here.*

> 📸 **Figure 05: Attachment Examination**
> `![Figure 05 - Attachment Examination](screenshots/Figure_05_Attachment_Examination.png)`
> *Insert a screenshot of the recovered attachments and their hashes here.*

> 📸 **Figure 06: Contacts and Calls**
> `![Figure 06 - Contacts and Calls](screenshots/Figure_06_Contacts_and_Calls.png)`
> *Insert a screenshot of the AddressBook and CallHistory query results here, once completed.*

---

## Safari and Calendar Examination

> ⚠️ **Outstanding:** I do not yet have a verified list of Safari visits or a completed Calendar examination, so no URLs, appointments or browsing events are presented as findings.

Planned steps to complete this section:

- Locate Safari's `History.db` and review its schema.
- Extract at least five actual visit records (URL, title, raw timestamp, timestamp field, epoch and unit).
- Convert timestamps to UTC only after confirming the field's time format.
- Distinguish browsing history from bookmarks, open tabs and synced tabs.
- Inspect `Calendar.sqlitedb` and `Extras.db` and document the available tables and event records.

**Screenshots for this section**

> 📸 **Figure 07: Safari and Calendar**
> `![Figure 07 - Safari and Calendar](screenshots/Figure_07_Safari_Calendar.png)`
> *Insert a screenshot of the Safari history and Calendar query results here, once completed.*

---

## Photographs and Media

I verified the hashes and dimensions of two image files recovered as Messages attachments (see above). These do not by themselves satisfy the separate `Photos.sqlite` requirement.

> ⚠️ **Outstanding:** Locate `Photos.sqlite`, examine at least three media records in `ZGENERICASSET` and related tables, hash the corresponding files, examine capture and added timestamps separately, record time-zone offsets and GPS fields only where available, and compare at least one database record with its file metadata.

I will not treat a sharing or deletion indicator as proof of a complete transfer or deliberate deletion without additional evidence.

**Screenshots for this section**

> 📸 **Figure 08 (Part 1): Photos Metadata**
> `![Figure 08 Part 1 - Photos Metadata](screenshots/Figure_08_Photos_Metadata_1.png)`
> *Insert the first Photos.sqlite metadata screenshot here, once completed.*

> 📸 **Figure 08 (Part 2): Photos Metadata**
> `![Figure 08 Part 2 - Photos Metadata](screenshots/Figure_08_Photos_Metadata_2.png)`
> *Insert the second Photos.sqlite metadata screenshot here, once completed.*

---

## KnowledgeC Activity

Target: `private/var/mobile/Library/CoreDuet/Knowledge/knowledgeC.db`

> ⚠️ **Outstanding:** A verified five-record extract from `ZOBJECT` (stream name, record ID, application or value, raw timestamps, converted UTC times and duration) is still needed. I have not claimed that any specific app was active or that the device was locked or unlocked at a particular time.

Once extracted, at least one activity interval should be compared with a message, browsing or media event. An overlap supports comparing records, but it does not prove the same person performed both activities.

**Screenshots for this section**

> 📸 **Figure 09: KnowledgeC**
> `![Figure 09 - KnowledgeC](screenshots/Figure_09_KnowledgeC.png)`
> *Insert a screenshot of the KnowledgeC `ZOBJECT` query output here, once completed.*

---

## Health and Location Evidence

### Health database

Source: `$HOME/SBT-DF205-Case-2-notes/task7/health/healthdb_secure.sqlite` (tables `workout_events` and `workouts`)

| Workout ID | First event (UTC) | Last event (UTC) | Duration (s) | Distance value | Energy value |
|---|---|---|---|---|---|
| 15017 | 27 Mar 2020, 18:57:39 | 27 Mar 2020, 19:15:26 | 1,080.12 | 3.24798 | 208.57 |
| 18146 | 28 Mar 2020, 14:41:25 | 28 Mar 2020, 14:58:38 | 1,039.28 | 3.23536 | 203.11 |
| 24649 | 30 Mar 2020, 12:44:04 | 30 Mar 2020, 13:05:59 | 1,591.07 | 4.84550 | 302.58 |
| 30219 | 31 Mar 2020, 18:58:47 | 31 Mar 2020, 19:24:48 | 1,572.25 | 4.86485 | 302.86 |
| 36805 | 2 Apr 2020, 17:36:41 | 2 Apr 2020, 18:02:24 | 1,550.33 | 4.85304 | 301.89 |
| 44801 | 3 Apr 2020, 19:11:28 | 3 Apr 2020, 19:37:41 | 1,580.99 | 4.86275 | 369.99 |

Timestamps were interpreted as seconds relative to Apple's epoch and converted to UTC. I kept the numeric activity-type value (37) rather than naming it, because I have not verified the mapping, and the units of the distance and energy fields still need confirming. These records show six workout entries but do not establish who performed the activity; the data could have been synchronised from an Apple Watch or another source.

**Screenshots for this section**

> 📸 **Figure 10: Health Workouts**
> `![Figure 10 - Health Workouts](screenshots/Figure_10_Health_Workouts.png)`
> *Insert a screenshot of the workout query results here.*

### Location database

Source: `$HOME/SBT-DF205-Case-2-notes/task7/routined/Cache.sqlite` (table `ZRTVISITMO`)

| Record ID | Entry (UTC) | Exit (UTC) | Approx. coordinates | Uncertainty | Data points |
|---|---|---|---|---|---|
| 68 | 11 Apr 2020, 19:09:45 | 12 Apr 2020, 13:55:11 | 35.659603, -78.872855 | 32.78 m | 1,220 |
| 70 | 12 Apr 2020, 13:55:12 | 12 Apr 2020, 13:56:00 | 35.659450, -78.876374 | 128.57 m | 49 |
| 82 | 12 Apr 2020, 14:27:08 | 13 Apr 2020, 19:20:24 | 35.659297, -78.873080 | 24.08 m | 5,492 |
| 83 | 12 Apr 2020, 14:27:08 | 14 Apr 2020, 12:38:07 | 35.659317, -78.873105 | 25.69 m | 7,702 |
| 84 | 12 Apr 2020, 14:27:08 | 15 Apr 2020, 16:02:36 | 35.659341, -78.873066 | 28.56 m | 14,977 |

Record 85 duplicates the entry and exit times, coordinates and data-point count of record 84 (only the confidence value differs), so I did not count it as an independent visit. I did not interpret the long intervals as proof that the device stayed at one precise location continuously.

Messages 40 and 41 (16:14:26 and 16:15:00 UTC on 12 April) fall within the long interval of records 82 to 85. This is a **temporal overlap, not proof** that a particular person was at those coordinates when the messages were sent.

**Screenshots for this section**

> 📸 **Figure 11: Location Records**
> `![Figure 11 - Location Records](screenshots/Figure_11_Location_Records.png)`
> *Insert a screenshot of the `ZRTVISITMO` query results here.*

---

## Timeline of Events

I built a timeline of 18 events across four artefact families (setup, Health, Messages and location). Raw timestamps and source fields are retained so the conversions can be checked.

| # | Raw time and field | Epoch and unit | UTC | Description | Evidence ID |
|---|---|---|---|---|---|
| 1 | `SetupLastExit` = 2020-03-21 21:49:08.216927 | Plist date | 21 Mar 2020, 21:49:08 | Recorded setup exit date | SET-001 |
| 2 | 607028259.36280501 (first workout event, ID 15017) | Apple epoch, s | 27 Mar 2020, 18:57:39 | First event, workout 15017 | HLT-15017 |
| 3 | 607099285.47888005 (first workout event, ID 18146) | Apple epoch, s | 28 Mar 2020, 14:41:25 | First event, workout 18146 | HLT-18146 |
| 4 | 607265044.34228301 (first workout event, ID 24649) | Apple epoch, s | 30 Mar 2020, 12:44:04 | First event, workout 24649 | HLT-24649 |
| 5 | 607373927.61740398 (first workout event, ID 30219) | Apple epoch, s | 31 Mar 2020, 18:58:47 | First event, workout 30219 | HLT-30219 |
| 6 | 607541801.45553601 (first workout event, ID 36805) | Apple epoch, s | 2 Apr 2020, 17:36:41 | First event, workout 36805 | HLT-36805 |
| 7 | 607633888.75596297 (first workout event, ID 44801) | Apple epoch, s | 3 Apr 2020, 19:11:28 | First event, workout 44801 | HLT-44801 |
| 8 | 608388861591000064 (`message.date`, ID 34) | Apple epoch, ns | 12 Apr 2020, 12:54:21 | Outgoing iMessage test | MSG-034 |
| 9 | 608389005092000000 (`message.date`, ID 36) | Apple epoch, ns | 12 Apr 2020, 12:56:45 | Outgoing message with an image attachment | MSG-036 |
| 10 | 608390179362000000 (`message.date`, ID 38) | Apple epoch, ns | 12 Apr 2020, 13:16:19 | Outgoing message referring to a PDF | MSG-038 |
| 11 | 608390475684724608 (`message.date`, ID 39) | Apple epoch, ns | 12 Apr 2020, 13:21:15 | Incoming message referring to a sysdiagnose paper | MSG-039 |
| 12 | 608392512.9988631 (`ZENTRYDATE`, location 70) | Apple epoch, s | 12 Apr 2020, 13:55:12 | Entry time, short visit-related record | LOC-070 |
| 13 | 608400866607614080 (`message.date`, ID 40) | Apple epoch, ns | 12 Apr 2020, 16:14:26 | Incoming message from a phone-number handle | MSG-040 |
| 14 | 608400900444000000 (`message.date`, ID 41) | Apple epoch, ns | 12 Apr 2020, 16:15:00 | Outgoing reply | MSG-041 |
| 15 | 608659356.82855296 (`ZEXITDATE`, location 84) | Apple epoch, s | 15 Apr 2020, 16:02:36 | Exit time, long location interval | LOC-084 |
| 16 | 608660441673863040 (`message.date`, ID 43) | Apple epoch, ns | 15 Apr 2020, 16:20:41 | Outgoing message about Siri and CarPlay testing | MSG-043 |
| 17 | 608730350348999936 (`message.date`, ID 45) | Apple epoch, ns | 16 Apr 2020, 11:45:50 | Outgoing message with an iCloud Drive link | MSG-045 |
| 18 | 608732278492000128 (`message.date`, ID 46) | Apple epoch, ns | 16 Apr 2020, 12:17:58 | Outgoing message about Siri dictation testing | MSG-046 |

The complete timeline will be preserved in `Timeline.csv` with the exact raw value, source field, epoch and unit, converted UTC time, description, source path and evidence ID.

---

## Key Correlations and Alternative Explanations

**Correlations identified**

1. Messages 38 and 39 are consistent with the two recovered PDF attachments.
2. Messages 40 and 41 fall within the long location interval of records 82 to 85.
3. The recorded setup date (21 March 2020) precedes the later Health and Messages activity.

**Alternative explanations considered**

- Health workout data may have been synchronised from another device (for example an Apple Watch) rather than generated by the phone's user.
- Location records may represent learned visits rather than continuous tracking.
- A message timestamp inside a location interval does not show the sender was physically at that location.

---

## Limitations and Outstanding Work

Completed in this report: evidence integrity check, setup property list, Messages and attachments, Health database, location database and the 18-event timeline.

Still requiring verified results before they can be reported as findings:

- [ ] Source TAR hash and post-examination recheck in the handling log
- [ ] Account associations (`Accounts3.sqlite`) with independent corroboration
- [ ] Two supported device settings
- [ ] Contacts (`AddressBook.sqlitedb`) and call history (`CallHistory.storedata`)
- [ ] Safari `History.db` (at least five visits) and Calendar databases
- [ ] `Photos.sqlite` (at least three media records)
- [ ] KnowledgeC `ZOBJECT` (at least five records)
- [ ] Verification of Health activity-type code 37 and the distance and energy units
- [ ] Corroboration of the iOS version and build at acquisition
- [ ] Full query output for at least five messages (raw timestamps, UTC, handle, direction, service type, text)

---

---

## Screenshot Index

Save each screenshot into the `screenshots/` folder using the exact filename below so the images render on GitHub.

| Figure | Section | Filename | What to capture |
|---|---|---|---|
| 01 | Evidence Handling | `Figure_01_Case_Directory.png` | Case working directory structure |
| 02 | Evidence Handling | `Figure_02_Evidence_Integrity.png` | 7-Zip test result and SHA-256 hash |
| 03 | Device Information | `Figure_03_Setup_Property_List.png` | Parsed `com.apple.purplebuddy.plist` |
| 04 | Messages | `Figure_04_Messages_Database.png` | `sms.db` query with raw and UTC timestamps |
| 05 | Messages | `Figure_05_Attachment_Examination.png` | Recovered attachments and hashes |
| 06 | Messages | `Figure_06_Contacts_and_Calls.png` | AddressBook and CallHistory results |
| 07 | Safari and Calendar | `Figure_07_Safari_Calendar.png` | Safari history and Calendar results |
| 08 (1) | Photographs | `Figure_08_Photos_Metadata_1.png` | `Photos.sqlite` metadata, part 1 |
| 08 (2) | Photographs | `Figure_08_Photos_Metadata_2.png` | `Photos.sqlite` metadata, part 2 |
| 09 | KnowledgeC | `Figure_09_KnowledgeC.png` | `ZOBJECT` query output |
| 10 | Health | `Figure_10_Health_Workouts.png` | Workout query results |
| 11 | Location | `Figure_11_Location_Records.png` | `ZRTVISITMO` query results |

---

## Key Learning Outcomes

- Preserving evidence and recording integrity with hashes.
- Examining iOS databases and property lists with SQLite and Python.
- Converting Apple epoch timestamps (seconds and nanoseconds) to UTC.
- Tracing message attachments back to recovered files.
- Comparing records from different artefact families to build a timeline.
- Separating what an artefact directly records from conclusions that need corroboration.

---

## Disclaimer

This repository is for **educational purposes** as part of the ICDFA SBT-DF205 module. The evidence comes from a publicly available training dataset (Joshua Hickman's iOS 13.4.1 iPhone SE image on Digital Corpora). No real-world individuals are being investigated, and findings are limited to the records examined.

---

## Author

**Adekunle Ogunyemi**  
Student, SBT-DF205 Mobile and IoT Forensics, ICDFA