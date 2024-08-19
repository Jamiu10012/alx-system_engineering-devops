###Issue Summary
Duration: August 12, 2024, 09:00 AM - 11:30 AM UTC (2 hours 30 minutes)

Impact: During the outage, 60% of users experienced significantly slow load times and intermittent timeouts when attempting to access the "Drug Verification" service on our Fake Pharmaceutical Drug Detection System. Affected users encountered 504 Gateway Timeout errors, preventing them from verifying drug authenticity.

Root Cause: The issue was traced to a misconfiguration in the MongoDB replica set, which resulted in a failure of automatic failover when the primary node became unreachable due to a network partition.

###Timeline
09:00 AM UTC: Issue detected through automated monitoring alerts indicating increased response times and a spike in 504 errors.
09:05 AM UTC: Initial investigation focused on the web servers and API gateway, assuming a possible load balancer or network issue.
09:30 AM UTC: Escalated to the database team after ruling out load balancer and web server issues; no anomalies found in the API layer.
10:00 AM UTC: Database logs reviewed; observed that the MongoDB primary node was unreachable and automatic failover had failed.
10:15 AM UTC: Misleading debugging path: team initially suspected a corrupted database instance or network latency issue between nodes.
10:30 AM UTC: The root cause identified as a configuration error in the MongoDB replica set preventing the secondary node from being promoted to primary.
11:00 AM UTC: Configuration corrected; MongoDB replica set reconfigured and restarted.
11:30 AM UTC: Service fully restored; monitoring confirmed normal operation and no further errors.
Root Cause and Resolution
Root Cause: The MongoDB replica set was misconfigured, specifically in the election timeout settings. The primary node became unreachable due to a transient network partition, but the remaining secondary node failed to promote itself to primary. This was because the election timeout was set too high, and the secondary node was not properly configured to take over quickly.

Resolution: The replica set configuration was updated to lower the election timeout value and ensure that all nodes were correctly prioritized for failover. The MongoDB service was then restarted to apply these changes. Additionally, the network partition was resolved, restoring connectivity to the original primary node, allowing the replica set to reestablish normal operation.

###corrective and Preventative Measures
Improvements and Fixes:

Database Configuration Review: Conduct a thorough review of all MongoDB replica set configurations to ensure proper failover settings are in place.
Monitoring Enhancements: Implement detailed monitoring and alerting for MongoDB election timeouts and replica set health to catch issues early.
Automated Failover Testing: Schedule regular failover drills to ensure that the MongoDB replica set can successfully promote a secondary node under failure conditions.
Documentation Update: Update internal runbooks with detailed steps for diagnosing and resolving similar database issues.
Specific Tasks:

Patch MongoDB Config: Lower the election timeout and adjust priority settings in the MongoDB configuration.
Enhance Monitoring: Add specific alerts for MongoDB replica set status and election outcomes.
Test Failover: Implement a monthly automated failover test to validate the resilience of the MongoDB replica set.
Update Runbooks: Document the root cause and resolution steps to guide future troubleshooting efforts.
