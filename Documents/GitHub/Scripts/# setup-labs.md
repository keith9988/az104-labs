# setup-labs.ps1
# Create AZ-104 lab repo structure with templated READMEs

param(
  [string]$RepoPath = "$HOME/Documents/GitHub/az104-labs"
)

# Ensure repo path exists
if (-not (Test-Path $RepoPath)) {
  New-Item -ItemType Directory -Force -Path $RepoPath | Out-Null
}
Set-Location $RepoPath

# Define labs: folder name => title
$labs = @(
  @{ Folder = "lab01-resource-groups"; Title = "Lab 01 – Resource Groups" },
  @{ Folder = "lab02-networking";      Title = "Lab 02 – Networking (VNet, Subnet, NSG)" },
  @{ Folder = "lab03-vms";             Title = "Lab 03 – Virtual Machines" },
  @{ Folder = "lab04-storage";         Title = "Lab 04 – Storage Accounts & Blobs" },
  @{ Folder = "lab05-rbac";            Title = "Lab 05 – RBAC & IAM" }
)

# Create lab folders and templated README in each
foreach ($lab in $labs) {
  $folder = $lab.Folder
  $title  = $lab.Title

  New-Item -ItemType Directory -Force -Path $folder | Out-Null
  New-Item -ItemType Directory -Force -Path "$folder/screenshots" | Out-Null  # place PNGs here

  $labReadme = @"
# $title

> Date: $(Get-Date -Format 'yyyy-MM-dd')  
> Region used: \`uksouth\` (adjust to your nearest region)

## Objectives
- Briefly state the goals of this lab (e.g., create an RG and explore tags; create VNet/Subnet/NSG; deploy a VM; configure storage; assign RBAC).

## Portal Steps
1. (Step-by-step bullets of what you clicked/configured)
2. (Add notes about options you considered and why)
3. (Capture any validation checks you performed)

## CLI Steps (PowerShell / Azure CLI)
\`\`\`powershell
# Example skeleton – replace with commands you actually ran
# Resource Group
az group create -n az104-lab-rg -l uksouth

# Networking (example)
# az network vnet create ...
# az network nsg create ...

# VM (example)
# az vm create ...

# Storage (example)
# az storage account create ...
\`\`\`

## Screenshots
Place images in \`$folder/screenshots\` and reference them here, e.g.:
![Portal overview](./screenshots/overview.png)

## Key Takeaways
- What did you learn?
- Gotchas/pitfalls
- Commands or portal settings to remember

## Cleanup (Cost Control)
\`\`\`powershell
# Delete the whole lab resource group when finished
az group delete -n az104-lab-rg -y --no-wait
\`\`\`
"@

  $labReadme | Out-File -FilePath "$folder/README.md" -Encoding utf8
}

# Root README with links to each lab
$rootReadme = @"
# Azure Administrator (AZ-104) Study Labs

This repository contains my hands-on practice while preparing for the **Microsoft Certified: Azure Administrator Associate (AZ-104)** exam.  
Each lab includes **Portal** and **CLI** steps plus a short reflection (\_Key Takeaways\_). I keep costs low and clean up after each lab.

## Labs

| #   | Topic                               | Link |
|-----|-------------------------------------|------|
| 01  | Resource Groups                      | [Lab 01](lab01-resource-groups/README.md) |
| 02  | Networking (VNet, Subnet, NSG)       | [Lab 02](lab02-networking/README.md)      |
| 03  | Virtual Machines                     | [Lab 03](lab03-vms/README.md)             |
| 04  | Storage Accounts & Blobs             | [Lab 04](lab04-storage/README.md)         |
| 05  | RBAC & IAM                           | [Lab 05](lab05-rbac/README.md)            |

## How I Work
- Do each task in the **Portal** first to learn the UI
- Repeat the same task using **Azure CLI** to reinforce automation
- Keep screenshots in each lab’s \`/screenshots\` folder
- Delete the lab resource group when done to avoid costs

> Subscription: (redacted) · Primary Region: \`uksouth\`
"@

$rootReadme | Out-File -FilePath "README.md" -Encoding utf8

Write-Host "✅ Lab structure created at $RepoPath" -ForegroundColor Green
