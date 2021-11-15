---
layout: page
title: "ToDo After Ransomware Attack"
permalink: /todo_after_ransomware_attack/
---

# WebApp Testing Checklist

### Technical Team(s)

- [ ]  Isolate your backup files and make sure the ransomware cannot touch it.
- [ ]  Estimate the scale of the attack and the source node(s).
- [ ]  Isolate the source node and all affected nodes. If the above step takes too long just pull the network cable from all affected machines (do not turn them off).
- [ ]  Start inspection on the non-affected nodes (to be sure).
- [ ]  Gather forensic data from the source node(s) if there might be a need for later in the process.
- [ ]  ID the malware and check if there is a public decryptor available: [Check ID Ransomware](https://id-ransomware.malwarehunterteam.com).
- [ ]  If you can recover your files, start the work and make sure the ransomware is not available on the nodes you restore the data too (otherwise it will be encrypted). If you cannot recover the files backup the encrypted files and hope for a solution in the future.
- [ ]  Make a choice - Reinstall the machines completely or try to remove the ransomware (tools like Hitman Pro / Malwarebytes can usually assist you). After the choice is made, start the work.
- [ ]  After machines are recovered start recovering your data (from backup or from decryption).
- [ ]  WORST-CASE-SCENARIO: you have no backup and the encryption is not reversable. In this case you need to make a decision about the impact on the company. Can you start over without damaging the company too much... or can you simple not function anymore without the data? If the last answer is "yes" you need to think about paying the ransom. My advice is NEVER PAY RANSOM (and support these criminal activities) but in some cases there is simply no choice. Get in contact with the criminals and agree to a lower price (usually there is room for negotiation). Also ask for a sample of decryption before you pay.
