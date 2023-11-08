# Project_Migration
 Steps for migrating a project between organizations using Google Cloud Platform (GCP).
 
### **Project Migration Documentation**

#### **1. Check Organization Id:**
To check the organization id of a project, use the following command:

```bash
gcloud projects describe <project_id> --format="value(parent.id)"
```

#### **2. Organization and Project Ids:**
Define the organization and project ids for better clarity:

```bash
# Source Organization Id
<source_organization_id>     # Source organization name

# Destination Organization Id
<destination_organization_id>     # Destination organization name

# Source Project Id
<source_project_id>     # Source project name
```

#### **3. Organization Level Permissions (Both Source and Destination):**
Ensure the following permissions at the organization level for both source and destination organizations:

```bash
gcloud organizations add-iam-policy-binding <source_organization_id> \
    --member="user:<your_email@example.com>" \
    --role="roles/billing.admin"

gcloud organizations add-iam-policy-binding <source_organization_id> \
    --member="user:<your_email@example.com>" \
    --role="roles/resourcemanager.organizationAdmin"

# Repeat the above commands for destination_organization_id

gcloud organizations add-iam-policy-binding <destination_organization_id> \
    --member="user:<your_email@example.com>" \
    --role="roles/billing.admin"

gcloud organizations add-iam-policy-binding <destination_organization_id> \
    --member="user:<your_email@example.com>" \
    --role="roles/resourcemanager.organizationAdmin"
```

#### **4. Project Level Permissions (Source Project):**
Grant the necessary permissions at the project level for the source project:

```bash
gcloud projects add-iam-policy-binding <source_project_id> \
    --member="user:<your_email@example.com>" \
    --role="roles/resourcemanager.projectIamAdmin"

gcloud projects add-iam-policy-binding <source_project_id> \
    --member="user:<your_email@example.com>" \
    --role="roles/resourcemanager.projectMover"

# Repeat the above commands for other roles in the source project
```

#### **5. Allow API in Source Organization under Organization Policy:**
Allow the API in the source organization using the organization policy:

```bash
gcloud resource-manager org-policies allow resourcemanager.allowedExportDestinations \
    --organization=<source_organization_id> \
    --effect=allow
```

#### **6. Allow API in Destination Organization under Organization Policy:**
Allow the API in the destination organization using the organization policy:

```bash
gcloud resource-manager org-policies allow resourcemanager.allowedImportSources \
    --organization=<destination_organization_id> \
    --effect=allow
```

#### **7. Open CLI on Organization Level for Source Destination:**
Open the CLI for the source destination and initiate the project move:

```bash
gcloud beta projects move <source_project_id> --organization <destination_organization_id>
```

#### **8. Rollback:**
In case of issues, rollback the project move:

```bash
gcloud beta projects move <source_project_id> --organization <source_organization_id>
```

This comprehensive documentation should guide you through the process of migrating a project between organizations on Google Cloud Platform. Let me know if you have any questions or need further clarification!

###### **Refrence:** https://cloud.google.com/resource-manager/docs/project-migration
###### **Handle special cases:** https://cloud.google.com/resource-manager/docs/handle-special-cases
