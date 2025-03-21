# Neofetch-For-Windows
Neofetch Script for windows
function Get-SystemInfo {
    # Microsoft logo (ASCII art) with blue color
    $microsoftLogo = @"
████  ████
████  ████

████  ████
████  ████
"@
    
    # Print the logo in blue
    Write-Host $microsoftLogo -ForegroundColor Blue

    # Print header with system info title in blue
    Write-Host "Neofetch for PowerShell" -ForegroundColor Blue
    Write-Host "----------------------------------------" -ForegroundColor Blue

    # Helper function for formatted output
    function Print-Info {
        param (
            [string]$label,
            [string]$value
        )
        Write-Host ("{0,-15}: {1}" -f $label, $value) -ForegroundColor Blue
    }

    # OS Information
    $os = Get-CimInstance -ClassName Win32_OperatingSystem
    Print-Info "OS" $os.Caption
    Print-Info "Hostname" $env:COMPUTERNAME

    # Uptime calculation
    $uptime = New-TimeSpan -Start $os.LastBootUpTime
    $formattedUptime = "{0}d {1}h {2}m {3}s" -f $uptime.Days, $uptime.Hours, $uptime.Minutes, $uptime.Seconds
    Print-Info "Uptime" $formattedUptime

    # CPU Information
    $cpus = Get-CimInstance -ClassName Win32_Processor
    foreach ($cpu in $cpus) {
        Print-Info "CPU" $cpu.Name
    }

    # GPU Information
    $gpus = Get-CimInstance -ClassName Win32_VideoController
    foreach ($gpu in $gpus) {
        Print-Info "GPU" $gpu.Name
        Print-Info "Resolution" $gpu.VideoModeDescription
    }

    # Memory Information
    $freeMemory = [math]::Round($os.FreePhysicalMemory / 1MB, 2)
    $totalMemory = [math]::Round($os.TotalVisibleMemorySize / 1MB, 2)
    Print-Info "Memory" "$freeMemory MB / $totalMemory MB"

    # Disk Usage (for C drive)
    $drive = Get-PSDrive -Name C
    if ($drive.Size -gt 0) {
        $diskUsage = [math]::Round(($drive.Used / $drive.Size) * 100, 2)
        Print-Info "Disk Usage" "$diskUsage%"
    } else {
        Print-Info "Disk Usage" "N/A (Drive size is 0)"
    }

    # PowerShell Version
    Print-Info "PowerShell" $PSVersionTable.PSVersion

    # Footer in blue
    Write-Host "----------------------------------------" -ForegroundColor Blue
}

# Automatically run the function to display system info, suppressing the command output
$null = Get-SystemInfo
