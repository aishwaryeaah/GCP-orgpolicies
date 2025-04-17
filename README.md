constraints/gcp.restrictNonCmekServices for cloudfunctions gen2



Outcome - 
Org policy is enforced 
CMEK is required 
Cloud Functions Gen2 is being blocked unless CMEK is provided (--kms-key)


FYI for (cloud run functions / cloud functions gen 2) -
Why Artifact Registry is Required When Using CMEK with Cloud Functions (Gen2)
Cloud Functions (Gen2) are built and deployed as container-based workloads. These containers are built using Cloud Build and stored in Artifact Registry before being executed by Cloud Run. –imp– When enabling Customer-Managed Encryption Keys (CMEK) for Cloud Functions, the entire deployment and execution pipeline must be CMEK-compliant.
Why It Is Required
When the organization policy constraint constraints/gcp.restrictNonCmekServices is enforced, any Google Cloud services that do not use CMEK will be blocked. Therefore, it is necessary that all components involved in the lifecycle of the Cloud Function are configured to use CMEK. This includes:
Cloud Run: Executes the function container and must be protected using the specified CMEK.


Artifact Registry: Stores the function’s container image. This repository must be CMEK-enabled and configured to use the same key.


Cloud Build (implicitly): Builds the container image from the source code.


Required Permissions
To successfully deploy a CMEK-enabled Cloud Function, the following service accounts must be granted the roles/cloudkms.cryptoKeyEncrypterDecrypter role on the key:
Cloud Run Functions Service Agent: service-PROJECT_NUMBER@gcf-admin-robot.iam.gserviceaccount.com


Artifact Registry Service Agent: service-PROJECT_NUMBER@gcp-sa-artifactregistry.iam.gserviceaccount.com


Cloud Run Service Agent: service-PROJECT_NUMBER@serverless-robot-prod.iam.gserviceaccount.com


Cloud Storage Service Agent: service-PROJECT_NUMBER@gs-project-accounts.iam.gserviceaccount.com


These service agents require access to decrypt or encrypt data using the CMEK to ensure all operations involving function deployment and execution are permitted.
 

If the org policy is disabled -
(gcloud.functions.deploy) ResponseError: status=[400], code=[Ok], message=[Could not create Cloud Run service test-crf-4. Constraint constraints/gcp.restrictNonCmekServices violated for CreateService attempting to create revision without a CMEK key. See https://cloud.google.com/resource-manager/docs/organization-policy/org-policy-constraints for more information.]




cloudfunctions api is under restricted services - (NOT using CMEK)
Error - 
(gcloud.functions.deploy) ResponseError: status=[400], code=[Ok], message=[The request has violated one or more Org Policies. Please refer to the respective violations for more information:
type: "constraints/gcp.restrictNonCmekServices"
subject: "orgpolicy:projects/aishwarya-orgpolicy-test"
description: "Constraint constraints/gcp.restrictNonCmekServices violated for projects/aishwarya-orgpolicy-test attempting PrepareCreateFunction with `service domain` value set to cloudfunctions.googleapis.com. See https://cloud.google.com/resource-manager/docs/organization-policy/org-policy-constraints for more information."
]


Policy enforced - but not using same key as artifact registry 
Error : 
User managed repository projects/aishwarya-orgpolicy-test/locations/europe-west2/repositories/test-cmek-cloudfunctions must have been encrypted using the same KMS key as of the function (projects/aishwarya-orgpolicy-test/locations/europe-west2/keyRings/test-keyring2/cryptoKeys/unused-key).


if service agent for cloud run functions is removed from kms encrypter decrypter role
(gcloud.functions.deploy) ResponseError: status=[403], code=[Ok], message=[KMS encrypt/decrypt permission denied on the key 'projects/aishwarya-orgpolicy-test/locations/europe-west2/keyRings/my-keyring/cryptoKeys/my-cmek-key'. This can be fixed by granting the GCF P4SA 'serviceAccount:service-800420767752@gcf-admin-robot.iam.gserviceaccount.com' the role 'roles/cloudkms.cryptoKeyEncrypterDecrypter' on the key.


if service agent for storage bucket is removed from kms encrypter decrypter role.
(gcloud.functions.deploy) ResponseError: status=[403], code=[Ok], message=[Unable to clone object to bucket 'gcf-v2-sources-800420767752-308646351'. KMS encrypt/decrypt permission denied on the key 'projects/aishwarya-orgpolicy-test/locations/europe-west2/keyRings/my-keyring/cryptoKeys/my-cmek-key'. This can be fixed by granting 'serviceAccount:service-$PROJECT_NUMBER@gs-project-accounts.iam.gserviceaccount.com' the role 'roles/cloudkms.cryptoKeyEncrypterDecrypter' on the key.]


if cloud run sa is removed from the role - 
(gcloud.functions.deploy) OperationError: code=13, message=Could not create or update Cloud Run service test-crf-7. Revision 'test-crf-7-00001-ton' is not ready and cannot serve traffic. Please ensure that the provided encryption key URL is correct and the project's Google Cloud Run Service Agent has the permission [cloudkms.cryptoKeyVersions.useToDecrypt] and [cloudkms.cryptoKeyVersions.useToEncrypt] on resource projects/aishwarya-orgpolicy-test/locations/europe-west2/keyRings/my-keyring/cryptoKeys/my-cmek-key. Permission 'cloudkms.cryptoKeyVersions.useToEncrypt' denied on resource 'projects/aishwarya-orgpolicy-test/locations/europe-west2/keyRings/my-keyring/cryptoKeys/my-cmek-key' (or it may not exist).
