# Programas
Repositório de programas para diagnóstico e manutenção.

## Ansible
Estamos implantando o uso do Ansible para controle de um conjunto de máquinas, numa rede local. Para isso, usaremos o [Ansible](https://docs.ansible.com/).  

### Configuranção Host Windows
Para configuração do Ansible nos hosts Windows, utilizamos as seguintes etapas, parte descrita na [documentação oficial](https://docs.ansible.com/ansible/latest/os_guide/windows_winrm.html#windows-winrm):  
Copie o seguinte snippet no PowerShell com permissões de administrador:
```bash
Write-Host "Iniciando a instalação do Python e configuração do WinRM..."

# Enables the WinRM service and sets up the HTTP listener
Enable-PSRemoting -Force

# Opens port 5985 for all profiles
$firewallParams = @{
    Action      = 'Allow'
    Description = 'Inbound rule for Windows Remote Management via WS-Management. [TCP 5985]'
    Direction   = 'Inbound'
    DisplayName = 'Windows Remote Management (HTTP-In)'
    LocalPort   = 5985
    Profile     = 'Any'
    Protocol    = 'TCP'
}
New-NetFirewallRule @firewallParams

# Allows local user accounts to be used with WinRM
# This can be ignored if using domain accounts
$tokenFilterParams = @{
    Path         = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
    Name         = 'LocalAccountTokenFilterPolicy'
    Value        = 1
    PropertyType = 'DWORD'
    Force        = $true
}
New-ItemProperty @tokenFilterParams
```

Em seguinda, o seguinte comando:
```bash
Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
        winrm set winrm/config/service '@{AllowUnencrypted="true"}'
```

Para verificar se tudo foi configurado, utilize:
```bash
winrm enumerate winrm/config/Listener
```

