# Chapter 16 - Artifacts as Evidence

## Types of Artifacts
Digital Forensics is a six phase process including Preparation, Identification, Collection, Preservation, Analysis and Reporting.

Analysis is a major phase where in forensicators discover different types of artifacts ranging from plain metadata to complex evidence of execution and residual traces. The vast gap between the difficulty to retrieve or reconstruct evidence determines the fine line between E-discovery and Digital Forensics.

User data such as internet history, images, videos, emails, messages etc fall under E-discovery. It is relatively easy to reconstruct even from the unallocated space. 

However, System Data like artifacts that help support some view of truth, or determine how closely a transpired event is to the evidence, are not that simple to manually parse with forensic soundess, which is why oftentimes forensicators rely on well-known parsing tools either commercial or opensource.

![EDvsDF](https://raw.githubusercontent.com/Nisarg12/CrowdsourcedDFIRBook/main/manuscript/resources/Chapter16_EDvsDF.png)

And that is the main difference between E-Discovery & Digital Forensics depending on the categorization of data alone. Both follow different procedures and have different scope of execution. Generally, E-Discovery can be contained to only the logical partitions and the unallocated region whereas Digital Forensics operates in a much wider scope solely due to the necessity of dealing with complex data structures.

## What is Parsing?
Which brings us to parsing. We often go around throwing the term  while working with a variety of artifacts;
"Parse this, parse that", but what does it mean in the real sense? To understand the parsing methodology, tools & techniques, we must be familiar with the origin of the handling of the data being parsed. What I mean by that is how was the data originally meant to be handled. What was it's structure by design. How can it be replicated.

Generally, it is some feature or underlying mechanism of the main operating system installed on the device. Parsing tools are written to accurately mimic those functions of the operating system which make the raw data stored on the hardware, human readable.

<img width="500" alt="Computer_Structure" src="https://upload.wikimedia.org/wikipedia/commons/b/b7/Role_of_an_Operating_System.svg">

Understand the operating system as an abstraction level between the end-user and the intricacies of raw data. It provides an interface to the user which hides all the complexities of  computer data and how it is being presented.

Before parsing the artifacts and diving deep into analysis, you must fully understand how files are generally handled by an operating system. As mentioned earlier, an operating system is just a very sophisticated piece of software written by the manufacturers to provide an abstraction level between the complexities of hardware interactions and the user.

In the context of file handling, operating systems either *store* files or *execute* files. Both of which requires different types of memory. Also note that *storing* files requires access to a storage media such as HDDs, SSDs and Flash drives, whereas *executing* files requires access to the microprocessor. Both are handled by the operating system.

As you might already know, computers or any electronic computing device for that matter, primarily utilize two types of memory:
* **RAM (Random Access Memory):**
Volatile memory, only works for the time power is supplied. Used for assisting execution of applications/software by the processor of the device.
* **ROM (Read Only Memory):**
Non-volatile memory, retains data even when not in use. Used for storing the application files for a larger period of time.

There are many sub-types of both RAM & ROM but only the fundamental difference between them is concerned here.
Now let's quickly look at the timeline of an application lifecycle in 2 stages:

* **Production Cycle:**
An application is a set of *programs*. A program is a set of *code* written by a programmer, generally in higher levelled languages that do not interact directly with machine level entities such as registers, buses, channels etc. That piece of code is written to the disk. The code is then compiled to assembly, which is a lower levelled language which can interact directly with machine level entities. Finally the assembly is converted to the machine code consisting of 1s and 0s (also known as binary or executable file), which is now ready for it's execution cycle.
* **Execution Cycle:**
Now that the program is sitting on the disk, waiting to be executed, it is first loaded into the RAM. The operating system instructs the processor about the arrival of this program and allocates the resources when they're made available by the processor. The processor's job is to execute the program one instruction at a time. Now the program can execute successfully if the processor is not required to be assigned another task with a higher priority. If so, the program is sent to the ready queue. The program can also terminate if it fails for some reason. However, finally it is discarded from the RAM.

You can easily remember both of these cycles by drawing an analogy between electronic memory and the human memory. Here, I use chess as an example. Our brains, much like a computer, uses two types of memory:
* **Short-term (Working memory):**
For a game of chess, we calculate the moves deeply in a vertical manner for a specific line based on the current position. This is calculative in nature. Calculation comes from present situation.
* **Long-term (Recalling memory):**
At the opening stage in a game of chess, we consider the candidate moves widely in a horizontol manner for many lines. This is instinctive in nature. Instinct comes from past experiences.

## Artifact-Evidence Relation
You will come across an ocean of different artifacts in your investigations, but artifacts have a very strange relationship with what might potentially be considered evidence. Artifacts alone do not give you the absolute truth of an event. They provide you tiny peepholes through which you can reconstruct and observe a part of the truth. In fact, one can never be sure if what they have is indeed the truth in it's entirety.

<img width="500" alt="Truth" src="https://raw.githubusercontent.com/Nisarg12/CrowdsourcedDFIRBook/main/manuscript/resources/Chapter16_Truth.jpg">

I always love to draw an analogy between the artifacts and the pieces of a puzzle, of which you're not certain to have the edge or the corner pieces. You gather what you can collect, and try to paint the picture as unbiased and complete as possible.

<img width="500" alt="Puzzle" src="https://raw.githubusercontent.com/Nisarg12/CrowdsourcedDFIRBook/main/manuscript/resources/Chapter16_Puzzle.png">

That being said, if you apply the additional knowledge from metadata, OSINT and HUMINT to the parsed artifacts, you might have something to work with. For instance, say you were assigned an employee policy violation case, where the employee was using their work device for illegally torrenting movies. Parsing the artifacts alone will give you information around the crime, but not as evidence. You would still need to prove that the face behind the keyboard at the time of the crime, was indeed the one that your artifacts claim. So you would then look for CCTV camera footage around the premises, going back to the Identification phase in the digital forensics lifecycle, and so forth and so on.

As a result of a codependency of the artifacts on drawing correlations to some external factor, they form a direct non-equivalence relation with evidence.

However, note that this "rule", if you will, is only applicaple to a more broad scope of the investigation and will generally be handled by the lawyers. In the more narrow scope as a forensicator, and for the scope of your final forensic report, artifacts are most critical. Just keep it in the back of your mind that encountering an artifact alone does not mean it's admissible evidence. Parse the artifact, make notes and document everything. Being forensically sound is more important than worrying about completing the entire puzzle. Because there will be no edge or corner pieces of the puzzle.

## Examples
This section will cover how some of the more uncommon artifacts can play into a case from the bird's eye view. We won't be getting into the technical specifics on parsing or extraction, but the significance of those artifacts on a higher level. Such as what does it offer, proove and deny. And what is it's forensic value.

### Registry
Windows registry is a heirarchical database used by the Windows operating system to store it's settings and configurations. Additionally, it also stores some user data pertaining to user applications, activities and other residual traces.

Registry is structured with what are called Hives or Hive Keys (HK) at the top-most level. Each hive contains numerous keys. A key can contain multiple sub-keys. And sub-keys contain fields with their values.

There are some important hive files with rich forensic value:
* **System Hive Files:**
	* SAM (Security Account Manager) - User account information such as hashed passwords, account metadata including last login timestamp, login counts, account creation timestamp, group information etc.
	* SYSTEM - File execution times (Evidence of Execution), USB devices connected (Evidence of Removable Media), local timezone, last shutdown time etc.
	* SOFTWARE - Information about both user and system software. Operating System information such as version, build, name & install timestamp. Last logged on user, network connections, IP addresses, IO devices etc
	* SECURITY - Information about security measures and policies in place for the system.
* **User Specific Hive Files:**
	* Amcache.hve - Information about application executables (Evidence of Execution), full path, size, last write timestamp, last modification timestamp and SHA-1 hashes.
	* ntuser.dat - Information about autostart applications, searched terms used anywhere in the operating system, recently accessed files, run queries, last execution times of applications etc.
	* UsrClass.dat - Information about user specific shellbags, covered in the next section.

### Shellbags
### Prefetch
### Jumplists & LNK files
### SRUDB.dat
### $MFT
### $130
### $LogFile
### hiberfil.sys