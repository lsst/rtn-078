# USDF Disaster Recovery Plan

```{abstract}
Ensure the timely and effective recovery of critical data and systems in the event of a disaster or significant data loss at the Vera Rubin Observatory US Data Facility.
```

Purpose
=======
The purpose of this disaster recovery plan is to ensure the timely and effective recovery of critical data and systems in the event of a disaster or significant data loss at the Vera Rubin Observatory US Data Facility. The functions of the US Data Facility (USDF) are detailed in DMTN-189, and can be summarized as follows:

Cloud
- Google cloud
- Science users, with access provided by the Rubin Science Platform
- Provide personal storage/CPU + cloud access to coadds
- Effective 500 cores added each year + 2 PB → 10 PB storage

SLAC
- Housed in SRCF
- Prompt and DRP processing
- Qserv catalogue server
- Storage archive for all data: cutouts, image access
- Data delivery to partners, including IDACs.
- Serving alerts to the community
- Home for developers and staff (and commissioners)
- User batch


Scope
=====
This plan covers the recovery of essential systems and data housed in the facility. It includes preventive measures, strategies for data backup and restoration, and outlines the roles and responsibilities of the recovery teams.

Objectives
==========

- Minimize downtime and data loss.
- Ensure the safety and well-being of personnel.
- Provide a systematic approach for disaster recovery.
- Facilitate efficient communication during recovery efforts.

Risk Assessment and Analysis
============================

Identify Potential Risks
------------------------

Potential risks include natural disasters, equipment failures, cyber threats, and human errors.
Natural disasters involve widespread and potentially long-lasting loss of access to the USDF and its data and services. Certainly major earthquakes fit this description, and potentially wildfires in the area.
The other risks presumably don’t involve shutting down of access (though recent cyber incursions have done just that at other observatories) and hopefully just need data recovery steps to happen.

Assess Impact
-------------

Given the central nature of the USDF, a natural disaster would essentially stop Rubin operations. There is no plan to replicate the USDF elsewhere, although all components of the USDF are capable of being deployed on a cloud platform.
The summit can survive about a week’s outage of the USDF before data is lost. This would be the primary impact of long term loss of services.
 Short term outages affect the ability to send out alerts and the loss of near real time responses from other telescopes.
 
Prioritize Risks
----------------

SLAC is built to high earthquake standards. We believe the probability of SLAC going offline for very long periods of time is negligible. Area wildfires could result in sitewide power outages up to a few weeks, though S3DF can withstand outages as long as diesel is available to power its generators.
Loss of irreplaceable data is the highest more-probable risk and is mitigated by keeping multiple copies of that data, including in different facilities.
The Butler and Rucio databases play central roles in data access in the archive. They are particularly important to keep safe.

Recovery Strategies
===================

Hardware Recovery
-----------------

All hardware (compute servers, storage, network switches) used by Rubin at S3DF is ordinary configurations of standard parts.
As a result, disaster recovery can involve purchasing replacement hardware, borrowing equivalent hardware from another site (such as NERSC or another Data Facility), or renting equivalent hardware in the cloud.
The first two options are viable for disasters requiring partial replacement of the S3DF data center.
The capacity required for a complete replacement of the USDF is substantial, however, so replacement hardware may take a long time to arrive and other sites may not have sufficient spare capacity, making the cloud the most viable option for rapid total recovery.

Re-creating the Embargo Rack in the event of a disaster may be complex.
Other sites may not have the ability to dedicate physically-isolated machines or the ability to set up IPSec tunnels, although cloud providers do typically have VPN abilities using IPSec.
Waivers may be necessary for some of the physical security aspects of Embargo.

Service Recovery
----------------

Infrastructure services depend on S3 object stores, the distributed Weka POSIX filesystem (with S3 gateway), Kubernetes clusters, and a Slurm batch system.
All can be reproduced on new hardware.
Weka specifically has a cloud version, and all cloud providers have S3 and Kubernetes available as infrastructure services.
Software deployments can be reconstituted from the same deployment artifacts used at S3DF for services and batch jobs.
In particular, Kubernetes deployments are stored in GitHub.
Containers are stored in GitHub Container Registry or Google Artifact Registry, and a local cache is maintained to minimize requests to these services.
Pipelines code package artifacts are stored in eups.lsst.cloud.
Source code is in GitHub.

Data Recovery
-------------

The hardest part of disaster recovery will be recovering the data.
The USDF is comprised of petabytes of information, including raw images, the EFD and LFA, data products in Data Previews and Data Releases, Butler and Rucio databases containing metadata, and more.
Transferring this data from backups, whether tape, other sites like the French Data Facility, or cloud-based archives, will take substantial amounts of time at expected bandwidths.
Moving 1 PB at a sustained rate of 100 Gbit/sec takes 22 hours; actual transfers are likely to have higher overheads, and tape read rates will be even lower.
Here there is an advantage for restoring data from a cloud archive to a cloud-based recovery site, as the available effective bandwidth within a region can be very high and performance of certain types of archival storage can be nearly as good as online normal storage.


Backup and Restoration Procedures; Redundancy Measures
------------------------------------------------------

Implement regular backup procedures for critical data and establish efficient restoration processes.

Postgres databases:
- Hosted in Kubernetes
- Filesystem snapshots
- Uses native tools for replicas and routine backups to S3DF S3 storage

EFD:
- Enterprise HA license; hosted in Kubernetes
- Redundant servers (duplicate within cluster, additional standby replica)
- Application-level backups to Weka Persistent Volume

LFA:
- Hosted in S3DF S3 storage (s3dfrgw)
- Mirrored to Weka storage
- Weka storage is periodically backed up to tape

Data archive:
- All Weka-hosted data is snapshotted. Snapshots are good for error recovery; less so for disaster recovery.
- Released data products will be backed up to tape using Rucio.

Data transferred from the summit:
- Transferred to mirrored S3 storage in the Embargo Rack
- After embargo, transferred to Weka storage, French Data Facility, and tape

Offsite backups of key databases, the EFD, and the LFA should be arranged.

SLAC identity management, including membership in the ``rubin_users`` group, Vault secrets, Kubernetes Role-Based Access Control, Domain Name System entries, and other S3DF infrastructure are beyond Rubin's scope.
