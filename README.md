# Jenkins Database Pipeline

This Jenkins pipeline automates the process of validating database selections, generating MySQL credentials, and sending them via email.

## Features
- Validates user-selected database based on product and environment.
- Dynamically generates MySQL user credentials.
- Sends credentials via email.

## Pipeline Stages
1. **Prepare Database Mapping**  
   - Copies `database-mapping.json` from `/root/mount_try/new/` if not found in the workspace.

2. **Validate Database Selection**  
   - Ensures the selected database is valid for the chosen product and environment.

3. **Generate Credentials**  
   - Creates a unique MySQL user with access to the selected database.

4. **Send Email**  
   - Sends database credentials to the specified email addresses.

## Setup Instructions
1. Install Jenkins with required plugins (e.g., **Pipeline**, **Email Extension**).
2. Configure a Jenkins pipeline job and paste the `Jenkinsfile` content.
3. Store `MYSQL_ROOT_PASSWORD` securely in Jenkins credentials.
4. Ensure MySQL is accessible from Jenkins.

## Running the Pipeline
- Run the pipeline from Jenkins UI and select:
  - `product` (e.g., a, b, c)
  - `environment` (e.g., dev, sit, uat)
  - `database` (matching database name)

## License
This project is open-source under the MIT License.
