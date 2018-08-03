node {
    // We have to checkout to a workspace with a shorted path to get around windows path name restrictions.
    // This also has a side benefit of ensuring that the worker will only use the amount of disk space
    // needed for a single build. The downside is that we can never run concurrent builds on this worker
    // and the workspaces are lost after the next build runs
    ws("C:\\wsws") {
        notify('warning', "STARTED")

        try {
            stage ("Clean Workspace") {
                deleteDir()
            }
            
            stage ("Clone sources") {
                dir('work-server') {
                    env_map = checkout(
                        [
                            $class: 'GitSCM',
                            branches: scm.branches,
                            extensions: scm.extensions + [[$class: 'CloneOption', depth: 0, noTags: false, reference: '', shallow: false, timeout: 20]],
                            userRemoteConfigs: scm.userRemoteConfigs
                        ]
                    )
					Commit = env_map.GIT_COMMIT
					Branch = env_map.GIT_BRANCH
		powershell(script:"""copy-item \$ENV:WORKSPACE1\\work-server \$ENV:WORKSPACE\\ -recurse -force
				     copy-item \$ENV:WORKSPACE1\\RE_commons \$ENV:WORKSPACE\\ -recurse -force
				     copy-item \$ENV:WORKSPACE1\\work-gmail-extension \$ENV:WORKSPACE\\ -recurse -force""")
                }
            } 

            stage ("Build") {
		    withEnv(
                    [
                        "PATH=${env.PATH};C:\\Program Files\\Git\\usr\\bin",
			"ARTIFACT_PATH=${env.WORKSPACE}\\Artifacts",
                        "JOB_NAME=10.2.0 Server x64"
                    ]
                ) {
                    dir('work-server/iserver/app/buildInstaller') {
                        build_cmd = "" +
                            """ant resolve setResourceProperties CIBuild """ +
                            """-Dvs.path64.2017="C:\\Program Files (x86)\\Microsoft Visual Studio\\2017\\Community\\Common7\\IDE\\\\" """ +
                            """-DskipMobility2=1"""
                        echo "build_cmd=${build_cmd}"
                        bat build_cmd
                    }
                }
		    
                    dir('CI_Build') {					
						postToArtifactory("https://artifacts.imanage.com/artifactory/commons-set-local/DeployScripts/Alpha/" + Commit + "/server.zip", pwd() + "/server.zip")
					}

				if (Branch =~'^PR-*')
				{
					githubNotify account: 'markmaxwell19', context: 'continuous-integration/RESTAPI', credentialsId: 'github-accessToken', description: 'Click Details to manually start the test.', gitApiUrl: '', repo: 'random-test', sha: "${Commit}", status: 'PENDING', targetUrl: "http://localhost:8080/job/10.2.0%20CI%20Test/buildWithParameters?PRNumber=${Branch.replace('PR-','')}&CommitNumber=${Commit}"
					/*
					triggerRemoteJob auth: NoneAuth(), job: 'http://localhost:8080/job/10.2.0%20CI%20Test/', maxConn: 1, parameters: """PRNumber=${Branch.replace('PR-','')}
CommitNumber=${Commit}""", shouldNotFailBuild: true, token: 'Litigation11', blockBuildUntilComplete: false
*/
				}
				else
				{
					println "Triggering RESTAPI Test on Branch Merge"
					/*
					triggerRemoteJob auth: NoneAuth(), job: 'http://localhost:8080/job/10.2.0%20CI%20Test/', maxConn: 1, parameters: """PRNumber=${Branch}
CommitNumber=${Commit}""", shouldNotFailBuild: true, token: 'Litigation11', blockBuildUntilComplete: false
*/
				}
			}
			
            notify('good', 'SUCCESS')
			

            
        } catch (e) {
            currentBuild.result = "FAILED"
            notify('danger', 'FAILED')
            throw e
        }
    }
}

def notify(color, message) {
	println message
}

def postToArtifactory(source,dest)
{
	withCredentials([usernameColonPassword(credentialsId: 'artifactory_set_username_password', variable: 'ARTIFACTORYCREDS')]) {
		def stdout = powershell(script:'''
                    [Net.ServicePointManager]::SecurityProtocol = 'tls12'
                    
                    $encodedCreds = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($ENV:ARTIFACTORYCREDS))
                    $Token = "Basic $encodedCreds"
                    $Headers = @{ Authorization = $Token}

                    # Upload the file to Artifactory
                    Write-output ("Uploading file ''' + dest + ''' to ''' + source + '''")

                    #$response = (Invoke-WebRequest -Uri "''' + source + '''" -Method Put -InFile "''' + dest + '''" -Headers $Headers -UseBasicParsing)
                ''')
        println stdout
	}
}
