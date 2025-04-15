Policy Enforcement Document: constraints/storage.secureHttpTransport
1. What is constraints/storage.secureHttpTransport?
The `constraints/storage.secureHttpTransport` is an Organization Policy Constraint provided by Google Cloud that enforces the use of HTTPS for all access to Cloud Storage buckets. When this constraint is set to `TRUE`, it prohibits any HTTP (unencrypted) communication to buckets, thereby ensuring encryption in transit for data security.
•	Key Points:
•	• Forces all communication to Google Cloud Storage to use HTTPS only.
•	• Blocks insecure HTTP requests that can expose data to threats.
•	• Supports encryption in transit best practices.
•	• Can be applied at the organization, folder, or project level.
•	• Retroactive in nature – impacts both new and existing resources once applied.
2. What Are We Changing?
We are enforcing the `constraints/storage.secureHttpTransport` policy across all projects. This means that any attempt to access Cloud Storage via HTTP will be blocked, and only HTTPS will be permitted going forward.
•	• Enabling this policy across organization/folder/project levels.
•	• Disabling all HTTP-based access to Cloud Storage buckets.
•	• Standardizing secure communications across all cloud services.
•	• Auditing and updating existing tools or services to use HTTPS.
•	• Introducing compliance checkpoints to monitor adherence.
3. What is the Action for the Project Team?
Project teams are required to proactively ensure that all their systems and integrations comply with the new enforcement. Failure to adapt will result in request failures and possible service disruptions.
•	• Review all existing applications, APIs, and scripts accessing Cloud Storage.
•	• Migrate any HTTP-based endpoints to HTTPS immediately.
•	• Check and update third-party integrations for HTTPS support.
•	• Coordinate with security and cloud governance teams to verify compliance.
•	• Test all access flows to confirm HTTPS-only behavior post-enforcement.
