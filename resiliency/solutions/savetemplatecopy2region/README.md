# Save-Template-Copy-To-Region

Demonstrates use of Azure runbooks and leveraging [Skytap's API](https://help.skytap.com/api-quick-start.html#rest-api-quick-start) to: 
1. Create template can be created from a live environment
1. Copy template to another region
1. Use projects to organize
1. Delete prior versions of the template copy process

# Preparation 

Often times the 'Before you Begin' steps seems to be the longest part of the journey.  The script can be run from powershell directly but for extending it we can add to Azure Runbooks 

- In Azure create an [Automation Account](https://learn.microsoft.com/en-us/azure/automation/automation-create-standalone-account?tabs=azureportal) and open the resource.
- In Shared Resources 
    - Create Credentials providing Skytap username and [API key](https://help.skytap.com/kb-find-api-token.html)
    - Create variables for 
      - env_to_backup (id of environment to backup)
      - sleeptime (time delay for checking busy state of template)
      - target_region (destination region to copy to region)

- In Process Automation 'Import a runbook' and upload the .ps1 file in this repository.
  
# Description of code

Establish header information to connect to Skytap

``` powershell
# Get header setup to connect and make requests.  This leverages credential assets already in Azure
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType] 'Tls12'
$cred = Get-AutomationPSCredential -Name 'Skytap-cred'
$content = 'application/json'
$baseAuth = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $cred.UserName, $cred.GetNetworkCredential().Password)))
$header =  @{"Accept" = $content; Authorization=("Basic {0}" -f $baseAuth); "Content_type" = $content}
$uri = 'https://cloud.skytap.com/'
```

Connect to Skytap

``` powershell
# Connect to Skytap
Invoke-RestMethod -uri $uri -Headers $header -Method GET -sessionvariable session
```

Establish variables for use in script
``` powershell
# Get environment ID from variables
$env_to_backup = Get-AutomationVariable -Name 'env_to_backup'
$target_region = Get-AutomationVariable -Name 'target_region'
$delay = Get-AutomationVariable -Name 'Sleeptime'
$today = get-date -format o
$project_name = "DR plan for environment " + $env_to_backup
```

Functions
``` powershell
#Simplifies get requests to system.  
Function get_skytap ($r) {
    $path = if($r -match $uri){$r} else { $uri + $r}
	$result = Invoke-RestMethod -uri $path -WebSession $session 
	return, $result
}
#Simplifies setting things like environment runstate, adding vm, etc.
Function set_skytap ($pth, $jsn, $method) {
    $path = if($pth -match $uri){$pth} else {$uri + $pth}
    $json = $jsn | convertto-json
    $action = if($method -ne $null){$method} else {'POST'}
   	Invoke-RestMethod -uri $path -WebSession $session -Method $action -body $json -ContentType $content
}

#check for runstate
Function busyness ($b){
	$i = 0
	$check = get_skytap $b
	while($i -ne 3 -and $check.busy -ne $null){
		write-output 'inside while'
		$i++
		$check = get_skytap $b
		sleep $delay
	}
	return, $check
}
```
Make request to get environment
``` powershell
$environment_source = get_skytap "configurations/$($env_to_backup)"
```
Organize template use/deletion with project
``` powershell
# Get or create a project for organization and identifying templates that are a part of the backup process.
$project_check = (get_skytap 'projects?scope=company' | % {$_}) | ? {$_.name -like $project_name}
$project = if($null -eq $project_check){
	set_skytap  'projects.json' @{"name"= $project_name}
} else {$project_check}
$project_templates = $project.url + '/templates/'
write-output $project_templates
```
Create a template from target environment
``` powershell
# Write json to request template creation
$create_template_json = @{
    "configuration_id"= $environment_source.id;
	"name"= "$($environment_source.name) $($today)";
    "publish_sets"= 'true';
    "live_copy" = 'true'
}

# Create template 
$create_template = set_skytap 'templates.json' $create_template_json
```
Once available, copy template to target region
``` powershell
# If template in a busy state, it won't copy
if((get_skytap $create_template.url).busy -ne $null ){ 
	busyness $create_template.url
}

# Write json to request copy template override notice about suspend state of vm
$copytoregion_json = @{
    "template_id"= $create_template.id;
    "target_region"= $target_region;
	"name"= $create_template.name;
	"copy_to_region_vm_status_override" = 'true'
}

# Copy template
$template_copy = set_skytap 'templates.json' $copytoregion_json
```
Delete old copies from project and place new ones in project
``` powershell
# Delete target region older templates.  But only the ones in the DR project
if($null -ne $template_copy.id){
	$target_site_templates = get_skytap $project_templates 
	
	# Write json to delete older versions of template
	$multiselect_json = @{"multiselect"= @($target_site_templates.id)}
	write-output $multiselect_json
	# Destroy template(s)
	set_skytap 'templates/destroy_multiple.json' $multiselect_json DELETE

	# Add the new templates to project
	set_skytap ($project_templates + $template_copy.id)
	set_skytap ($project_templates + $create_template.id)
}
```


