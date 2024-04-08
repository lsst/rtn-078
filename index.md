# USDF Disaster Recovery Plan

```{abstract}
Ensure the timely and effective recovery of critical data and systems in the event of a disaster or significant data loss at the Vera Rubin Observatory US Data Facility
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

Minimize downtime and data loss.
Ensure the safety and well-being of personnel.
Provide a systematic approach for disaster recovery.
Facilitate efficient communication during recovery efforts.

Risk Assessment and Analysis
============================

Identify Potential Risks
------------------------

Potential risks include natural disasters, equipment failures, cyber threats, and human errors.
Natural disasters involve widespread and potentially long-lasting loss of access to the USDF and its data and services. Certainly major earthquakes fit this description, and potentially wildfires in the area.
The other risks presumably don’t involve shutting down of access (though recent cyber incursions have done just that at other observatories) and hopefully just need data recovery steps to happen..

Assess Impact
-------------

Given the central nature of the USDF, a natural disaster would essentially stop Rubin operations. There is no plan to replicate the USDF elsewhere.
The summit can survive about a week’s outage of the USDF before data is lost. This would be the primary impact of long term loss of services.
 Short term outages affect the ability to send out alerts and the loss of near real time responses from other telescopes.
 
Prioritize Risks
----------------

SLAC is built to high earthquake standards. We believe the probability of SLAC going offline for very long periods of time is negligible. Area wildfires could result in sitewide power outages up to a few weeks, though S3DF can withstand outages as long as diesel is available to power its generators.
Loss of irreplaceable data is the highest more-probable risk and is mitigated by keeping multiple copies of that data, including in different facilities.
The butler and Rucio databases play central roles in data access in the archive. They are particularly important to keep safe. 

Recovery Strategies
===================

Backup and Restoration Procedures; Redundancy Measures
------------------------------------------------------

Implement regular backup procedures for critical data and establish efficient restoration processes.

Postgres databases:
- Hosted in kubernetes
- Filesystem snapshots
- Use native tools for replicas and routine backups and restores
- Filesystem snapshots are taken
- 
EFD:
- Enterprise HA license; hosted in kubernetes
- Redundant servers
- Filesystem snapshots
- Application-level backups (should be happening)


LFA?

Data archive

- All weka-hosted data is snapshotted. Caveat is that the snapshots don’t yet go to tape; that is in work by the s3df storage team.
- A separate tape backup is in work using Rucio
- 
Data transferred from the summit

- Transferred to S3 storage. Prior to tape backup being ready, these are copied to weka nightly.
- Essentially all services are hosted in kubernetes, allowing for easy movement to new pods.  All Kubernetes manifests to deploy applications are stored in the SLAC GitHub.  This is hosted by GitHub externally to SLAC. All application data is snapshotted, as well as application level backups to offline local (USDF) systems. These backups should also be replicated to an offsite location (Google Cloud? Fermi?)

Questions
- Identity services? LDAP for user authentication to S3DF, Kubernetes auth.  What is failback to local accounts in the event LDAP is down?
- DNS dependencies and failover?
- Need to account for connection to Summit up, but Internet down in DR event?
- Dependency on Internet connectivity for Docker images?  Keep critical images locally?
- Kubernetes and vCluster binaries kept locally if hardware fails and need to redeploy
- Copy of configs from GitHub?
- F5 dependence for ingress?  Is it failback to nginx or other software solution if hardware lead time is too long?
- Secrets in Vault.  Are they backed up offsite?
- How does a vCluster restore work?   Restore vCluster from etcd backup or deploy as new and apply configs? 
- Is intent to always try to bring stuff back up in USDF? Or should there be plan to bring everything up elsewhere? (i guess negliable risk means the former?)

Off-site Data Storage
=====================

The key irreplaceable data are the raw visit images and the EFD record.
 The raw data is transferred to the FrDF immediately upon release from USDF embargo for long term storage. During the embargo period, there are copies of the raw data at the summit and base.
There is a full copy of the EFD at the base, and a 30-day buffer at the summit.
We could send tapes to Iron Mountain for offsite archive.

Cloud-Based Backup Solutions
============================

Currently there is a full cloud version of the EFD hosted in the IDF.
Send all application level backups to Google S3?

Data Replication
================

Implement data replication strategies to ensure real-time synchronization between primary and backup systems.
Embargo CEPH cluster is an Active Replica (both clusters in same building tho).

