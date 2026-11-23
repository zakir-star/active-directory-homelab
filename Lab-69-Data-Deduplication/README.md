# Lab 69: Data Deduplication

## 🎯 Objective

Install and configure the Data Deduplication role service on Windows Server 2022, optimize a data volume, and verify how duplicate data is reduced through deduplication. The lab uses two folders containing copies of the Windows Server DVD contents to demonstrate the storage savings.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 💿 **Operating System:** Windows Server 2022
- 🛠️ **Tools:** Server Manager and Windows PowerShell
- 💽 **Data Volume:** `E:\` (20 GB)
- 📀 **Source:** Windows Server DVD drive `D:\`
- 📁 **Test Folders:** `E:\copy-1` and `E:\copy-2`
- ⚙️ **Data Deduplication Type:** General purpose file server
- ⏱️ **Deduplication Age Setting:** 0 days

## 📚 Theory — Windows Data Deduplication

Windows Data Deduplication is a Windows Server storage feature that reduces the amount of physical disk space consumed by files containing duplicate data. It is useful when a volume stores many files that share identical content, such as file-server data, software distribution content, or backup data.

### How Data Deduplication works

Data Deduplication analyzes files and identifies repeated data patterns. It divides file content into chunks, identifies chunks that are already stored, and stores shared chunk data only once in the deduplication store. The file system then uses references to the stored chunks to represent the optimized files.

This means that multiple files can continue to appear as separate files to users and applications even when some of their underlying content is stored only once.

### Important terms

- **Optimization:** The process that scans eligible files, identifies duplicate chunks, and replaces redundant file content with references to the deduplicated data.
- **Deduplication store:** The storage area that contains the optimized chunk data and related metadata.
- **Deduplication job:** A background task that performs operations such as optimization, garbage collection, or scrubbing.
- **Deduplication rate:** The percentage of the volume's data reported as reduced through deduplication.
- **Deduplication savings:** The amount of storage space reported as saved by the deduplication process.
- **Minimum file age:** The age threshold used to determine when files become eligible for optimization. In this lab, it was changed to **0 days** so the newly copied test data could be processed without waiting.

### Deduplication types

Windows Server provides different usage profiles for Data Deduplication, including:

- **General purpose file server:** Designed for common file-server workloads.
- **Virtualization ( ಸು ):** Designed for supported virtual machine storage workloads.
- **Backup:** Designed for supported backup application workloads.

The exact available options depend on the Windows Server version and the installed Data Deduplication components. This lab uses **General purpose file server**.

### Optimization is not compression or backup

Data Deduplication reduces repeated data; it does not replace a backup strategy. It also does not guarantee that every file will save space. Files with little or no repeated content may provide limited savings, and deduplication processing uses system resources while jobs run.

## ⚙️ Lab Configuration

### Step 1 — Install the Data Deduplication role service

1. Sign in to the Windows Server 2022 machine.
2. Open **Server Manager**.
3. Use **Add Roles and Features** to install the **Data Deduplication** role service under **File and Storage Services**.
4. Complete the installation.

### Step 2 — Prepare duplicate test data on the E: drive

1. Open the `E:\` drive in File Explorer. The drive has a capacity of **20 GB**.
2. Create two folders:
   - `copy-1`
   - `copy-2`
3. Open the Windows Server DVD drive `D:\`.
4. Copy all files and folders from the DVD drive.
5. Paste the copied contents into `E:\copy-1`.
6. Paste the same contents into `E:\copy-2`.
7. Check the available free space on the `E:\` drive before running deduplication. The initial free space observed in this lab was **10.5 GB**.

### Step 3 — Configure Data Deduplication on the E: volume

1. Open **Server Manager**.
2. Navigate to **File and Storage Services → Volumes**.
3. Right-click the `E:\` volume and select **Configure Data Deduplication**.
4. Select **General purpose file server** as the Data Deduplication type.
5. Change **Deduplicate files older than** from **3** days to **0** days.
6. Click **OK** to apply the configuration.

### Step 4 — Start the optimization job

1. Open **Windows PowerShell** as Administrator.
2. Run the following command to start an optimization job on the `E:\` volume:

```powershell
Start-DedupJob -Volume E: -Type Optimization
```

3. Allow the optimization job to process the volume.

### Step 5 — Monitor the optimization job

1. In the elevated PowerShell window, run:

```powershell
Get-DedupJob
```

2. Review the job status and progress.
3. In this lab, the progress was monitored at **0%**, **36%**, and **65%**.
4. Continue monitoring until the optimization job reaches **100%** and completes.

### Step 6 — Verify deduplication savings

1. Open **Server Manager**.
2. Navigate to **File and Storage Services → Volumes**.
3. Review the deduplication information for the `E:\` volume.
4. Verify the reported free space, deduplication rate, and deduplication savings.
5. In this lab, the completed optimization reported:
   - **Free Space:** 15.5 GB
   - **Deduplication Rate:** 54%
   - **Deduplication Savings:** 5.26 GB

### Step 7 — Verify the volume in File Explorer

1. Open **File Explorer → This PC**.
2. Locate the `E:\` drive.
3. Confirm that the displayed free space is **15.5 GB** after optimization.

## 🔎 Verification

- Confirm that the **Data Deduplication** role service is installed.
- Confirm that Data Deduplication is configured on the `E:\` volume.
- Confirm that **General purpose file server** is selected.
- Confirm that **Deduplicate files older than** is set to **0 days**.
- Confirm that the optimization job completed using `Get-DedupJob`.
- Verify the reported **54%** deduplication rate and **5.26 GB** savings in Server Manager.
- Confirm that File Explorer shows **15.5 GB** free on the `E:\` drive.

## ⚠️ Important Notes

- The test volume used in this lab is `E:\`, with a capacity of **20 GB**.
- The two test folders contain copies of the same Windows Server DVD contents to provide repeated data for the optimization job.
- Setting the minimum file age to **0 days** makes the newly copied test files eligible for optimization without the default three-day waiting period.
- The optimization job runs in the background. Its progress can be checked with `Get-DedupJob`.
- Deduplication savings depend on how much duplicate content exists on the volume. Different data sets can produce different results.
- The free-space values displayed by Windows may be rounded. The reported **5.26 GB** deduplication savings and the displayed free-space change should be treated as separate reported measurements.
- Data Deduplication is not a substitute for independent backups. Maintain appropriate backups before making changes to important data.

## 🎯 Expected Result

- The Data Deduplication role service is installed on Windows Server 2022.
- The `E:\` volume is configured for **General purpose file server** deduplication.
- The minimum file age is set to **0 days**.
- The optimization job completes successfully.
- Server Manager reports a **54%** deduplication rate and **5.26 GB** of deduplication savings.
- File Explorer shows **15.5 GB** of free space on the `E:\` drive.

## 📸 Screenshots

Add the selected lab screenshots to the `screenshots` folder. The placeholders below identify the key evidence to include; add the actual image links after selecting the screenshots.

1. **Data Deduplication role service installed**
   <!-- Screenshot placeholder 1 -->

2. **E: drive test folders containing the copied Windows Server DVD contents**
   <!-- Screenshot placeholder 2 -->

3. **Data Deduplication configuration — General purpose file server and 0-day file age**
   <!-- Screenshot placeholder 3 -->

4. **PowerShell optimization job progress and completed status**
   <!-- Screenshot placeholder 4 -->

5. **Final deduplication results — 54% rate and 5.26 GB savings**
   <!-- Screenshot placeholder 5 -->

6. **E: drive showing 15.5 GB free in This PC**
   <!-- Screenshot placeholder 6 -->

## 🌍 Real-World Use Case

Organizations that store large amounts of repeated file content can use Data Deduplication to reduce storage consumption. For example, file servers that contain similar documents, software distribution files, or supported backup data may benefit from storing common data chunks only once. This can help make better use of available storage capacity.

## 📌 Outcome

Installed the Data Deduplication role service on Windows Server 2022, prepared two folders containing identical Windows Server DVD contents on the 20 GB `E:\` volume, and configured the volume for **General purpose file server** deduplication with a **0-day** file age. Ran and monitored the optimization job using PowerShell. After completion, Server Manager reported a **54%** deduplication rate and **5.26 GB** in savings, while File Explorer showed **15.5 GB** of free space.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This practical exercise demonstrates how Windows Server Data Deduplication can identify repeated data and reduce storage consumption on a volume. The test used duplicate copies of the Windows Server DVD contents so that the effect of optimization could be observed and verified through Server Manager and PowerShell.

## ✅ Closing

The Data Deduplication configuration and optimization process were completed successfully on the `E:\` volume. The final deduplication statistics were verified in Server Manager, and the available free space was confirmed in File Explorer.
