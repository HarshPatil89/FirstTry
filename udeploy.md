1. Install Required Packages
bash
Copy
Edit
pip install requests
2. Set Up IBM UrbanCode Deploy API Access
IBM UrbanCode Deploy's REST API requires authentication using Basic Auth (Username + Password) or an Access Token.

Python Script to Access UrbanCode Deploy API
python
Copy
Edit
import requests
import json

# IBM UrbanCode Deploy Server Details
UCD_URL = "https://your-urbancode-deploy-server:8443"
USERNAME = "your-username"  # Replace with your UCD username
PASSWORD = "your-password"  # Replace with your UCD password
API_TOKEN = "your-api-token"  # (Use token if preferred, otherwise use username/password)

# Set authentication (Choose one)
auth = (USERNAME, PASSWORD)  # Basic Authentication
headers = {"Authorization": f"Bearer {API_TOKEN}"} if API_TOKEN else {}

# Disable SSL warnings (if needed)
requests.packages.urllib3.disable_warnings()

# Function to get applications from UrbanCode Deploy
def get_applications():
    url = f"{UCD_URL}/rest/deploy/application"
    response = requests.get(url, auth=auth, headers=headers, verify=False)  # 'verify=False' ignores SSL issues

    if response.status_code == 200:
        applications = response.json()
        print(json.dumps(applications, indent=4))
        return applications
    else:
        print("Error:", response.text)
        return None

# Function to get deployments of an application
def get_application_processes(application_id):
    url = f"{UCD_URL}/rest/deploy/application/{application_id}/processes"
    response = requests.get(url, auth=auth, headers=headers, verify=False)

    if response.status_code == 200:
        processes = response.json()
        print(json.dumps(processes, indent=4))
        return processes
    else:
        print("Error:", response.text)
        return None

# Get all applications
applications = get_applications()

# If applications exist, fetch their deployment processes
if applications:
    for app in applications:
        print(f"\nFetching processes for application: {app['name']}")
        get_application_processes(app["id"])
3. Explanation of the Script
Authenticates using either Basic Auth (username/password) or an API token.
Fetches all applications from the IBM UrbanCode Deploy server.
Retrieves deployment processes for each application.
4. Additional API Actions
You can modify the script to perform other tasks:

Trigger a Deployment:
python
Copy
Edit
def trigger_deployment(application_id, process_id, environment_id):
    url = f"{UCD_URL}/rest/deploy/applicationProcessRequest"
    payload = {
        "application": application_id,
        "applicationProcess": process_id,
        "environment": environment_id,
        "onlyChanged": False
    }

    response = requests.post(url, json=payload, auth=auth, headers=headers, verify=False)

    if response.status_code == 200:
        print("Deployment triggered successfully!")
        print(response.json())
    else:
        print("Error triggering deployment:", response.text)

# Example usage
trigger_deployment("app-id", "process-id", "env-id")
