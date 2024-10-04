# Self Study Guide

### Network-Attached Storage (NAS)
**Network-Attached Storage (NAS)** is a file-level storage architecture that makes stored data more accessible to networked devices. NAS devices use network protocols such as NFS (Network File System), SMB/CIFS (Server Message Block/Common Internet File System), and AFP (Apple Filing Protocol) to facilitate file sharing. These protocols allow devices with different operating systems (Windows, macOS, Linux) to access the NAS seamlessly⁸.

**Key Features:**
- **File-Level Storage:** NAS stores data in files and folders, making it easy to manage and access.
- **Network Connectivity:** NAS devices connect to a network, allowing multiple users to access and share data.
- **Scalability:** NAS systems can be easily expanded by adding more storage devices.

### Storage Area Network (SAN)
**Storage Area Network (SAN)** is a high-speed network that provides block-level storage to multiple servers. SANs use protocols like iSCSI (Internet Small Computer Systems Interface) and Fibre Channel to connect storage devices and servers⁷[^10^].

**Key Features:**
- **Block-Level Storage:** SAN stores data in blocks, which can be managed by the server's operating system.
- **High Performance:** SANs offer high-speed data transfer rates, making them suitable for mission-critical applications.
- **Centralized Storage:** SANs provide a centralized storage solution, improving data management and security.

### Related Protocols
- **NFS (Network File System):** A protocol that allows file sharing over a network, commonly used in UNIX/Linux environments.
- **(s)FTP (Secure File Transfer Protocol):** A protocol for transferring files securely over a network.
- **SMB (Server Message Block):** A protocol for sharing files, printers, and other resources in Windows environments.
- **iSCSI (Internet Small Computer Systems Interface):** A protocol that allows the transmission of SCSI commands over IP networks, enabling block-level storage access.

### Block-Level Storage
**Block-Level Storage** divides data into blocks and stores them as separate pieces. Each block has a unique identifier, allowing the storage system to retrieve data quickly. This type of storage is commonly used by cloud service providers for applications that require fast and frequent access to data¹².

**Key Features:**
- **High Performance:** Suitable for databases and applications that require low-latency access.
- **Flexibility:** Blocks can be managed individually, allowing for efficient storage utilization.
- **Use Cases:** Virtual machines, databases, and transactional applications.

### Object Storage
**Object Storage** stores data as objects, each containing the data itself, metadata, and a unique identifier. This type of storage is ideal for large volumes of unstructured data, such as media files, backups, and logs¹³.

**Key Features:**
- **Scalability:** Can handle vast amounts of data, making it suitable for big data applications.
- **Metadata:** Each object includes metadata, providing additional context and information.
- **Use Cases:** Backup and archival, media storage, and big data analytics.

### Differences Between Block Storage, Object Storage, and Network File System (NFS) on AWS
**AWS Block Storage (EBS):**
- **Amazon Elastic Block Store (EBS)** provides block-level storage volumes for use with Amazon EC2 instances. EBS volumes are highly available and can be attached to any EC2 instance in the same Availability Zone².

**AWS Object Storage (S3):**
- **Amazon Simple Storage Service (S3)** is an object storage service that offers scalability, data availability, security, and performance. S3 is designed for storing and retrieving any amount of data from anywhere on the web¹.

**AWS Network File System (EFS):**
- **Amazon Elastic File System (EFS)** provides scalable file storage for use with Amazon EC2 instances. EFS supports the NFS protocol, allowing multiple instances to access the file system concurrently⁵.

**Comparison:**
- **Block Storage vs. Object Storage:** Block storage is ideal for applications requiring fast, low-latency access, such as databases and virtual machines. Object storage is better suited for storing large volumes of unstructured data, such as media files and backups¹².
- **Block Storage vs. NFS:** Block storage provides raw storage volumes that can be formatted with a file system, while NFS offers a file-level storage solution that can be accessed over a network⁵.
- **Object Storage vs. NFS:** Object storage is designed for scalability and managing large datasets, while NFS is suitable for shared file access and collaboration⁵.

