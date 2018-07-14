node {
    try{
        stage('build') {
            powershell(
            '[Net.ServicePointManager]::SecurityProtocol = "tls12"'

                'function EncodeBasicAuthToken'
                {
                    $Username = $ENV:GitPass.User
                    $Password = $ENV:GITPass.Password
                    $pair = "$($Username):$($Password)"
                    $encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($pair))
                    return "Basic $encodedCreds"
                }
                '
                
                'function Send-Status'
                {
                    param(
                        [String]$Action,
                        [String]$Repo,
                        [String]$PR,
                        [String]$PRHead,
                        [String]$JenkinsUrl)

                    $Headers = @{ Authorization = EncodeBasicAuthToken
                                  Accept = 'application/vnd.github.howard-the-duck-preview+json'}

                    switch ( $Action.ToUpper() )
                    {

                        'INITIALIZE' {
                            # Send the intail response so a user can intiate a build using the Details button in GitHub.
                            $Body = (@{ state = 'pending'
                                      target_url = $JenkinsUrl
                                      description = 'Click Details to Start build for PR-$PR'
                                      context = 'continuous-integration/BenTest'
                            })| ConvertTo-Json

                            Write-Host 'Sending initialization status for commit $PRHead to https://api.github.com/repos/$Repo/statuses/$PRHead'

                            $response = (curl -Uri 'https://api.github.com/repos/$Repo/statuses/$PRHead' -Body $Body -Method Post -Headers $Headers -UseBasicParsing )
                        }

                        'PENDING' {
                            # Updates the target_url with the running Jenkins Build URL.
                            $Body = (@{ state = 'pending'
                                      target_url = $JenkinsUrl
                                      description = 'Ben Test is running for PR-$PR...'
                                      context = 'continuous-integration/BenTest'
                            })| ConvertTo-Json

                            Write-Host 'Sending Pending status for commit $PRHead to https://api.github.com/repos/$Repo/statuses/$PRHead'
                            $response = (curl -Uri 'https://api.github.com/repos/$Repo/statuses/$PRHead' -Body $Body -Method Post -Headers $Headers -UseBasicParsing )

                        }
                        'SUCCESS' {
                            # Sends a Successfull status for the Commit.
                            $Body = (@{ state = 'success'
                                      target_url = $JenkinsUrl
                                      description = 'Ben Test Approved!'
                                      context = 'continuous-integration/BenTest'
                            }) | ConvertTo-Json

                            Write-Host 'Sending Success status for commit $PRHead to https://api.github.com/repos/$Repo/statuses/$PRHead'
                            $response = (curl -Uri 'https://api.github.com/repos/$Repo/statuses/$PRHead' -Body $Body -Method Post -Headers $Headers -UseBasicParsing )
                        }

                        'FAILURE' {
                            # Sends a failed status for the Commit.
                            $Body = (@{ state = 'failure'
                                      target_url = $JenkinsUrl
                                      description = 'This commit Failed Ben's Test!'
                                      context = 'continuous-integration/BenTest'
                            }) | ConvertTo-Json

                            Write-Host 'Sending Failed status for commit $PRHead to https://api.github.com/repos/$Repo/statuses/$PRHead'
                            $response = (curl -Uri 'https://api.github.com/repos/$Repo/statuses/$PRHead' -Body $Body -Method Post -Headers $Headers -UseBasicParsing )
                        }

                        Default {
                            Write-Error 'Not a valid parameter $Action'
                        }
                    }
                }
            
                $Repo = 'markmaxwell19/random-test'
                $ENV:PRNumber = 8

                # Get current building commit
                $PRHead = (Get-PR -Repo $Repo -PR $ENV:PRNumber).head.sha
                Write-Host 'The PR head is $PRHead'
                Send-Status -Action 'Initialize' -Repo $Repo -PR $ENV:PRNumber -PRHead $PRHead -JenkinsUrl 'http://localhost:8080/job/BenTestPOC/build?token=SecretToken'
            )
        }
    }
    catch (e)
    {
        powershell(Write-host 'Failed')
    }
}
