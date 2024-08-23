### Asynchronous Prework: **UDS 101**

**Objective:**  
By the end of this self-paced prework, participants will have deployed an Nginx server locally using Kubernetes and UDS CLI, gaining foundational experience that will prepare new engineers the more advanced **UDS 201 Live Workshop**.

---

**Estimated Duration:** 30-45 minutes

---

## Prework Agenda

### **1. Introduction**

- **Objective:** Familiarize yourself with the tools you'll be using: Kubernetes, UDS CLI, and Nginx.
- **Discussion Topics:**
  - Overview of Kubernetes and its role in modern software deployment.
  - Introduction to UDS CLI as a powerful deployment tool designed specifically for secure and air-gapped environments.
  - Explanation of Nginx as a lightweight web server used in this exercise.

**Important Context**:  
It is critical that all Unicorn Engineers have a deep understanding of our core offerings. Even if creating UDS packages and UDS bundles isn’t part of your day-to-day work, gaining this experience will help you better serve our Mission Heroes and deepen your understanding of the UDS ecosystem.

**Target Audience**:  
This prework is intended for new engineers in Product, Delivery, and Growth, as part of their onboarding process and preparation for the UDS 201 Live Workshop.

### **2. Setup Environment**

- **Objective:** Ensure you have the necessary tools installed and set up before proceeding.
- **Steps:**
  - **Install UDS CLI**:
    - Command: `brew tap defenseunicorns/tap && brew install uds`
  - **Ensure Docker is Running**: Verify Docker or Colima is running on your machine.
  - **Install K3d (if not already installed)**:
    - Command: `brew install k3d`

**Note**:  
Before diving into UDS Package and UDS Bundle creation, it’s important to understand what UDS is and the problem it solves. Complete the short, 10-minute "Intro to UDS" course, which all new hires are automatically enrolled in via Rippling. This foundational knowledge will make the hands-on portion of this prework more meaningful.

### **3. Create and Configure Nginx Zarf Package**

- **Objective:** Create a Zarf package for Nginx and configure it for deployment.
- **Steps:**
  - **Create Directory for Nginx Package**:
    - Command: `mkdir nginx-package && cd nginx-package`
  - **Create `zarf.yaml` File** with the following content:

    ```yaml
    kind: ZarfPackageConfig
    metadata:
      name: nginx
      version: 1.0.0

    components:
      - name: nginx
        required: true
        charts:
          - name: nginx
            version: 18.1.9
            namespace: nginx
            url: https://charts.bitnami.com/bitnami
            gitPath: charts/nginx
        images:
          - docker.io/bitnami/nginx:1.27.1-debian-12-r0
        actions:
          onDeploy:
            after:
              - wait:
                  cluster:
                    kind: deployment
                    name: nginx
                    namespace: nginx
                    condition: available
    ```

  - **Create Zarf Package**:
    - Command: `zarf package create --confirm`

**Additional Context**:  
Before diving into this exercise, it's beneficial to understand what makes a Zarf Package a UDS Package. Familiarize yourself with the UDS Core applications and how they integrate with Zarf by reviewing the available documentation [here](https://uds.defenseunicorns.com/docs/).

### **4. Create UDS Bundle for Nginx Deployment**

- **Objective:** Configure a UDS Bundle to deploy the Nginx package.
- **Steps:**
  - **Create UDS Bundle Configuration**:
    - Create `uds-bundle.yaml` in the same directory.
    - Content:

    ```yaml
    kind: UDSBundle
    metadata:
      name: nginx-bundle
      description: Bundle with k3d, Zarf init, UDS Core, and nginx.
      version: 1.0.0

    packages:
      - name: uds-k3d
        repository: ghcr.io/defenseunicorns/packages/uds-k3d
        ref: 0.8.0

      - name: init
        repository: oci://ghcr.io/zarf-dev/packages/init
        ref: v0.38.2

      - name: core
        repository: oci://ghcr.io/defenseunicorns/packages/uds/core
        ref: 0.25.2-upstream

      - name: nginx
        path: ./
        ref: 1.0.0
    ```

**Further Learning**:  
This guide is intended to fast-track creating a UDS Package and UDS Bundle. It’s recommended to explore the UDS repositories on GitHub to better understand the broader UDS ecosystem.

### **5. Deploy the UDS Bundle**

- **Objective:** Deploy the Nginx server on a local Kubernetes cluster using UDS CLI.
- **Steps:**
  - **Create the UDS Bundle**:
    - Command: `uds create --confirm`
  - **Deploy the UDS Bundle**:
    - Command: `uds deploy uds-bundle-nginx-bundle-arm64-1.0.0.tar.zst --confirm`

**Tip**:  
Deploying UDS Core is the gateway to leveraging UDS and easily creating bundles. For more details on deploying UDS Core, review the [UDS Core deployment guide](https://medium.com/defense-unicorns/from-zero-to-hero-kickstart-your-journey-with-uds-core-8409972c8bb9).

### **6. Interact with the Deployed Nginx Server**

- **Objective:** Verify the deployment and interact with the Nginx server.
- **Steps:**
  - **Verify Pods are Running**:
    - Command: `kubectl get pods -n nginx`
  - **Port-Forward to Access Nginx Locally**:
    - Command: `kubectl port-forward pod/nginx-<pod-name> 8080:80 -n nginx`
  - **Access Nginx in Browser**:
    - URL: `http://localhost:8080`
  - **Install k9s for Better Cluster Management**:
    - Command: `brew install derailed/k9s/k9s`

**Pro Tip**:  
As you become more familiar with UDS and Kubernetes, using tools like k9s will help you manage and monitor your clusters more effectively.

### **7. Review and Prepare for UDS 201 Live Workshop**

- **Objective:** Reflect on your experience and prepare for the upcoming live session.
- **Tasks:**
  - Take note of any challenges or questions that arose during the prework.
  - Review the UDS documentation and your deployment setup to solidify your understanding.
  - Prepare to discuss your experience and findings during the UDS 201 Live Workshop.

**Cohort Support**:  
As a new 🦄 Engineer, you are part of a cohort that can provide additional support and resources. Use your new hire Slack channel to share findings and ask questions.

---

**Resources:**

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Nginx Official Site](https://www.nginx.com/)
- [UDS CLI Documentation](https://github.com/defenseunicorns/uds-cli)
- [Intro to UDS Course](https://app.rippling.com/apps/RipplingLMS/employee/my-courses)

**Final Note**:  
This prework is designed to be self-paced and hands-on, with the goal of preparing you for more advanced tasks in the upcoming UDS 201 Live Workshop. Completing this exercise will provide a solid foundation for understanding and working within the UDS ecosystem.
