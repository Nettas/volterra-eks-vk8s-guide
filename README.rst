volterra-eks-vk8s-lab
==================================================

.. contents:: Table of Contents

Pre-Requisites
###############

- AWS Account
- aws cli
- terraform
- kubectl
- volterra account


.. figure:: _figures/overview.png

Lab Environment Setup  
############################### 

1. AWS CLI
*************************** 
Open IAM => Users

.. figure:: _figures/aws_cli_config_1.png

.. figure:: _figures/aws_cli_config_1_2.png

.. figure:: _figures/aws_cli_config_1_3.png

.. figure:: _figures/aws_cli_config_1_4.png

.. figure:: _figures/aws_cli_config_1_5.png

.. figure:: _figures/aws_cli_config_2.png

.. figure:: _figures/aws_cli_config_3.png

.. figure:: _figures/aws_cli_config_4.png

.. figure:: _figures/aws_cli_config_5.png

.. figure:: _figures/aws_cli_config_6.png

.. figure:: _figures/aws_cli_config_7.png

.. figure:: _figures/aws_cli_config_8.png

.. figure:: _figures/aws_cli_config_9.png

deploy kubectl to AWS

cd terraform
terraform init
terraform plan
terraform apply

.. figure:: _figures/eks_setup_1.png

.. figure:: _figures/eks_setup_2.png

.. figure:: _figures/eks_setup_3.png

.. figure:: _figures/eks_setup_4.png

.. figure:: _figures/eks_setup_5.png

Connect EKS Cluster
##################### 

In order to connect EKS cluster, we'll follow a few steps. First, we'll need to generate a site token, which is used among a few other things to deploy and configure a K8s cluster as a Volterra Site. Then we'll update the manifest with the generated token, and, finally, we'll deploy it.

1. Create token
***************

`a)` Log in the VoltConsole and go to the **System** namespace.  Then navigate to **Site Management** in the configuration menu, and select **Site Tokens** from the options pane.

.. figure:: _figures/connect_eks_cluster_1.png

`b)` Click **Add site token** to open the form and create a new token.

.. figure:: _figures/connect_eks_cluster_2.png

`c)` Then enter the site name. Description field is optional. Click **Add site token** button at the bottom of the form. 

.. figure:: _figures/connect_eks_cluster_3.png

`d)` Copy the token UID to use it for the manifest file in the next step.

.. figure:: _figures/connect_eks_cluster_4.png

2. Update manifest
*******************

Open the kubernetes deployment file located at **./k8s-deployments/volterra-k8s-manifest.yaml**. Paste the token generated in the previous step and save the file. The edited manifest will later be applied to spawn a Volterra Mesh on the EKS cluster. The original manifest template file can be found here:  `Manifest Template <https://gitlab.com/volterra.io/volterra-ce/-/blob/master/k8s/ce_k8s.yml>`_ 

.. figure:: _figures/connect_eks_cluster_5.png

3. Deploy manifest
*******************

Go to the **./k8s-deployments** directory, open the console and run the following command: **kubectl apply –f volterra-k8s-manifest.yaml**. This deploys the site using the created manifest file.

.. figure:: _figures/connect_eks_cluster_6.png

4. Accept registration
*******************

The Site we just configured will show up as a new registration request in the VoltConsole. We now need to approve the registration request for the site.

`a)` Go back to the VoltConsole, the **System** tab. Navigate to the **Site Management** menu option to accept the pending registration. Select **Registrations** from the options pane. You will see your site in the displayed list. 

.. figure:: _figures/connect_eks_cluster_7.png

`b)` Click the tick to load the **Registration Acceptance** form.

.. figure:: _figures/connect_eks_cluster_8.png

`c)` Click the **Save and Exit** button to save the registration.

.. figure:: _figures/connect_eks_cluster_9.png

5. Check status
*******************

We have now configured our Site, so let's see its status, including health score. Go to the **Site List** tab and you’ll see the dashboard of your site. In the screenshot below, we can see that the site is up and running, with 100% health score. 

.. figure:: _figures/connect_eks_cluster_10.png

**Note**: It may take a few minutes for the health and connectivity status to get updated in the portal.

Create VK8S Cluster
##################### 

Volterra provides mechanism to easily deploy applications using vK8s across Volterra global network and make them available closer to users. Virtual Kubernetes (vK8s) clusters are fully-functional Kubernetes deployments that can span multiple geographic regions, clouds, and even on-prem environments. Let's now follow a few steps below to create a vK8s object in VoltConsole, associate with a virtual site that groups Volterra sites, download kubeconfig of the created vK8s and test connectivity.

1. Create cluster
*******************

`a)` Select **Applications** tab and then navigate to **Virtual K8s** from the configuration menu. Click **Add virtual K8s** to create a vK8s object.

.. figure:: _figures/create_vk8s_1.png

`b)` Let's now give the vK8s a name and then move on to **Select Vsite Ref**: the virtual-site reference of locations on the Volterra network where vK8s will be instantiated. We will use the default virtual-site for our vK8s.

.. figure:: _figures/create_vk8s_2.png

`c)` Check the box just next to **ves-io-all-res** to associate the virtual site that selects all Volterra network cloud sites, and click **Select Vsite Ref**.

.. figure:: _figures/create_vk8s_3.png

`d)` Continue to apply the virtual site to the vK8s configuration. Click **Save and Exit** to complete creating the vK8s clusters in all Volterra Regional Edge (RE) sites.

.. figure:: _figures/create_vk8s_4.png

The process of creating a vK8s cluster takes just a minute, and after that you will be all set to deploy and distribute app workloads onto this new infrastructure.

2. Download Kubeconfig
**********************

We will now need a kubeconfig file for our cluster. Kubeconfig stores information about clusters, users, namespaces, and authentication mechanisms. We will download the Kubeconfig entering the certificate expiry date when prompted. 

`a)` Open the dropdown menu by clicking three dots and start downloading Kubeconfig. 

.. figure:: _figures/create_vk8s_5.png

`b)` Open the calendar and select the expiry date. 

.. figure:: _figures/create_vk8s_6.png

`c)` Click **Download credential** to start the download.

.. figure:: _figures/create_vk8s_7.png

`d)` As you can see, Kubeconfig is downloaded. 

.. figure:: _figures/create_vk8s_8.png

`e)` Copy the downloaded Kubeconfig into the **k8s-deployments** folder.

.. figure:: _figures/create_vk8s_9.png

3. Check connection
**********************

Open CLI, and run the following command **kubectl --kubeconfig ./ves_default_vk8s.yaml cluster-info** to test if the created vK8s cluster is connected. If it's successfully accomplished, the output will show that it's running at Volterra.  

.. figure:: _figures/create_vk8s_10.png

Deploy resources to Volterra Edge
##################### 

After vK8s cluster has been created and tested, we can deploy our app's resources to Volterra Edge. We are going to locate frontend and nearest-store-backend in Volterra Edge. 

We'll create internal TCP and public HTTP load balancers, connecting Volterra with EKS cluster (with app's backend), and Volterra with the internet, respectively. Then we will test if the resources are successfully deployed to Volterra Edge and available. 

1. Deploy resources
**********************

Using Kubeconfig, we will now deploy our app to Volterra Edge moving there its frontend and nearest-store-backend. Open CLI and run the following command: **kubectl --kubeconfig ./ves_default_vk8s.yaml apply -f vk8s-deployment.yaml**. The output will show the services created. 

.. figure:: _figures/create_vk8s_11.png

2. Create internal load balancer
********************************

Let's now create an internal TCP load balancer to connect Volterra with k8s cluster (where the app's backend is), then add and configure an origin pool. Origin pools consist of endpoints and clusters, as well as routes and advertise policies that are required to make the application available to the internet. 

`a)` In the **Application** tab, navigate to **Load Balancers** and then select **TCP Load Balancers** in the options. Then click **Add TCP Load Balancer** to open the load balancer creation form.

.. figure:: _figures/tcplb_mysql_1.png

`b)` Enter a name for the TCP load balancer in the Metadata section, and domain that will be matched to this balancer. A domain can be delegated to Volterra, so that Domain Name Service (DNS) entries can be created quickly in order to deploy and route traffic to our workload within seconds. 

Then fill in listen port **3306** for the TCP proxy, and move on to creating origin pool that will be used for this load balancer by clicking **Configure** origin pools.

.. figure:: _figures/tcplb_mysql_2.png

`c)` The origin pools are a mechanism to configure a set of endpoints grouped together into a resource pool that is used in the load balancer configuration. 

Let's create a new Origin Pool, which will be used in our load balancer by clicking **Add item** .

.. figure:: _figures/tcplb_mysql_3.png

`d)` Click **Create new origin pool** to open the origin pool creation form. 

.. figure:: _figures/tcplb_mysql_4.png

`e)` Enter a unique name for the origin pool, and then select **K8s Service Name of Origin Server on given Sites** as the type of origin server. Note that we will need to indicate the Origin Server **service name**, which follows the format of **servicename.namespace**. For this flow, let's specify **buytime-database.default**. 

After that select site reference to site object **eks-cluster**. This specifies where the origin server is located. 

Select **Outside Network** on the site and enter the port **3306** where endpoint service will be available. Click **Continue** to move on.

.. figure:: _figures/tcplb_mysql_5.png

`f)` Click **Apply** to apply the configuration of origin pool to the load balancer. This will return to the load balancer configuration form.

.. figure:: _figures/tcplb_mysql_6.png

`g)` Let's configure the method to advertise VIP. Select **Advertise Custom** on specific sites which will advertise the VIP on specific sites, not on public network with default VIP. Then click **Configure**. 

.. figure:: _figures/tcplb_mysql_7.png

`h)` Select **Virtual Site** to advertise load balancer on a virtual site with the given network. Then select **vK8s Service Network** as network type to be used on site and move on to selecting reference to virtual site object - **shared/ves-io-all-res** covering all regional edge sites across Volterra ADN.  

**Apply** custom advertise VIP configuration.

.. figure:: _figures/tcplb_mysql_8.png

`i)` Finish creating the load balancer clicking **Save and Exit**.

.. figure:: _figures/tcplb_mysql_9.png

Great! The internal TCP load balancer is now configured and created, and Volterra is connected with our EKS cluster with app's backend. Let's move on to creating public load balancer. 

3. Create public load balancer
******************************

We will use Volterra HTTP Load Balancer as a Reverse Proxy and to route traffic to resources located on Volterra vk8s and EKS based on the URI prefix. Let's follow the steps below to create an origin pool and load balancer for our app.


`a)` In the **Application** tab, navigate to **Load Balancers** and then select **HTTP Load Balancers** in the options. Then click **Add HTTP Load Balancer** to open the load balancer creation form.

.. figure:: _figures/httplb_1.png

`b)` First, enter the load balancer name. Then provide a domain name for our workload: a domain can be delegated to Volterra, so that Domain Name Service (DNS) entries can be created quickly in order to deploy and route traffic to our workload within seconds. Let’s use **buytime.demo.f5lab** for this flow. Finally, move on to creating an origin pool that will be used for this load balancer by clicking **Configure**.

.. figure:: _figures/httplb_2.png

`c)`

.. figure:: _figures/httplb_2_1.png

`d)`

.. figure:: _figures/httplb_3.png

`e)`

.. figure:: _figures/httplb_4.png

`f)`

.. figure:: _figures/httplb_5.png

`g)`

.. figure:: _figures/httplb_6.png

`h)`

.. figure:: _figures/httplb_7.png

`i)`

.. figure:: _figures/httplb_8.png

`j)`

.. figure:: _figures/httplb_9.png

`k)`

.. figure:: _figures/httplb_10.png

`l)`

.. figure:: _figures/httplb_11.png

`m)`

.. figure:: _figures/httplb_12.png

`n)`

.. figure:: _figures/httplb_13.png

`o)`

.. figure:: _figures/httplb_14.png

`p)`

.. figure:: _figures/httplb_15.png

`q)`

.. figure:: _figures/httplb_16.png

`r)`

.. figure:: _figures/httplb_17.png

`s)`

.. figure:: _figures/httplb_18.png

`t)`

.. figure:: _figures/httplb_19.png

`u)`

.. figure:: _figures/httplb_20.png

`v)`

.. figure:: _figures/httplb_21.png

4. Check connection from web
****************************

.. figure:: _figures/httplb_22.png
