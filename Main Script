Configuration BrouwerijMoos 

{


    param(

    

        [parameter(Mandatory = $true)]
        [pscredential]$domainCred,

        [parameter(Mandatory = $true)]
        [pscredential]$safemodeAdministratorCred
         


    )

    Import-DscResource -ModuleName xActiveDirectory
    Import-DscResource –ModuleName PSDesiredStateConfiguration
    Import-DscResource -ModuleName xNetworking
    Import-DscResource -ModuleName xComputerManagement
    Import-DscResource -ModuleName xdhcpserver
    Import-DscResource -ModuleName xSmbShare 


    Node $AllNodes.NodeName
    {
        LocalConfigurationManager {
            ActionAfterReboot  = 'ContinueConfiguration'
            ConfigurationMode  = 'ApplyOnly'
            RebootNodeIfNeeded = $true
        }

        xIPAddress NewIPAddress
        {
            IPAddress      = "10.25.0.50/24"
            InterfaceAlias = 'Ethernet0'
            AddressFamily  = "IPV4"
           
        }

        xDefaultGatewayAddress DefaultGateway
        {
            Address        = "10.25.0.1"
            InterfaceAlias = 'Ethernet0'
            AddressFamily  = "IPV4"
        }

         WindowsFeature DNS { 
            Ensure = "Present" 
            Name   = "DNS"
           
        }

        xDnsServerAddress DnsServerAddress 
        { 
            Address        = '127.0.0.1' 
            InterfaceAlias = 'Ethernet0'
            AddressFamily  = 'IPv4'
            DependsOn      = "[WindowsFeature]DNS"
        }

        WindowsFeature DNSTools {
            Ensure = "Present"
            Name = 'RSAT-DNS-Server'
            DependsOn = '[WindowsFeature]DNS'
        }

     
        WindowsFeature DHCP {
            DependsOn            = '[xIPAddress]NewIpAddress'
            Name                 = 'DHCP'
            Ensure               = 'PRESENT'
            IncludeAllSubFeature = $true                                                                                                                              
     
        }


        WindowsFeature DHCPTools
        {
            Ensure = "Present"
            Name = 'RSAT-DHCP'
            DependsOn = '[WindowsFeature]DHCP'
        }


        xDhcpServerScope Scope
        {
            ScopeID = "10.25.0.0"
            IPStartRange = "10.25.0.10"
            IPEndRange = "10.25.0.40"
	    Ensure = "Present"
            Name = "scope"
            SubnetMask = "255.255.255.0"
            State = "Active"         
            LeaseDuration = "8:00:00"
            DependsOn = "[WindowsFeature]DHCP"
        
        }
        xDhcpServerOption ServerOpt
        {
            ScopeID = "10.25.0.0"
            Router = "10.25.0.1"
            DnsServerIPAddress = "10.25.0.50"
            DnsDomain = "BrouwerijMoos.local"
            AddressFamily = "IPv4"            
            Ensure = "Present"
            DependsOn = "[xDhcpServerScope]Scope"
        }
      

        WindowsFeature AD-Domain-Services {

            Ensure    = "Present"
            Name      = "AD-Domain-Services"
            DependsOn = "[File]ADFiles"
        }

        WindowsFeature RSAT-DNS-Server {
            Ensure    = "Present"
            Name      = "RSAT-DNS-Server"
            DependsOn = "[WindowsFeature]DNS"
        }

        File ADFiles {
            DestinationPath = 'C:\NTDS'
            Type            = 'Directory'
            Ensure          = 'Present'
        }
        WindowsFeature RSAT-AD-Tools {
            Name      = 'RSAT-AD-Tools'
            Ensure    = 'Present'
            DependsOn = "[WindowsFeature]AD-Domain-Services"
        }

        WindowsFeature RSAT-ADDS {
            Ensure    = "Present"
            Name      = "RSAT-ADDS"
            DependsOn = "[WindowsFeature]AD-Domain-Services"
        }


        WindowsFeature RSAT-ADDS-Tools {   
            Name      = 'RSAT-ADDS-Tools'
            Ensure    = 'Present'
            DependsOn = "[WindowsFeature]RSAT-ADDS"
        }
     
        WindowsFeature ADDSInstall {
            Ensure = "Present"
            Name   = "AD-Domain-Services"
        }

        WindowsFeature ADDSTools {
            Ensure = 'Present'
            Name   = 'RSAT-ADDS'
        }

        WindowsFeature RSAT-AD-AdminCenter {
            Name      = 'RSAT-AD-AdminCenter'
            Ensure    = 'Present'
            DependsOn = "[WindowsFeature]AD-Domain-Services"
        }

        WindowsFeature IIS {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        WindowsFeature IISConsole {
            Ensure    = 'Present'
            Name      = 'Web-Mgmt-Console'
            DependsOn = '[WindowsFeature]IIS'
        }

        WindowsFeature IISScriptingTools {
            Ensure    = 'Present'
            Name      = 'Web-Scripting-Tools'
            DependsOn = '[WindowsFeature]IIS'
        }

        File Indexfile {
            Ensure          = 'Present'
            Type            = 'file'
            DestinationPath = "C:\inetpub\wwwroot\index.html"
            Contents        = "<html>
            <header><title>Welkom</title></header>
                <body>
                        Welkom op mijn website! Ik ben Ward Van Overwalle.
                </body>
            </html>"
        }

        xFirewall firewall
        {
            Name        = "firewall"
            Ensure      = "Present"
            Direction   = "inbound"
            Description = "allow HTTP traffic for Internet Information Services (IIS) [TCP 80]"
            Profile     = "Domain"
            Protocol    = "TCP"
            LocalPort   = ("80")
            Action      = "Allow"
            Enabled     = "True"
        }
        File Share {
            Ensure          = "present"
            DestinationPath = "c:\share"
            Type            = "Directory"
        }
        
        File HR {
            Ensure          = "present"
            DestinationPath = "c:\share\HR"
            Type            = "Directory"
            
        }

        File Productie {
            Ensure          = "present"
            DestinationPath = "c:\share\Productie"
            Type            = "Directory"
            
        }

        File Marketing {
            Ensure          = "present"
            DestinationPath = "c:\share\Marketing"
            Type            = "Directory"
            
        }
 
        File Logistiek {
            Ensure          = "present"
            DestinationPath = "c:\share\Logistiek"
            Type            = "Directory"
            
        }

        File Onderzoek {
            Ensure          = "present"
            DestinationPath = "c:\share\Onderzoek"
            Type            = "Directory"
            
        }

        xSmbShare HR 
        { 
            Ensure       = "Present"  
            Name         = "HR" 
            Path         = "C:\share\HR"                    
        } 

        xSmbShare Productie 
        { 
            Ensure       = "Present"  
            Name         = "Productie" 
            Path         = "C:\share\Productie"  
        } 

        xSmbShare Marketing
        { 
            Ensure       = "Present"  
            Name         = "Marketing" 
            Path         = "C:\share\Marketing"      
        } 

        xSmbShare Logistiek 
        { 
            Ensure       = "Present"  
            Name         = "Logistiek" 
            Path         = "C:\share\Logistiek"     
        } 
        xSmbShare Onderzoek
        { 
            Ensure       = "Present"  
            Name         = "Onderzoek" 
            Path         = "C:\share\Onderzoek"       
        } 

        xADDomain FirstDC
        {
            DomainName                    = "BrouwerijMoos.local"
            DomainNETBIOSName             = "BrouwerijMOOS"
            DomainAdministratorCredential = $domainCred
            SafemodeAdministratorPassword = $safemodeAdministratorCred
            DatabasePath                  = 'C:\NTDS'         
            LogPath                       = 'C:\NTDS' 
            DependsOn                     = "[WindowsFeature]ADDSInstall"
        }

     

    }

}

$ADConfig = @{
    AllNodes = @(
        @{
            NodeName                    = "localhost"
            Role                        = "Primary DC"
            DomainName                  = "BrouwerijMoos.local"
            RetryCount                  = 20
            RetryIntervalSec            = 30
            PsDscAllowPlainTextPassword = $true
            PSDscAllowDomainUser        = $true
        }
    )
}

BrouwerijMoos -ConfigurationData $ADConfig `
    -safemodeAdministratorCred (Get-Credential -UserName '(Administrator)'  `
        -Message "New Domain Safe Mode Administrator Password") `
    -domainCred (Get-Credential -UserName Bmoos.local\administrator `
        -Message "New Domain Admin Credential") `
    
Set-DscLocalConfigurationManager -Path .\BrouwerijMoos -Verbose -Force
