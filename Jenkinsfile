/*
 * This is a vanilla Jenkins pipeline that relies on the Jenkins kubernetes plugin to dynamically provision agents for
 * the build containers.
 *
 * The individual containers are defined in the `jenkins-pod-template.yaml` and the containers are referenced by name
 * in the `container()` blocks. The underlying pod definition expects certain kube Secrets and ConfigMap objects to
 * have been created in order for the Pod to run. See `jenkins-pod-template.yaml` for more information.
 *
 * The cloudName variable is set dynamically based on the existance/value of env.CLOUD_NAME which allows this pipeline
 * to run in both Kubernetes and OpenShift environments.
 */

def buildAgentName(String jobName, String buildNumber) {
    if (jobName.length() > 40) {
        jobName = jobName.substring(0, 40);
    }

    return "agent.${jobName}${buildNumber}".replace('_', '-').replace('/', '-').replace('-.', '.');
}

def buildLabel = buildAgentName(env.JOB_NAME, env.BUILD_NUMBER);
def workingDir = "/var/jenkins/agent"
podTemplate(
   label: buildLabel,
   yaml: """
apiVersion: v1
kind: Pod
spec:
  serviceAccountName: jenkins
  containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
"""
) {
    node(buildLabel) {
        container(name: 'nginx', shell: '/bin/bash') {
            checkout scm
            stage('Setup') {
                sh '''ls -ltr
                '''
            }
            
        }
        
    }
}

