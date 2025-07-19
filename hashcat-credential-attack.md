# Lab Title: Using Hashcat to Crack Credentials

**Keywords:** *Credential-based attacks, password policy, security controls, Hashcat*

## Cybersecurity Relevance

Weak passwords and inadequate hash protections are common security vulnerabilities.  
This lab demonstrates how easily exposed hashes can be exploited if technical controls — such as password complexity, proper salting, and secure hash key storage — are lacking.

## Objectives

1. **Simulate credential-based attacks by using Hashcat** to crack MD5 hashes  
2. Reinforce the value of technical *(type)* / preventive *(category)* controls such as:  
   - Complex password requirements  
   - Account lockout policies  

## Threat Attack Scope  
***Compromised Credential Abuse Scenario***

## Important Terms

**a) Hash** — One-way, irreversible cryptographic function consisting of character strings, more secure than plain text.  
Cybersecurity goal met from the CIA Triad: **Data Integrity**

**b) MD5 Hash** *(Message Digest 5 — by Ron Rivest, 1991)*  
- Produces a 128-bit value represented as a 32-character hexadecimal string  
- Each hash string is the result of running a password through the MD5 hashing algorithm


##Lab Environment/tools

1. **Kali-Linux 2025.1** (Installed on Virtual Box VM)

2. **Linux command-line module used**: Hashcat (ready-to-use/ pre-installed on Kali-Linux)
	- **Hashcat** is a Linux-based open source password cracking tool 

	- *Use for both good and nefarious exploits by*:  	
		-**White Hat Hackers** (Authorized Pent Testers hired to explore vulnerabilities, 
		   for the good, to improve security)

		-  **Black Hat Hackers** ( Unauthorized Bad actors with malicious intent)

3. **Dictionary / Wordlist (`rockyou.txt`)**  
   - Plaintext (unhashed) file with over 14 million+ real password guesses  
   - Based on a 2009 data breach from the RockYou website  
   - Commonly used for dictionary attacks in lab simulations and real-world scenarios

4. **Hash Function Used:**  
   - **MD5 Hash Algorithm**  
   - **IMPORTANT NOTE:**  
     - For simplicity, this lab uses the MD5 (Message Digest) hash created by **Ron Rivest** in 1991  
     - However, this hash function is now deprecated due to *collision vulnerabilities*  
     - Hash collisions occur when the algorithm produces the same hash output for different inputs  
     - Collisions jeopardize the ability to verify data integrity

HERE
 5. **Security Best Practice: Use SHA (Secure Hash Algorithm)**  
   - Replace deprecated algorithms with government-standard SHA-based hashing  
   - **NIST** (National Institute of Standards and Technology) published SHA-1 in 1995  
   - There are three versions: **SHA-1**, **SHA-2**, and **SHA-3**  
   - **SHA-3** is the most secure and current standard

6. **SHA HASH Challenge (Extended Lab):**  
   - If you've completed the MD5 version — congrats!  
   - Now try re-running the lab using a SHA-based hash instead  
   - **Hint:**  
     - Hashcat uses different hash modes for each algorithm (e.g., SHA-1, SHA-2, SHA-3)  
     - To find the correct mode number, refer to this:  
       [Hashcat Cheat Sheet PDF](https://www.blackhillsinfosec.com/wp-content/uploads/2020/09/HashcatCheatSheet.v2018.1b.pdf)  
   - Modify your dictionary attack command to use the appropriate *hash mode type*  
   - For example, using a SHA-2 variant (there are four), your command might look like:
     ```bash
     -m 1440
     ```
   - The above mode (`-m 1440`) corresponds to **SHA-256**  
   - **BONUS:** Want to level up? Try the BONUS LAB: *"Custom Hash"* to create your own hashed password list

---

### Workflow Intro Overview:

**Step 1:** Obtain a set of sample hashes  
**Step 2:** Create a hash file directly in the terminal using a "here document"  
**Step 3:** Run `hashcat` to view the help menu and available module options  
**Step 4:** Locate the `rockyou.txt` file for the dictionary attack  
**Step 5:** Unzip the file using the `gunzip` command  
**Step 6:** Navigate back to your home directory  
**Step 7:** Launch the dictionary-based password cracking attack  
**Step 8:** View the `BlackHatCracked.txt` file using `cat` to display its contents  
**Step 9:** Final Attack Observations  
**Step 10:** Key Takeaways  
**Step 11:** Troubleshooting Insights, Findings, and Roadblocks	 




### 1. Obtain a Set of Sample Hashes

> **Context:**  
> Assume you're an attacker or penetration tester who has already obtained an MD5-hashed list of seven passwords  
> from a data breach or database compromise.

Below is the list of compromised hashes:

   ```
   f25a2fc72690b780b2a14e140ef6a9e0  
   8afa847f50a716e64932d995c8e7435a  
   67881381dbc68d4761230131ae0008f7  
   d8578edf8458ce06fbc5bb76a58c5ca4  
   c666aa5fa8bb61999c15f739f7a1601e  
   9377176efbb37c44c7efd23977f238d1  
   af037d1620094023b45c0f7f5197b73f
   ```
2. **Create a hash file directly within the terminal shell** using a "here document" with the following command string:

```bash
┌──(kali㉿kali)-[~]
cat << EOF > BreachTarget_MD5hashes.txt
f25a2fc72690b780b2a14e140ef6a9e0
8afa847f50a716e64932d995c8e7435a
67881381dbc68d4761230131ae0008f7
d8578edf8458ce06fbc5bb76a58c5ca4
c666aa5fa8bb61999c15f739f7a1601e
9377176efbb37c44c7efd23977f238d1
af037d1620094023b45c0f7f5197b73f
EOF
```

> **Note:** This command tells the shell to redirect all the input lines between `cat` and `EOF` (End of File)
into a file named `BreachTarget_MD5hashes.txt`.


       
3. **Open Hashcat to display the help menu and view available Hashcat module options**

```bash
┌──(kali㉿kali)-[~]
hashcat -h | more
hashcat (v6.2.6) starting in help mode
```

> **Note:** The `-h` option displays help, and the `more` command provides a paginated 
view of all available options, allowing you to scroll through each page. Use the **Spacebar** to scroll.

This displays the following categories of options:

- Choose an available hash mode (e.g., **MD5**, **SHA-1**, **SHA-256**)
- Attack modes (e.g., **dictionary**, **brute-force**)
- Command flags and usage
- Proper syntax examples

> **Context:** The most relevant options for this hash attack SIM are:  
> a) **MD5 Hash Mode**, referenced with option `0`  
> b) **Attack Mode**, selected with the `-a` flag and the number `0`    
	

4. **Locate the `rockyou.txt` file** that will be used for this dictionary attack.  
In the terminal, input the following:

```bash
┌──(kali㉿kali)-[~]
locate rockyou.txt
```

> **Note:** If successful, the directory path displayed should look like the following.  
> If you see the `.gz` file extension, the file is compressed and needs to be unzipped.  
> Problems? See the **Troubleshooting** section — *Problem 1*.

```bash
┌──(kali㉿kali)-[~]
locate rockyou.txt
/usr/share/wordlists/rockyou.txt
```

> **Reminder:** If your output shows a `.gz` extension, the file is compressed using Gzip.  
> You'll need to unzip it before it can be used.

```bash
┌──(kali㉿kali)-[~]
locate rockyou.txt
/usr/share/wordlists/rockyou.txt.gz
```

5. **Unzip the file using the `gunzip` command.**  
In the terminal, type:

```bash
┌──(kali㉿kali)-[~]
sudo gunzip rockyou.txt.gz
```

> **Note:** Kali Linux uses a tool called `gunzip` to extract `.gz` files.

Next, use the `ls` command to verify the file was successfully unzipped and now appears with a `.txt` file extension:

```bash
┌──(kali㉿kali)-[~]
ls
amass  dirb  dirbuster  dnsmap.txt  fasttrack.txt  fern-wifi  john.lst  legion  metasploit  nmap.lst  rockyou.txt
```

> **Note:** Make sure you're in the `wordlists` directory.  
> You should see the file listed as `rockyou.txt`.  
> Your directories listed from the `ls` command may vary, but you should see the target file.
    
6. **Return to your home directory** — the base of operations for this lab — using the following command:

```bash
┌──(kali㉿kali)-[~]
cd ~
```
7. **Launch the dictionary-based password cracking attack** using the following command in the terminal:

```bash
┌──(kali㉿kali)-[~]
hashcat -m 0 -a 0 -o BlackHatCracked.txt BreachTarget_MD5hashes.txt /usr/share/wordlists/rockyou.txt
```

> ### **Attack "Command Conversation" in a Nutshell**
>
> - Call the Hashcat tool with the `hashcat` command. The `-m` option combined with `0` tells Hashcat to use the **MD5** hash algorithm.  
> - The `-a 0` option indicates a **dictionary attack** mode.  
> - The `-o BlackHatCracked.txt` flag tells Hashcat to **output cracked passwords** to that file.  
> - `BreachTarget_MD5hashes.txt` is the file that contains the **breached target hashes**.  
> - `/usr/share/wordlists/rockyou.txt` is the dictionary **wordlist** that Hashcat will compare hashes against.  
> - Hashcat will compute hashes from each password in `rockyou.txt` and compare them to the ones in `BreachTarget_MD5hashes.txt` in this simulation.

8. **View the contents of the `BlackHatCracked.txt` file** using the `cat` command:

```bash
┌──(kali㉿kali)-[~]
cat BlackHatCracked.txt
f25a2fc72690b780b2a14e140ef6a9e0:iloveyou
8afa847f50a716e64932d995c8e7435a:princess
67881381dbc68d4761230131ae0008f7:babygirl
d8578edf8458ce06fbc5bb76a58c5ca4:qwerty
```

> **Note:** This output shows that Hashcat successfully cracked the MD5 hashes and matched them to real passwords  
> from the `rockyou.txt` wordlist.

 9. **Final Attack Observations**

- Only **4 out of 7 hashes** were matched in the wordlist, resulting in a **57.14% success rate**.  
  Hashcat output confirms:

```text
Recovered........: 4/7 (57.14%) Digests (total), 4/7 (57.14%) Digests (new)
```

- The total number of password guess attempts was over **14 million**, and the cracking speed registered at  
  **1852.0 kH/s** (about **1.85 million hashes per second**).  
  THAT’S A FAST ATTACK!

```text
Restore.Point....: 14344385/14344385 < PW guess attempts
Speed.#1.........:  1852.0 kH/s (0.03ms)
```

10.  **Attack Takeaways**

- This demonstrates the importance of **reducing the attack surface** by implementing strong technical controls:
  - Enforcing **password complexity**
  - Applying **hash salting** (adding a random string before hashing)
  - Securing **hash key storage**



--------------------------------------------------

## Troubleshooting Insights, Findings, Errors / Roadblocks

---

### 🛠️ 1) Problem Statement: *Couldn't locate the `rockyou.txt` dictionary password list*

- **Problem Scope:** The `locate` command depends on a pre-built index database, which may not be updated on fresh installs of Kali Linux.

- **Solution:** Update the file index with the following command (run as root):

```bash
┌──(kali㉿kali)-[~]
sudo updatedb
```

Then re-run:

```bash
┌──(kali㉿kali)-[~]
locate rockyou.txt
```

- **Outcome:** This resolved the issue and permitted Hashcat to proceed with the dictionary attack using the `rockyou.txt` file.

---

### 🛠️ 2) Problem Statement: *Permission denied when using `gunzip` to extract `rockyou.txt.gz`*

```bash
──(kali㉿kali)-[/usr/share/wordlists]
gunzip rockyou.txt.gz
gzip: rockyou.txt: Permission denied
```

- **Root Cause:** `/usr/share/wordlists/` is a protected directory and requires root privileges to make file changes.

- **Solution:** Use `sudo` to run `gunzip` with elevated privileges:

```bash
──(kali㉿kali)-[/usr/share/wordlists]
sudo gunzip rockyou.txt.gz
[sudo] password for kali: 
```

- Verify the file was extracted:

```bash
┌──(kali㉿kali)-[/usr/share/wordlists]
ls
rockyou.txt     <<<< Notice how the `.gz` file extension is removed
```

- **Outcome:** Using `sudo` granted permission to unzip and extract the dictionary file. The attack was able to continue successfully.

## 🎉 Kudos — You Made It Through the Attack!

> **Note:**  
> This lab was inspired by common credential attack simulations used in ethical hacking training environments.  

© 2025 **Javinne McCoy**. All rights reserved.

This lab and its contents are **original** and intended for **educational and portfolio purposes only**.  
You are welcome to view and share the public GitHub link to this lab.  
However, please do **not reproduce or republish its content** without permission.