node {
    try{
        stage('build') {
            powershell("""
                [Net.ServicePointManager]::SecurityProtocol = 'tls12'
                
                function EncodeBasicAuthToken
                {
                    $Username = $ENV:GitPass.User
                    $Password = $ENV:GITPass.Password
                    $pair = '$Username:$Password'
                    $encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($pair))
                    return 'Basic $encodedCreds'
                }
               
                Write-Host EncodeBasicAuthToken
            """)
        }
    }
    catch (e)
    {
        powershell("""
        
        Write-host 'Failed'
        """)
    }
}
