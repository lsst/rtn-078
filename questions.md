Questions
- Identity services? LDAP for user authentication to S3DF, Kubernetes auth.  What is failback to local accounts in the event LDAP is down?
- Need to account for connection to Summit up, but Internet down in DR event?
- Kubernetes and vCluster binaries kept locally if hardware fails and need to redeploy?
- Copy of configs from GitHub?
- F5 dependence for ingress?  Is it failback to nginx or other software solution if hardware lead time is too long?
- Secrets in Vault.  Are they backed up offsite?
- How does a vCluster restore work?   Restore vCluster from etcd backup or deploy as new and apply configs? 
- Is intent to always try to bring stuff back up in USDF? Or should there be plan to bring everything up elsewhere? (i guess negliable risk means the former?)
