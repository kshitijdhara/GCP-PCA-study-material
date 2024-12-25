# Computing in Google Cloud 💻
- [Computing in Google Cloud 💻](#computing-in-google-cloud-)
  - [Virtual Machines: Your Digital Workforce](#virtual-machines-your-digital-workforce)
    - [Types of VMs](#types-of-vms)
    - [Special VM Types](#special-vm-types)
  - [VM Best Practices: Optimizing Your Digital Workforce](#vm-best-practices-optimizing-your-digital-workforce)
  - [Storage Options: Your Digital Filing System](#storage-options-your-digital-filing-system)
  - [Image Management Best Practices:](#image-management-best-practices)
  - [Image Families Best Practices:](#image-families-best-practices)
  - [Disk Snapshots](#disk-snapshots)
  - [Unmanaged Instance Groups](#unmanaged-instance-groups)
  - [References](#references)

Google Cloud Platform offers a comprehensive suite of compute options and best practices to help you build and manage your applications efficiently. Let's dive deeper into these concepts with more descriptive explanations:

## Virtual Machines: Your Digital Workforce

Google Cloud's Virtual Machines (VMs) are like a team of digital workers you can customize for various tasks. Imagine you're running a digital factory, and each VM is a specialized worker:

### Types of VMs

1. **Standard VMs**: These are your versatile workers, balancing CPU and memory. They're like skilled craftspeople who can handle a variety of tasks efficiently.

2. **High-CPU VMs**: Picture these as your rapid-fire assembly line workers. They excel at CPU-intensive tasks, like data processing or rendering, churning through calculations at lightning speed.

3. **High-Memory VMs**: These are like your data analysts with photographic memories. They're perfect for tasks that require holding vast amounts of information in memory, such as large databases or in-memory analytics.

4. **Custom VMs**: Imagine being able to create a worker with precisely the skills you need. Need someone with the speed of a High-CPU VM but with a bit more memory? Custom VMs let you fine-tune the CPU-to-memory ratio to match your exact requirements.

### Special VM Types

- **Shielded VMs**: Think of these as your security-cleared workers. They come with built-in defenses against rootkits and boot-level malware, ensuring the integrity of your operations from the ground up.

- **Confidential VMs**: These are like workers in a top-secret facility. They encrypt data while it's being processed, ensuring that even if someone were to peek at the VM's memory, they'd only see encrypted gibberish.

- **Preemptible/Spot VMs**: Imagine temporary workers you can hire at a steep discount. They're perfect for batch jobs or non-critical tasks, but they might be asked to leave at short notice if resources are needed elsewhere.

- **Tau VMs**: These are your cost-effective workhorses, designed for large-scale, container-based workloads. They offer the best price-performance ratio, making them ideal for running containerized microservices, web serving, or large-scale Java applications.

## VM Best Practices: Optimizing Your Digital Workforce

1. **Distribute Across Zones**: This is like having your workers in different office locations. If one location goes down due to a power outage or natural disaster, your operations continue uninterrupted in other zones.

2. **Use Managed Instance Groups (MIGs)**: Think of this as having an intelligent HR system that automatically scales your workforce. It can spin up new VMs when demand is high and shut them down when it's low, ensuring you're always optimally staffed without manual intervention.[Auto healing](https://cloud.google.com/compute/docs/instance-groups/autohealing-instances-in-migs)

3. **Implement Startup and Shutdown Scripts**: These are like giving your workers a detailed checklist for starting and ending their shifts. Startup scripts can automatically install software, configure settings, or fetch necessary data when a VM boots up. Shutdown scripts ensure proper cleanup, like saving logs or syncing data, before a VM is terminated.

4. **Leverage Image Families**: This is akin to having a standardized uniform and toolkit for your workers. Image families make it easy to keep your VMs up-to-date with the latest patches and configurations, while still allowing you to roll back if needed.

5. **Optimize Disk Usage**: Just as you'd organize your physical workspace for efficiency, optimizing disk usage ensures your VMs perform at their best. Use different storage types based on your needs:
   - PD HDD for bulk storage of infrequently accessed data
   - PD SSD for faster, more frequent read/write operations
   - Local SSD for extremely low-latency, high-performance storage needs

## Storage Options: Your Digital Filing System

Your storage options in Google Cloud are like different types of filing systems, each with its own strengths:

1. **Persistent Disk (PD)**: This is your reliable, network-attached storage. It comes in several flavors:
   - Standard PD: Like a traditional filing cabinet. Reliable and cost-effective for general use.
   - Balanced PD: A more modern filing system that offers a good balance of performance and capacity.
   - SSD PD: An electronic filing system with rapid access times, perfect for databases or I/O-intensive applications.
   - Extreme PD: The fastest filing system available, designed for the most demanding workloads like high-performance databases.
   - [PD Restrictions](https://cloud.google.com/compute/docs/disks#pdnumberlimits)
   - [Documentation](https://cloud.google.com/compute/docs/disks#pdspecs_rw)

2. **Local SSD**: This is like having a high-speed document scanner right on your desk. It offers blazing-fast performance but is tied to the lifecycle of your VM.

3. **RAM disk**: Imagine having critical information memorized. It's the fastest possible access, but the data is lost if the VM restarts.

## Image Management Best Practices:
- An image is a bundle of the raw bytes used to create a prepopulated disk, needs a master boot record and bootable partition to be bootable. Image families cannot be stored in Cloud Storage by the end user, they must be in the Custom Images service
- There are Public Images for use at no extra cost, and also premium images like RHEL and Windows that incur hourly fees
- If you use a startup script to deploy your applications as the instances boot, make sure the script is idempotent to avoid partially configured states or inconsistent configurations, the startup script can start a tool like Chef or Ansible
- The process of creating a custom image is called baking. It can be manual, automated, or imported. Manual would be starting with a public image, customizing it, then creating a custom image from the boot disk. Packer is a good tool for automated baking, and importing is a migration conversation.
- Recommended to shut down instances before creating new custom image
- Images are encrypted by default but you can also bring your own key.
- Image Families help you manage images in your project by grouping related images together to make rolling forward and backwards easier.
- Images are good candidates to span multiple projects, you can use a shared set of images to meet best practices for security, etc.

## Image Families Best Practices:
- Allows users to keep track of the image-family name, not an exact image, which can be useful for easier versioning, kind of like a Docker image.
- Public images are grouped into image families and always points to the latest version that is available in your VM’s zone
- You can create your own custom image families and deprecate old images, test your latest referenced images from the image families before using it in production
- Use Cloud Trace to help you diagnose latency issues caused by application-serving requests
- Leverage Cloud CDN for cacheable resources, host static content on GCS buckets to reduce web server load, deploy across regions if possible to bring apps closer to users
- Use Cloud Load Balancers and a Managed Instance Group instead of floating IP addresses – Review differences from Floating IP Addresses

## Disk Snapshots

- Snapshots are a project-level resource, but can now be shared across projects. Like Images, they are stored in GCS but only visible to users through the Snapshot interface
- They are incremental, the first snapshot is full and the subsequent ones only contain differences
- You can take a snapshot of a VM in one zone and create a new VM off of it in a different zone, it is the basis for the gcloud compute instances move command
- On Linux, if it’s a boot disk, halt the system. If it’s secondary, unmount first. If you can’t unmount, stop apps from writing, complete pending writes with sudo sync and suspend writing with sudo fsfreeze -f /path/to/mountpoint
- On Windows, use Volume Shadow Service (VSS)
- Determine whether you need crash consistent snapshots or application consistent snapshots. Crash consistent snapshots are used when applications are running, but you’ll likely need to replay file system and application-level journals before use. Application consistent snapshots require pausing your applications, potentially between multiple persistent disks. You don’t need to stop the VM instance to do this, but it will require pausing your apps
- You can snapshot your disk once every 10 minutes, best practice is to snapshot a disk once per hour, and use a snapshot schedule to do this
- You can also only create new zonal persistent disks from a snapshot at most once every 10 minutes
- If you have existing snapshots of a persistent disk, the system automatically uses them as a baseline for any subsequent snapshots that you create from that same disk
- Snapshot creation usually peaks at midnight. Do it off hours for faster speeds.
- Organize your data on separate persistent disks so you’re not snapshotting excessive data, and use discard or fstrim on Linux before you create a snapshot so you don’t bring in files you don’t need
- If you use a snapshot frequently, you can save on networking costs by creating a custom image of the snapshot.
- A custom image can be created from a running disk, you don’t need to snapshot it first

## Unmanaged Instance Groups

Unmanaged instance groups are collections of instances that are not necessarily identical and do not share a common instance template

Best for load balancing dissimilar instances, which you can add and remove arbitrarily

**Autoscaling, autohealing, and rolling updates** are not supported

Each unmanaged instance group can contain a maximum of 500 instances

Unmanaged instance groups have to be in one zone

Deleting an unmanaged instance group leaves the underlying instances behind


By understanding these concepts in depth and following best practices, you're setting up your digital infrastructure for success. Remember, just like in a physical workplace, it's about choosing the right tools, organizing efficiently, and continuously optimizing for better performance.

## References
- [Google Cloud Compute Engine Documentation](https://cloud.google.com/compute/docs)
- [Choosing the right VM](https://www.youtube.com/watch?v=QZ8PmZjF9vw)


[App Engine: Serverless is the Way!](<Part 6.md>) ➡️
