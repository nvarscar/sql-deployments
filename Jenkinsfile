def repo = 'C:\\Lab\\Repo'
def codePath = '.\\Code\\*'
def name = 'dbops.zip'
pipeline {
  agent {
    node {
      label 'windows'
    }
  }
  stages {
    stage('Deploy CI DB') {
      steps {
        powershell """
            \$password = 'dbatools.IO'
            \$sPassword = ConvertTo-SecureString \$password -AsPlainText -Force
            \$cred = New-Object pscredential 'sqladmin', \$sPassword
            Copy-DBOPackageArtifact -Repository '$repo' -Name '$name' -Destination .
            Invoke-DBOPackageCI -Path '$name' -ScriptPath '$codePath'
            \$dbName = '$name' + '_CI_' + [guid]::NewGuid().Guid
            try {
                New-DbaDatabase -SqlInstance localhost -Database \$dbName -SqlCredential \$cred
                Install-DBOPackage -Path '$name' -SqlInstance localhost -Database \$dbName -Credential \$cred
            } catch {
                throw \$_
            } finally {
                Remove-DbaDatabase -SqlInstance localhost -Database \$dbName -Confirm:0 -SqlCredential \$cred
            }
        """
      }
    }
  }
}