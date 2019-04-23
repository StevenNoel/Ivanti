#Must be ran from Appsense Server

#region Modules
#Imports the modules for Management Server and Personalization Server
if (Get-module -Name "Appsense.Management*"){} 
Else {Import-ApsInstanceModule -ProductName "Ivanti Management Server 2018.3 SP1"}

#If (Get-Module -Name "Ivanti Personalization Server*"){}
#Else {Import-ApsInstanceModule -ProductName "Ivanti Personalization Server 2018.3 SP2"}

If (Get-Module -Name "AppSense.ServerConfigurationPortal*"){}
Else {Import-Module AppSense.ServerConfigurationPortal.PowerShell}
#endregion Modules

############################### Start Functions ################################################

Function Get-DatabaseConnections
{
<#
.SYNOPSIS
  Get Ivanti Database Connections
.DESCRIPTION
  Get Ivanti Database Connections. Must be ran from Appsense Server. Must be ran as the Appsense 'Configuration Account' username.
.EXAMPLE
  $Connections = Get-DatabaseConnections
  Get-DatabaseConnections
#> 
    $DatabaseConnections = Get-ApsDatabaseConnection

    Write-Host "There are" $DatabaseConnections.Count "Connections" -ForegroundColor Yellow
    Foreach ($DBConnection in $DatabaseConnections)
    {
        Write-host "Database Connection Name:" $DBConnection.FriendlyName -ForegroundColor Yellow
        $DBConnection | Out-Host
        $DBConnection.ConfigurerCredential | Out-Host
        $DBConnection.ServiceCredential | Out-Host
    }

    return $DatabaseConnections
}

Function Set-DatabaseConnection
{
<#
.SYNOPSIS
  Set Ivanti Database Connection
.DESCRIPTION
  Set Ivanti Database Connection
.PARAMETER DatabaseServer
  Database Server Location
.PARAMETER DatabaseName
  The Database name
.PARAMETER DatabaseConnectionName
  New Database Connection Name you want to add
.PARAMETER RemoveDatabaseConnection
  The Database Connection you want to remove
.PARAMETER AddOnly
  Only Adds the New Database Connection, Doesn't perform the Remove actions
.PARAMETER RemoveOnly
  Only Removes the specified Database Connection, Doesn't perform the Add actions
.PARAMETER LogOnly
  Include ELM script hosts in return
.EXAMPLE
  Set-DatabaseConnection -DatabaseServer "DBServer\Instance" -DatabaseName "DBname" -Type Management -DatabaseConnectionName "ivanti-Management-test" -LogOnly -Verbose
  Set-DatabaseConnection -DatabaseServer "DBServer\Instance" -DatabaseName "DBname" -Type Management -DatabaseConnectionName "ivanti-Management-test" -Verbose
  Set-DatabaseConnection -DatabaseServer "DatabaseServer" -DatabaseName "Database Name" -Type Personalization -DatabaseConnectionName "ivanti-Pers-test" -AddOnly -Verbose
  Set-DatabaseConnection -DatabaseServer "DatabaseServer" -DatabaseName "Database Name" -Type Personalization -DatabaseConnectionName "ivanti-Pers-test" -RemoveDatabaseConnection "NEWPS-Database-Conn" -Verbose
#>
    Param(
            [Parameter(Mandatory=$True)][string]$DatabaseServer,
            [Parameter(Mandatory=$True)][string]$DatabaseName,
            [Parameter(Mandatory=$True)][string]$DatabaseConnectionName,
            [Parameter(Mandatory=$True)][ValidateSet('Management','Personalization')][string]$Type,
            [Parameter(Mandatory=$False)][string]$RemoveDatabaseConnection,
            [Parameter(Mandatory=$False)][switch]$AddOnly,
            [Parameter(Mandatory=$False)][switch]$RemoveOnly,
            [Parameter(Mandatory=$False)][switch]$LogOnly
         )
    Try
    {
        If ($LogOnly)
        {
            Write-Host "LOG ONLY MODE - No Changes will be made"
        }
    
        if ($Type -eq "Management")
        {
            Set-ApsInstance -InstanceId ((Get-ApsInstance | Where-Object {$_.ProductName -like "*Management*"}).InstanceID) -Verbose
        }
        If ($Type -eq "Personalization")
        {
            Set-ApsInstance -InstanceId ((Get-ApsInstance | Where-Object {$_.ProductName -like "*Person*"}).InstanceID) -Verbose
        }

        $DatabaseConnectionName = $DatabaseConnectionName + "-" + (Get-Date -Format yyy-MM-dd-HHmm)

        $CurrentDBConnection = Get-ApsDatabaseDetail
        If ($RemoveDatabaseConnection)
        {
            $CurrentDBConnection.DatabaseConnection = $RemoveDatabaseConnection
        }
        Write-Host "Working on Instance Connection:"
        $CurrentDBConnection
        
        Write-host "Adding Database Connection" $DatabaseConnectionName
            If (!($LogOnly))
            {

                    If (!($RemoveOnly))
                    {
                        Try
                        {
                            Stop-ApsServer -Verbose
                            Write-host "Creating Connection...:" $DatabaseConnectionName
                            Set-ApsServerDatabase -DatabaseServer $DatabaseServer -DatabaseName $DatabaseName -ConfigurerCredential (Get-Credential -Message "Enter *Configuration Account* Creds.  Ex: domain\username") -ServiceCredential (Get-Credential -Message "Enter *Service Account* Creds.  Ex: domain\username") -DatabaseConnection $DatabaseConnectionName -Verbose
                            Write-host "Created Connection Successfully"
                            Start-ApsServer -Verbose
                        }
                        Catch
                        {
                            Write-Host "Unable to Add Database Connection" $DatabaseConnectionName
                        }

                    }
                    If (!($AddOnly))
                    {
                        Try
                        {
                        Write-Host "Removing Connection...:" $CurrentDBConnection.DatabaseConnection
                        Remove-ApsDatabaseConnection -DatabaseConnection $CurrentDBConnection.DatabaseConnection -type $type -Verbose
                        Write-host "Removed Database Connection"
                        }
                        Catch
                        {
                            Write-Host "Unable to Remove Database Connection" $CurrentDBConnection.DatabaseConnection
                        }
                    }                
            }         
    }
    Catch
    {
        Write-host "Unable to Lookup Database Connection by Friendly Name:" $ConnectionFriendlyName
    }
    
    #return $SetDBConnection | Out-Null
}
