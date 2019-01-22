node {
    // We have to checkout to a workspace with a shorted path to get around windows path name restrictions.
    // This also has a side benefit of ensuring that the worker will only use the amount of disk space
    // needed for a single build. The downside is that we can never run concurrent builds on this worker
    // and the workspaces are lost after the next build runs
    ws("C:\\wsws") {
        try {
            stage ("Clean Workspace") {
                deleteDir()
            }
			stage("Initialize and Build") {
				parallel(
					RestCI: {
						stage('Initialize Rest-CI') {
							powershell label: '', script: 'start-sleep 10; write-host (get-date)'
							echo "Initialize Rest-CI"
						}
					},
					Builder: {
						stage('Clone sources') {
							echo "Cloning sources"
							powershell label: '', script: 'write-host (get-date)'
						}
					},
					failFast: true
				)
			}
			stage('Build') {
				echo "Building"
				powershell label: '', script: 'write-host (get-date)'
			}
			Stage("Test") {
				parallel(
					RestCI: {
						dir('') {
							try {
								echo "Running Rest CI Tests"
								powershell label: '', script: 'start-sleep 10; write-host (get-date)'
							} finally {
								echo "Captured Rest CI Tests"
							}
							
						}
					},
					PythonUT: {
						dir('') {
							try{
								echo "Running Python Unit Tests"
								powershell label: '', script: 'start-sleep 10; write-host (get-date)'
							} finally {
								echo "Captured Python Tests"
							}
						}
					}
				)
			}
        } catch (e) {
            currentBuild.result = "FAILED"
            throw e
        }
    }
}
