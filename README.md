# Final-Project-Assessment-for-Scalefocus-Academy
Final Project Assessment for Scalefocus Academy
Prerequisites:
1. I have allready had Installed minikube, Helm and Jenkins
2. Created the repo with named : Final Project Assessment for Scalefocus Academy
Requirement for the Project Assessment:
1. Cloned all the charts with command git clone https://github.com/bitnami/charts.git in local directory called : final-assessment
2. Corrected the values.yaml the LoadBalancer to type: ClusterIP
3. Created  Jenkins pipeline that checks if wp namespace exists, if it doesn’t then it creates one. Checks if WordPress exists, if it doesn’t then it installs the chart.
  Here is my Jenkins pipeline: 
  
            pipeline {

            agent any
            //Set the KUBECONFIG environment variable. This ensures that the kubectl and helm commands executed by Jenkins use the correct configuration.
            environment {
              KUBECONFIG = '/Users/Daniel/.kube/config'
            }
            stages {
              stage('Verify') {
                steps {
                  script {
                    // Check if WordPress deployment exists
                    def deploymentExists = bat(script: 'kubectl get deployment final-project-wp-scalefocus-wordpress -n wp', returnStatus: true) == 0
                    if (deploymentExists) {
                      echo 'WordPress is already installed'
                    } else {
                      echo 'WordPress is not installed. Proceed with the deployment.'
                    }
                  }
                }
              }
              stage('Deploy') {
                steps {
                  script {
                    try {
                      // Check if the namespace exists
                      def namespaceExists = bat(script: 'kubectl get namespace wp', returnStatus: true) == 0
                      if (!namespaceExists) {
                        // Create the namespace
                        bat 'kubectl create namespace wp'
                      }
                      // Deploy the application using Helm
                      bat 'helm upgrade --install final-project-wp-scalefocus /Users/Daniel/desktop/final-assessment/charts/bitnami/wordpress -n wp -f /Users/Daniel/desktop/final-assessment/charts/bitnami/wordpress/values.yaml'
                      // Forward the port in the foreground

                      bat 'kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 80:80'

                    } catch (Exception e) {
                      // Handle any deployment errors
                      echo "Deployment error: ${e.getMessage()}"
                      error "Deployment failed: ${e.getMessage()}"
                    }
                  }
                }
              }
            }
          }



4. Named the deployment : final-project-wp-scalefocus.
5. Deployed the helm chart using the Jenkins pipeline. Here is the output:

            Started by user Daniel Jovevski
            [Pipeline] Start of Pipeline
            [Pipeline] node
            Running on Jenkins in C:\ProgramData\Jenkins\.jenkins\workspace\final-project-wp-scalefocus
            [Pipeline] {
            [Pipeline] withEnv
            [Pipeline] {
            [Pipeline] stage
            [Pipeline] { (Verify)
            [Pipeline] script
            [Pipeline] {
            [Pipeline] bat

            C:\ProgramData\Jenkins\.jenkins\workspace\final-project-wp-scalefocus>kubectl get deployment final-project-wp-scalefocus -n wp 
            Error from server (NotFound): deployments.apps "final-project-wp-scalefocus" not found
            [Pipeline] echo
            WordPress is not installed. Proceed with the deployment.
            [Pipeline] }
            [Pipeline] // script
            [Pipeline] }
            [Pipeline] // stage
            [Pipeline] stage
            [Pipeline] { (Deploy)
            [Pipeline] script
            [Pipeline] {
            [Pipeline] bat

            C:\ProgramData\Jenkins\.jenkins\workspace\final-project-wp-scalefocus>kubectl get namespace wp 
            NAME   STATUS   AGE
            wp     Active   3h22m
            [Pipeline] bat

            C:\ProgramData\Jenkins\.jenkins\workspace\final-project-wp-scalefocus>helm upgrade --install final-project-wp-scalefocus /Users/Daniel/desktop/final-assessment/charts/bitnami/wordpress -n wp -f /Users/Daniel/desktop/final-assessment/charts/bitnami/wordpress/values.yaml 
            Release "final-project-wp-scalefocus" has been upgraded. Happy Helming!
            NAME: final-project-wp-scalefocus
            LAST DEPLOYED: Mon May 15 19:20:57 2023
            NAMESPACE: wp
            STATUS: deployed
            REVISION: 23
            TEST SUITE: None
            NOTES:
            CHART NAME: wordpress
            CHART VERSION: 16.1.2
            APP VERSION: 6.2.0

            ** Please be patient while the chart is being deployed **

            Your WordPress site can be accessed through the following DNS name from within your cluster:

                final-project-wp-scalefocus-wordpress.wp.svc.cluster.local (port 80)

            To access your WordPress site from outside the cluster follow the steps below:

            1. Get the WordPress URL by running these commands:

               kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 80:80 &
               echo "WordPress URL: http://127.0.0.1//"
               echo "WordPress Admin URL: http://127.0.0.1//admin"

            2. Open a browser and access WordPress using the obtained URL.

            3. Login with the following credentials below to see your blog:

              echo Username: user
              echo Password: $(kubectl get secret --namespace wp final-project-wp-scalefocus-wordpress -o jsonpath="{.data.wordpress-password}" | base64 -d)
            [Pipeline] bat

            C:\ProgramData\Jenkins\.jenkins\workspace\final-project-wp-scalefocus>kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 80:80 
            Forwarding from 127.0.0.1:80 -> 8080
            Handling connection for 80
            Handling connection for 80
            Handling connection for 80
            Handling connection for 80
            Handling connection for 80
            Handling connection for 80
            Handling connection for 80
                   
                   
6. When i click and load the home page of the WordPress the homepage is shown : 
![image](https://github.com/Daniel-AnTra/Final-Project-Assessment-for-Scalefocus-Academy/assets/121831389/4d73e1c4-393d-429d-bbcd-53ea19d89ab3)

   Also for the admin panel : 
![image](https://github.com/Daniel-AnTra/Final-Project-Assessment-for-Scalefocus-Academy/assets/121831389/d6e763e9-ede7-4e5c-aaae-1f08d4481705)
7. Explained all the steps for the project in this README ! 


I hope it is okay. 


