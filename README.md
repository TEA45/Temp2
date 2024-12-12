import requests

def get_internal_name_for_idp_migration_status(SystemInfo, SysLog):
    """
    Fetch the internal name for the IDP custom field 'Migration Status'.
    """
    SysLog.logInfo("Calling IDP API to fetch the internal name for 'Migration Status'...")

    # API Endpoint
    url = "https://infinity.dev.fiscloudservices.com/idp/DEV547-D1-csmr/rest/1.0/users/page/list"

    # Headers
    headers = {
        "Accept": "application/json",
        "Content-Type": "application/json",
        "X-SunGard-IdP-API-Key": "DEV547-D1-CSMR_APIKey",
        "Authorization": "Bearer c482766b9a19d5e97acc68416c015527"
    }

    # Query parameters
    params = {"searchCrit": "loginName eq 'Migration Status'"}

    try:
        # Make the API call
        response = requests.get(url, headers=headers, params=params)

        if response.status_code == 200:
            SysLog.logInfo("Successfully retrieved data from IDP API.")
            data = response.json()

            # Assuming the response contains 'fields' with internal names
            if "fields" in data:
                for field in data["fields"]:
                    if field.get("name") == "Migration Status":
                        internal_name = field.get("internalName")
                        SysLog.logInfo(f"Internal name for 'Migration Status': {internal_name}")
                        return internal_name

            SysLog.logInfo("'Migration Status' not found in the response.")
            return None
        else:
            SysLog.logError(f"Failed to fetch data. Status Code: {response.status_code}, Response: {response.text}")
            return None

    except Exception as e:
        SysLog.logError(f"Error occurred while fetching data: {str(e)}")
        return None




[IDPApiInfo]
idpApiBaseUrl = https://infinity.dev.fiscloudservices.com/idp/DEV547-D1-csmr/rest/1.0/users/page/list
idpApiKey = DEV547-D1-CSMR_APIKey
idpAuthorization = Bearer c482766b9a19d5e97acc68416c015527






from NightlyProcess.getinternalnameforidpmigstatus import get_internal_name_for_idp_migration_status

def NightlyProcessLauncher(argv, SysApp, configFile):
    # Fetch the internal name for Migration Status
    migration_status_internal_name = get_internal_name_for_idp_migration_status(SysApp, SysLog)
    if migration_status_internal_name:
        SysApp.migrationStatusInternalName = migration_status_internal_name
        SysLog.logInfo(f"Migration Status Internal Name set: {migration_status_internal_name}")
    else:
        SysLog.logError("Failed to fetch the internal name for 'Migration Status'.")
