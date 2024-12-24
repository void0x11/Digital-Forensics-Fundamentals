
digital forensics is the application of computer science to investigate digital evidence for a legal purpose. Digital forensics is used in two types of investigations:

1. **Public-sector investigations** refer to the investigations carried out by government and law enforcement agencies. They would be part of a crime or civil investigation.
2. **Private-sector investigations** refer to the investigations carried out by corporate bodies by assigning a private investigator, whether in-house or outsourced. They are triggered by corporate policy violations

### <span style="color:rgb(255, 192, 0)">The need for DFIR</span>

DFIR helps security professionals in various ways, some of which are summarized below:

- Finding evidence of attacker activity in the network and sifting false alarms from actual incidents.
- Robustly removing the attacker,  so their foothold from the network no longer remains.
- Identifying the extent and timeframe of a breach. This helps in communicating with relevant stakeholders.
- Finding the loopholes that led to the breach. What needs to be changed to avoid the breach in the future?
- Understanding attacker behavior to pre-emptively block further intrusion attempts by the attacker.
- Sharing information about the attacker with the community.

![[Pasted image 20241128231617.png]]

As a digital forensics investigator, you arrive at a scene similar to the one shown in the image above. What should you do as a digital forensics investigator? After getting the proper legal authorization, the basic plan goes as follows:

1. **Artifacts:** Artifacts are pieces of evidence that point to an activity performed on a system. When performing DFIR, artifacts are collected to support a hypothesis or claim about attacker activity. For example, if we are to claim that the attacker used Windows registry keys to maintain persistence on a system, we can use the said registry key to support our claim. In this case, the mentioned registry key will be considered an artifact. Artifact collection is, therefore, an essential part of the DFIR process. Artifacts can be collected from the Endpoint or Server's file system, memory, or network activity.
   
   the evidence is first collected and write-protected. Then, a copy of the write-protected evidence is used for analysis. This process ensures that our original evidence is not contaminated and remains safe while analyzing. If our copy under investigation gets corrupted, we can always return and make a new copy from the evidence we had preserved.

3. **Establish a chain of custody**: Fill out the related form appropriately ([Sample form](https://www.nist.gov/document/sample-chain-custody-formdocx)). The purpose is to ensure that only the authorized investigators had access to the evidence and no one could have tampered with it.
   
4. **Place the evidence in a secure container**: You want to ensure that the evidence does not get damaged. In the case of smartphones, you want to ensure that they cannot access the network, so they don’t get wiped remotely.
   
5. Order of volatility: Digital evidence is often volatile, i.e., it can be lost forever if not captured in time. For example, data in a computer system's memory (RAM) will be lost when the computer is shut down since the RAM keeps data only as long as it remains powered on. Some sources are more volatile as compared to others. For example, a hard drive is persistent storage and maintains the data even if power is lost. Therefore, a hard drive is less volatile than RAM. While performing DFIR, it is vital to understand the order of volatility of the different evidence sources to capture and preserve accordingly. 
   
   In the example above, we will need to preserve the RAM before preserving the hard drive since we might lose data in the RAM if we don't prioritize it.
   
6. Transport the evidence to your digital forensics lab and Once we have collected the artifacts and maintained their integrity, we need to present them understandably to fully use the information contained in them. A timeline of events needs to be created for efficient and accurate analysis. This timeline of events puts all the activities in chronological order. This activity is called timeline creation. Timeline creation provides perspective to the investigation and helps collate information from various sources to create a story of how things happened.

<span style="color:rgb(146, 208, 80)">At the lab</span>, the process goes as follows:

1. Retrieve the digital evidence from the secure container.
2. Create a forensic copy of the evidence: The forensic copy requires advanced software to avoid modifying the original data.
3. Return the digital evidence to the secure container: You will be working on the copy. If you damage the copy, you can always create a new one.
4. Start processing the copy on your forensics workstation.
### <span style="color:rgb(255, 192, 0)">Practical Example of Digital Forensics</span>

Note that exporting the file to other formats, such as `PDF`, would maintain most of the metadata of the original document, depending on the PDF writer used.

#### Files Metadata
We can try to read the pdf metadata using the program `pdfinfo` it displays various metadata related to a PDF file, such as title, subject, author, creator, and creation date.

```bash
pdfinfo DOCUMENT.pdf
```

#### Photo EXIF Data
EXIF stands for Exchangeable Image File Format; it is a standard for saving metadata to image files. Whenever you take a photo with your smartphone or with your digital camera, plenty of information gets embedded in the image. 

Metadata recorded examples:

- Camera model / Smartphone model
- Date and time of image capture
- Photo settings such as focal length, aperture, shutter speed, and ISO settings

Searching [Microsoft Bing Maps](https://www.bing.com/maps) or [Google Maps](https://www.google.com/maps) for `51 deg 30' 51.90" N, 0 deg 5' 38.73" W` reveals the street where the photo was taken. 
Note that for the search to work, we had to replace `deg` with `°` and remove the extra white space. In other words, we typed `51°30'51.9"N 0°05'38.73"W` in the map search bar.

``` bash
exiftool IMAGE.jpg
```

### <span style="color:rgb(255, 192, 0)">DFIR Tools</span> 

1. Eric Zimmerman's tools : These tools help the registry, file system, timeline, and many other analyses.
2. KAPE: Kroll Artifact Parser and Extractor (KAPE)This tool automates the collection and parsing of forensic artifacts and can help create a timeline of events.
3. Autospy: Autopsy is an open-source forensics platform that helps analyze data from digital media like mobile devices, hard drives, and removable drives. Various plugins for autopsy speed up the forensic process and extract and present valuable information from the raw data sources.
4. Volatility: Volatility is a tool that helps perform memory analysis for memory captures from both Windows and Linux Operating Systems. It is a powerful tool that can help extract valuable information from the memory of a machine under investigation.
5. Redline: incident response tool developed and freely distributed by FireEye. This tool can gather forensic data from a system and help with collected forensic information.
6. Velociraptor: Velociraptor is an advanced endpoint-monitoring, forensics, and response platform. It is open-source but very powerful.

### <span style="color:rgb(255, 192, 0)">The Incident Response Process</span>

SANS has published an [Incident Handler's handbook](https://www.sans.org/white-papers/33901/). The handbook defines the steps as follows:

![[Pasted image 20241129032314.png]]

1. **Preparation** : Preparation includes having the required people, processes, and technology to prevent and respond to incidents.
2. **Identification**: An incident is identified through some indicators in the identification phase. These indicators are then analyzed for False Positives, documented, and communicated to the relevant stakeholders.
3. **Containment**: In this phase, the incident is contained, and efforts are made to limit its effects. There can be short-term and long-term fixes for containing the threat based on forensic analysis of the incident that will be a part of this phase.
4. **Eradication**: Next, the threat is eradicated from the network. It has to be ensured that a proper forensic analysis is performed and the threat is effectively contained before eradication. For example, if the entry point of the threat actor into the network is not plugged, the threat will not be effectively eradicated, and the actor can gain a foothold again.
5. **Recovery**:  Once the threat is removed from the network, the services that had been disrupted are brought back as they were before the incident happened.
6. **Lessons Learned**: Finally, a review of the incident is performed, the incident is documented, and steps are taken based on the findings from the incident to make sure that the team is better prepared for the next time an incident occurs.

The steps defined by SANS are often summarized as the acronym PICERL.

