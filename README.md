# Kura QuikBuks: A Cloud-Based CI/CD Deployment on AWS

## PURPOSE

The purpose of this project was to deploy a Flask application called **_Kura QuikBuks_** using AWS services, specifically focusing on creating a CI/CD pipeline with Jenkins, AWS EC2, and Elastic Beanstalk. The project aimed to integrate cloud-based services and DevOps tools to streamline the deployment process, ensuring that changes made in the application's source code are automatically tested and validated.

## STEPS

1: **Configure Security Groups**
- **Why:** Kura QuikBuks uses EC2's accessible via SSH, Jenkins to create CI/CD Pipeline, Gunicorn as a WSGI server for the Flask application. If Ports 22, 8080 and 800 aren't open, nothing will work.
- **How**: I created a Security Group that ensured Ports 22, 8080 and 8000 were accepting inbound traffic, and allowed all outbound traffic through all ports to ensure any customer can be served. 

2. **Set Up the EC2 Instances:**
- **Why:** EC2 instances were needed to run Jenkins and host the application. They provided the computing resources required to manage and deploy the Flask app.
- **How:** I launched two EC2 instances – one for Jenkins and one as part of the Elastic Beanstalk environment.

3. **Install Jenkins:**
 - **Why:** Jenkins is crucial for automating the build and deployment process. It pulls the code from GitHub, tests the code's logic, and deploys the app.
 - **How:** Jenkins was installed on an EC2 instance, and the necessary plugins were added to enable GitHub integration, allowing us to link our GitHub repository with the source code Jenkins needed.
  
4. **Build via Jenkins:**
- **Why:** Jenkins is the backbone of my CI/CD pipeline. Jenkins was used to build the app, test the source code, validate that the logic's sound, and rebuild whenever changes were pushed to the GitHub repo.
- **How:** Created a Jenkins pipeline that pulls code from GitHub and confirms the app was ready for deployment, with the ability to rebuild and validate again whenever changes to the repo were made.

5. **Configure Elastic Beanstalk:**
- **Why:** Elastic Beanstalk simplifies application deployment by managing the underlying infrastructure, handling scaling, and providing a platform for deploying the Flask app.
- **How:** A Python environment was created in Elastic Beanstalk, where I uploaded the application's code packaged as a ZIP file.

6. **Understanding Gunicorn and NGINX:**
- **Why:** Gunicorn is a WSGI server for serving the Flask app, and NGINX is used as a reverse proxy to handle client requests and pass them to Gunicorn.
- **How:** The configs for Gunicorn and NGINX are provided in the source code, but it's important to know that Gunicorn is bound to `127.0.0.1:8000` and NGINX is configured to forward requests to Gunicorn.


## SYSTEM DESIGN DIAGRAM

[Link to the system design diagram for QuikBuks](https://github.com/tortiz7/C5_Deployment_Workload_1/blob/main/Documentation/Kura%20QuikBuks%20System%20Diagram.drawio.png)

## ISSUES/TROUBLESHOOTING

### Elastic Beanstalk Environment Misconfigurations:
  
   **Problem:** This turned out to be my biggest problem deploying the Kura Quik Buks retail app correctly. Elastic Beanstalk couldn't find the application module due to incorrect directory structure in the uploaded ZIP file. The source files were buried within a folder, which prevented Gunicorn from accessing them.
  
  **Solution:** Restructured the ZIP file to have the source files directly under the main folder.
 
 ### Jenkins, Python, and Dependencies Installation Script Issues:

  **Problem:** Initially, I encountered persistent issues with the installation script provided in the project assignment files. The script was supposed to install Jenkins, Python, and the necessary dependencies on the EC2 instance, but it consistently failed, leading to multiple instance terminations and re-creations.

  **Troubleshooting:**  After several attempts, it became clear that the script had some configuration errors and missing dependencies. I modified the script by ensuring that all necessary repositories were added, particularly for Python 3.7, which is deprecated and requires a specific repository (deadsnakes) to install correctly.

**Solution:**  By refining the installation script to include the correct repositories and ensuring all dependencies were specified, I was able to get Jenkins, Python, and the necessary libraries installed correctly. This allowed me to proceed with the project without further interruptions.

### Chasing Phantom Issues:

**VPC Security Settings:**
  - **Problem:** Thought the issue might be related to VPC security settings, suspecting that certain rules were blocking the necessary traffic.
  - **Solution:** The security group settings were correct, and the actual issue was unrelated to VPC settings. It was more about ensuring the application files were in the correct directory structure.

**PHP-FPM Configuration:**
  - **Problem:** While troubleshooting NGINX, I considered whether PHP-FPM might be required or misconfigured, despite the project being built with Flask.
  - **Solution:** PHP-FPM is for PHP applications, and since the project was Python-based, this was irrelevant. The focus needed to remain on Python-specific configurations.

**Environment Variables:**
  - **Problem:** I speculated that additional environment variables might be needed to point to the right directories or modules.
  - **Solution:** The existing environment variables were set correctly, and the problem lied elsewhere — in the directory structure of the uploaded ZIP file.

**NGINX Configuration Errors:**
  - **Problem:** I spent time adjusting NGINX configurations, believing that the 502 Bad Gateway errors were due to incorrect proxy settings or conflicting configurations.
  - **Solution:** The NGINX configuration was fine. The real issue was the misconfigured application structure in the uploaded ZIP file.

**Gunicorn Installation:**
  - **Problem:** Suspected that Gunicorn might not be correctly installed or configured in the Elastic Beanstalk environment.
  - **Solution:** Gunicorn was installed correctly, but it couldn’t locate the `application.py` module due to the incorrect directory structure.

  ### Lesson:  Focus on the basics first – directory structures, file paths, and service configurations.

## OPTIMIZATIONS

**True Jenkins Automation**
  - **AWS CLI:** The biggest roadblock for deployment was the nested source code folder in the .zip used to create the Elastic Beanstalk environment. This could have been avoided by deploying via command-line.
  - **Automated Jenkins Deployment:** With AWS CLI installed in the EC2 instance, the pipeline script could've been configured so Jenkins can automatically deploy the project after building and validating code.
  - **CI/CD Unleashed:** By automating deployment this way, Jenkins can redploy Kura QuikBuks everytime there are changes made to the source code in the GitHub Repo, ensuring a proper CI/CD pipeline.

**Benefits of Using Managed Services:**
   - **Simplified Management:** Managed services like Elastic Beanstalk reduce the need for manual infrastructure management, allowing you to focus on the application code.
   - **Scalability:** Automatically handles scaling based on traffic, which is crucial for retail banking applications that may experience variable load.
   - **Security:** AWS provides built-in security features and compliance with industry standards, easing the burden on development teams.

**Issues for Retail Banks:**
   - **Compliance and Data Privacy:** Retail banks have strict data privacy regulations. Managed services may not provide the level of control needed to ensure compliance with regulations like GDPR or PCI-DSS.
   - **Vendor Lock-In:** Using a managed service ties you to the provider’s ecosystem, which can be a risk if you need to migrate to another platform in the future.
   - **Mitigation:** Opt for hybrid models where sensitive data is handled on-premises, and less sensitive workloads are managed in the cloud.

**Disadvantages of Using Elastic Beanstalk:**
   - **Limited Customization:** Elastic Beanstalk abstracts a lot of the infrastructure details, which can limit your ability to customize configurations.
   - **Dependency on AWS:** Being tied to AWS’s ecosystem can be a disadvantage if you ever need to switch providers.
   - **Mitigation:** Use Elastic Beanstalk for initial deployments and scale, but consider transitioning to containerized environments like Kubernetes for more control.
 
## LESSONS LEARNED

- **EC2 and Elastic Beanstalk Integration:** Understanding how Elastic Beanstalk automates environment setups on EC2 instances helped streamline the deployment process, though it required careful attention to file structure and configurations.
- **Jenkins CI/CD Pipeline:** The automation provided by Jenkins significantly reduces time spent testing and implementing code changes and can streamline deployments, highlighting the importance of automation in modern development workflows.
- **Configuration Management:** Properly configuring services like NGINX and Gunicorn is critical to the smooth operation of a web application. Misconfigurations can lead to hard-to-trace errors that disrupt the deployment pipeline.

## DOCUMENTATION
(Link to Jenkins successful scan)[https://github.com/tortiz7/C5_Deployment_Workload_1/blob/main/Documentation/Jenkins%20Kura%20QuikBuks%20Successful%20Scan.png]

## CONCLUSION

Deploying the Kura QuikBuks project using AWS services provided a deep dive into the complexities and conveniences of cloud-based deployments. By integrating Jenkins for CI/CD, I ensured that the deployment process was efficient and reliable. Although I encountered numerous challenges, each was an opportunity to learn more about cloud infrastructure and deployment strategies. This project emphasized the importance of proper configuration, the challenges of working with legacy software versions, and the need for careful planning when using managed services. Looking forward, optimizing the deployment process and considering alternative deployment methods will be key to scaling and maintaining the application.


---
