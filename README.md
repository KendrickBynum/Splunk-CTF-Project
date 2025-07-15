# Splunk-CTF-Project

# Splunk SIEM Log Analysis Project

## 🧠 Overview
This independent project simulates a real-world Security Operations Center (SOC) investigation using **Splunk**. I created a threat hunting workflow to analyze two datasets: one based on Netflix media metadata and another simulating a malware incident in an enterprise network.

My goal was to practice using a SIEM platform to detect patterns in large volumes of logs, identify potential threats, and piece together evidence of malicious activity using Splunk's powerful search capabilities.

---

## 🔧 Tools & Environment
- Splunk Enterprise (local instance via RDP)
- Log sources: `Netflix` data and `Pathcode` malware investigation logs
- Skills applied: log parsing, threat correlation, investigative logic, field extraction

---

## 🎯 Project Goals
- Understand how media content is categorized in a streaming dataset
- Investigate a suspected malware attack using hash values and log correlation
- Use Splunk to build a timeline of attacker behavior based on IP, usernames, and user agent strings
- Reinforce SIEM skills like log enrichment and incident triage

---

## 🔍 Investigation 1: Netflix Dataset Exploration

### 🎯 Objective:
I wanted to explore how content metadata could be used to answer specific business or analytics questions using Splunk.

---

#### 🔎 Use Case 1: Find how many titles are rated TV-PG

**Query:**
```
index=main host=Netflix rating="TV-PG"
```

**Outcome:**  
This returned `1,726` results, showing how widespread this rating is in the Netflix catalog.

<img width="1912" height="1007" alt="image" src="https://github.com/user-attachments/assets/2cdcd91f-1b22-4552-9c34-d04d7ccb73e4" />

---

#### 🔎 Use Case 2: What is the longest series by number of seasons?

**Query:**
```
index=main host=Netflix type="TV Show"
| eval seasons = tonumber(replace(duration, "[^0-9]", ""))
| stats max(seasons) by title
```

**Outcome:**  
I discovered that certain docuseries spanned multiple seasons, useful for identifying binge-worthy content patterns.

<img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/56269f26-aa82-4aa1-9577-cb910e24fdf3" />

---

#### 🔎 Use Case 3: Which countries produce the most G-rated movies?

**Query:**
```
index=main host=Netflix rating="G" type="Movie"
| stats count by country
| sort -count
```

**Outcome:**  
The U.S. was the top source, but several international countries also produced G-rated content.

<img width="1916" height="1025" alt="image" src="https://github.com/user-attachments/assets/86149437-ce32-4ea7-898a-9b6f1adda22d" />

---

## 🔍 Investigation 2: Simulated Malware Breach (Pathcode Logs)

### 🎯 Objective:
I wanted to trace a simulated malware upload and identify how it happened, who did it, and what else occurred around it.

---

#### 🧪 Step 1: Start with the known malware hash

**Query:**
```
index=pathcode "3AADBF7E527FC1A050E1C97FEA1CBA4D"
| table _time IP Filename
```

**Result:**  
This revealed the file `EvilScript.exe` uploaded from IP address `192.168.1.10`.

<img width="1919" height="990" alt="image" src="https://github.com/user-attachments/assets/a069ab50-86ac-4944-b353-179eaada108e" />

---

#### 🧪 Step 2: Investigate the attacker’s behavior

**Query:**
```
index=pathcode IP="192.168.1.50"
| table _time user Filename
```

**Insight:**  
This user tried multiple login attempts before uploading the malware, suggesting a brute-force tactic.

<img width="1919" height="999" alt="image" src="https://github.com/user-attachments/assets/5ae59f34-8b2b-443e-b4a0-2f330f69f26b" />

---

#### 🧪 Step 3: Identify the User Agent

**Query:**
```
index=pathcode IP="192.168.1.10"
| table _time "User Agent"
```

**Result:**  
The attacker used a non-standard client that stood out against normal traffic — a good detection signature.

<img width="1919" height="995" alt="image" src="https://github.com/user-attachments/assets/1bd52ae4-a3b5-4f26-a516-e1c2d2524a0d" />

---

#### 🧪 Step 4: See who else uploaded files around that time

**Query:**
```
index=pathcode earliest="06/04/2023:17:55:00" latest="06/04/2023:18:05:00"
| table _time user IP Filename
```

**Outcome:**  
One other user uploaded a benign file within the same time window, confirming it wasn’t just the attacker active during that period.

<img width="1919" height="1000" alt="image" src="https://github.com/user-attachments/assets/17efdd81-4a0f-49ff-949e-9017d0892d4f" />
<img width="1914" height="986" alt="image" src="https://github.com/user-attachments/assets/94aea756-44c2-4c4e-b459-85db6fa0e1f6" />

---

#### 🧪 Step 5: Compare uploaded hashes

**Query:**
```
index=pathcode earliest="06/04/2023:17:55:00" latest="06/04/2023:18:05:00"
| table _time user IP Filename "File Hash"
```

**Result:**  
The hash of `EvilScript.exe` matched the known malicious hash. The second file’s hash did not, confirming it was unrelated.

<img width="1919" height="925" alt="image" src="https://github.com/user-attachments/assets/6484a0cf-544e-42c7-a7fe-b26bd8ef5692" />
<img width="1919" height="936" alt="image" src="https://github.com/user-attachments/assets/48b6b793-908b-4865-887d-18d5e0f5d79e" />


---

## 📘 Key Takeaways

- 🔍 SIEMs are essential for detecting and reconstructing attacks from logs
- ⏱️ Time correlation across events is critical in multi-stage attacks
- 🧠 Raw logs hold more value when parsed into consistent fields like `IP`, `user`, and `action`
- 📊 Even non-security datasets (like Netflix) can be useful for practicing search, stats, and filtering in Splunk

---

## 💼 Use Case

This project demonstrates my ability to:
- Perform threat hunting using SIEM tools like Splunk
- Extract insights from structured and unstructured logs
- Write effective SPL (Splunk Processing Language) queries
- Document and communicate findings clearly

---

**Want to see more?**  
I'm happy to walk through this project or others I’ve worked on in a mock interview or technical conversation.
