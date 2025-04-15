# Memory Forensics Analysis of a Compromised Windows Machine Using Volatility

## Objective

This project is aimed at investigating and analyzing a memory dump from a compromised virtual machine in order to detect signs of malware infection, identify suspicious processes and network activity. The focus is to gain insights into the attacker’s behavior using the Volatility framework.

### Skills Learned
- Conducted memory forensics using Volatility on a compromised Windows VM.
- Identified memory-resident malware and analyzed process behavior.
- Detected unauthorized network connections indicating lateral movement.
- Investigated DLLs and command-line arguments to understand attacker actions.\
- Gained practical experience in post-incident memory analysis.

### Tools Used

- Volatility 3: Memory forensics framework used to analyze RAM dumps and extract information about processes, network connections, loaded DLLs, etc.
- Kali Linux: The operating system used to host and run the Volatility framework and conduct forensic analysis.

### Environment
A snapshot of the compromised Windows VM was captured using VMware. Volatility 3 was installed and executed within a Kali Linux to facilitate the analysis.

### Steps
1. Gained root access via `sudo su -`, then navigated to the Volatility directory to begin analysis.
   <br>
   
   ![image](https://github.com/user-attachments/assets/b3de41df-95f6-4970-b556-df5165a8c36f)

2. Ran the `malfind` plugin to identify memory regions with suspicious permissions (read, write, execute). 
   <br>
 
   ![image](https://github.com/user-attachments/assets/5e9b9209-0d86-472f-95ca-fbc615080785)
   This revealed a suspicious process named TrustMe.exe.

3. Identified Network connections: Using netscan, an active SMB (port 445) connection was uncovered between the compromised system and another internal host. This connection
   is highly suspicious and  warrants deeper investigation, because it indicates lateral movement.
    <br>
    
    ![image](https://github.com/user-attachments/assets/5be027d6-a087-4f7f-ba11-d8e83533673c)

4. Identified running processes with `pslist`
   <br>
   
   ![image](https://github.com/user-attachments/assets/ced9e69a-df49-4c0f-a39e-928e8fcb6e90)

5.  Here, I visualized parent-child relationships using `Pstree.` This revealed that cmd.exe was launched by TrustMe.exe, which was itself initiated via explorer.exe 
    strongly suggesting the execution was triggered by a user on the system, possibly through phishing or other social engineering tactics.
    <br>
 
    ![image](https://github.com/user-attachments/assets/bbf00e0c-57fd-4166-b7cd-a6fcaadd35c4)

6.  Using the PID of the TrustMe.exe, which is 5452. I used  `dlllist` and reviewed its loaded modules
    <br>
   
    ![image](https://github.com/user-attachments/assets/6fc6238a-42bc-4e44-859e-3c48c4d6080e)
    This confirmed that TrustMe.exe had access to various system libraries, potentially to maintain persistence or evade detection.

# Conclusion
This memory analysis project successfully demonstrated the use of Volatility to investigate a compromised Windows machine. I identified a suspicious executable (TrustMe.exe), traced its origin to user interaction via explorer.exe, and observed its attempt to execute system commands through cmd.exe.
Further investigation revealed an open SMB network connection, suggesting possible lateral movement within the environment. 
This project highlights how even a single malicious process can reveal a larger attack chain when memory is properly analyzed. It emphasizes the value of tools like Volatility in providing deep visibility into the state of a system post-compromise.

# Recommendations
- User Awareness Training: Since the malware was likely executed manually via explorer.exe, user education on phishing and suspicious files is critical to reducing the risk of similar attacks.
- Implement Endpoint Detection & Response (EDR): Deploy EDR tools that provide real-time monitoring of process behavior and memory activity, helping detect threats like TrustMe.exe as they execute.
- Automate Memory Analysis in Incident Response: Integrate memory forensics tools into the organization’s IR playbook. Use automation scripts to run baseline Volatility modules on suspected systems.


