pipeline {
        agent {
            label "APT-Dev"
        }
	environment{ 
		package_name="scanmanager"
		gitlabSourceBranch="/refs/tags/d-3.0.0"
		package_version="${gitlabSourceBranch.substring(12)}-ubuntu6"
		tag_version="${gitlabSourceBranch.substring(10)}"
		package_architecture="amd64"
		package_folder="${package_name}_${package_version}-${package_architecture}"
		final_folder="/home/ace-deployment-user/apt/${package_folder}"
		debian_folder="${final_folder}/DEBIAN"
	}
    stages {
        stage('Scan Manager Checkout') {
            
            steps {
                echo "Package name : ${package_folder}"
					script {
						def data = "$env.tag_version"
						if(data.startsWith("d")) {
							sh 'echo "This tag is for Development"'
						} else {
							unstable "This tag is not for Development"
						}
					 }
				
				cleanWs()
				checkout([$class: 'GitSCM', branches: [[name: '${gitlabSourceBranch}']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-user-gitlab-smamidala-token', url: 'https://gitlab.controlcase.com/innovations/debian-packages/scan-manager.git']]])
            }
        }
		stage('Ace Agent Checkout') {
			when { 
					expression 
						{ env.tag_version.startsWith("d")} 
				}
            steps {
				checkout([$class: 'GitSCM', branches: [[name: '${gitlabSourceBranch}']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'ace-agent']],  submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-user-gitlab-smamidala-token', url: 'https://gitlab.controlcase.com/innovations/ace-agent.git']]])
            }
        }
		stage('Obfuscation') {
			when { 
					expression 
						{ env.tag_version.startsWith("d")} 
				}
            steps {
				dir('ace-agent') {
					sh '''mkdir ../obfuscated
					rubyencoder -r -b- -o ../obfuscated --ruby 2.5 --rails *.rb
					'''
				}
				sh 'cp -r obfuscated/* ace-agent/'
            }
        }
    }
}
