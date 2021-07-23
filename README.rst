update kubeconfig for kubectl
aws eks update-kubeconfig --region eu-west-2 --name education-eks-DPP2hvMh


Pre-Requisites
###############

kubectl
AWS Account
aws cli
terraform



volterra account


Lab Environment Setup  
############################### 

1. AWS CLI
*************************** 
Open IAM => Users

Create new user

Get token and secret

-- aws configure

Create IAM policy

-- link to minimal IAM policy

deploy kubectl to AWS

cd terraform
terraform init
terraform plan
terraform apply

.. figure:: _figures/eks_setup_1.png

.. figure:: _figures/eks_setup_2.png

.. figure:: _figures/eks_setup_3.png

.. figure:: _figures/eks_setup_4.png

Connect EKS Cluster
##################### 

1. Create token

.. figure:: _figures/connect_eks_cluster_1.png

.. figure:: _figures/connect_eks_cluster_2.png

.. figure:: _figures/connect_eks_cluster_3.png

.. figure:: _figures/connect_eks_cluster_4.png

2. update manifest

.. figure:: _figures/connect_eks_cluster_5.png

3. deploy manifest

.. figure:: _figures/connect_eks_cluster_6.png

4. accept registration

.. figure:: _figures/connect_eks_cluster_7.png

.. figure:: _figures/connect_eks_cluster_8.png

.. figure:: _figures/connect_eks_cluster_9.png

5. check status

.. figure:: _figures/connect_eks_cluster_10.png

Create VK8S Cluster
##################### 

1. Create cluster

.. figure:: _figures/create_vk8s_1.png

.. figure:: _figures/create_vk8s_2.png

.. figure:: _figures/create_vk8s_3.png

.. figure:: _figures/create_vk8s_4.png

2. download kubeconfig

.. figure:: _figures/create_vk8s_5.png

.. figure:: _figures/create_vk8s_6.png

.. figure:: _figures/create_vk8s_7.png

.. figure:: _figures/create_vk8s_8.png

.. figure:: _figures/create_vk8s_9.png

3. check connection

kubectl --kubeconfig ./ves_default_vk8s.yaml cluster-info

.. figure:: _figures/create_vk8s_10.png

Deploy resources to Volterra Edge
##################### 

1. deploy resources

-- TBD

2. Create internal load balancer

.. figure:: _figures/backend_lb_1.png

.. figure:: _figures/backend_lb_2.png

.. figure:: _figures/backend_lb_3.png

.. figure:: _figures/backend_lb_4.png

.. figure:: _figures/backend_lb_5.png

.. figure:: _figures/backend_lb_6.png

.. figure:: _figures/backend_lb_7.png

.. figure:: _figures/backend_lb_8.png

.. figure:: _figures/backend_lb_9.png

3. create public load balancer

.. figure:: _figures/frontend_lb_1.png

.. figure:: _figures/frontend_lb_2.png

.. figure:: _figures/frontend_lb_3.png

.. figure:: _figures/frontend_lb_4.png

.. figure:: _figures/frontend_lb_5.png

.. figure:: _figures/frontend_lb_6.png

.. figure:: _figures/frontend_lb_7.png

.. figure:: _figures/frontend_lb_8.png

4. check connection from web

.. figure:: _figures/frontend_lb_9.png