``` powershell
<#
.SYNOPSIS
Sets the necessary file permissions on a private key file (.pem) for use with the
Windows OpenSSH client (ssh.exe).

.DESCRIPTION
The Windows OpenSSH client requires strict permissions on private key files.
This script resets permissions, disables inheritance, removes common default
group access, and grants only the current user Read access to the specified file.
It aims to resolve the "WARNING: UNPROTECTED PRIVATE KEY FILE!" and
"bad permissions" errors from ssh.exe on Windows.

.PARAMETER PemFilePath
The full path to the .pem private key file whose permissions need to be fixed.

.EXAMPLE
.\Set-SshKeyPermissions.ps1 -PemFilePath "C:\Users\Administrator\.ssh\ProductionServer.pem"

.EXAMPLE
.\Set-SshKeyPermissions.ps1 -PemFilePath "C:\ProgramData\.ssh\AnotherKey.pem"

.NOTES
Author: Sumesh
Version: 1.0
Requires sufficient privileges to modify file permissions (run as the user who
owns the file or as an Administrator).
Run this script in the PowerShell session where you intend to run the ssh command,
or as the user who will be running the ssh command.
#>
[CmdletBinding()]
param(
    [Parameter(Mandatory = $true, HelpMessage = "Path to the .pem private key file.")]
    [string]$PemFilePath
)

# Validate the input path is an existing file
if (-not (Test-Path -Path $PemFilePath -PathType Leaf)) {
    Write-Error "Error: File not found or path is a directory: '$PemFilePath'"
    # Stop script execution if file doesn't exist
    return
}

# Get the current user's name in a format icacls understands (DOMAIN\User or HOST\User)
try {
    $currentUser = [System.Security.Principal.WindowsIdentity]::GetCurrent()
    $userName = $currentUser.Name
    Write-Host "Target File: $PemFilePath"
    Write-Host "Current User: $userName"
    Write-Host "Applying required permissions for OpenSSH..."
}
catch {
    Write-Error "Error: Could not retrieve current user information. $_"
    return
}

# --- Permission Modification Steps ---
# Use /T to act on files in specified directory and subdirs (though only one file here)
# Use /C to continue on file errors (e.g., access denied on some) - less relevant for single file
# Use /Q to suppress success messages for cleaner output

# 1. Reset permissions to defaults and remove inherited permissions
Write-Host "Step 1: Resetting permissions and disabling inheritance..."
icacls $PemFilePath /reset /T /C /Q
if ($LASTEXITCODE -ne 0) {
    Write-Warning "Step 1a (icacls /reset) finished with exit code: $LASTEXITCODE. Check for errors."
}
icacls $PemFilePath /inheritance:r /T /C /Q
if ($LASTEXITCODE -ne 0) {
    Write-Warning "Step 1b (icacls /inheritance:r) finished with exit code: $LASTEXITCODE. Check for errors."
}


# 2. Remove permissions for common groups that often cause SSH issues
Write-Host "Step 2: Removing permissions for default groups (Administrators, SYSTEM, Users)..."
# These might not have explicit ACEs after reset/inheritance removal, so errors are possible but usually ignorable.
icacls $PemFilePath /remove "Administrators" /T /C /Q
icacls $PemFilePath /remove "SYSTEM" /T /C /Q
icacls $PemFilePath /remove "Users" /T /C /Q
# Authenticated Users is another common one from inheritance
icacls $PemFilePath /remove "Authenticated Users" /T /C /Q

# 3. Grant explicit Read access ONLY to the current user
#    SSH generally only needs Read (R) access to the private key.
Write-Host "Step 3: Granting Read (R) access explicitly and solely to '$userName'..."
# The syntax is /grant User:(Permission)
icacls $PemFilePath /grant "$userName`:(R)" /T /C /Q
if ($LASTEXITCODE -ne 0) {
    # This is the most critical step, flag potential issues
    Write-Warning "Step 3 (icacls /grant) finished with exit code: $LASTEXITCODE."
    Write-Warning "This might indicate a problem setting the necessary permission for '$userName'."
}

# --- Verification ---
Write-Host "Step 4: Verifying final permissions for '$PemFilePath'..."
Write-Host "--------------------------------------------------"
# Execute icacls and display its output
icacls $PemFilePath
Write-Host "--------------------------------------------------"

# Check if the grant command likely succeeded (exit code 0)
if ($LASTEXITCODE -eq 0) {
    Write-Host "Permissions adjustment complete." -ForegroundColor Green
    Write-Host "The file should now ONLY grant '$userName' Read access (or Full Control if Read failed and it fell back)."
    Write-Host "Please try connecting via SSH using '$PemFilePath'."
} else {
    Write-Error "Permissions adjustment may have failed. Please review the output above and the file permissions manually."
}


```