# steps to create cluster
gcloud auth login  
gcloud container clusters create test --zone europe-west2-c  
kubectl create -f <file/folder>  
^^ file/folder must be.contain .yml

# connect service to nginx
kubectl exec -it <pod name> bash  
^^ pod name retrieved through 'kubectl get pods' command  
will have to change /etc/nginx/nginx.conf to (see steps below first):  
events {}

http {

        server {
        
                listen 80;
                
                location / {
                
                        proxy_pass http://jenkins:8080/;

                }

        } 
}  

nginx -s reload

*may need to download vim to edit:  
apt update
sudo apt install -y vim

*may need to create disk (check disks in gcp, check size is 200GB min, skip if one present):  
gcloud compute disks create --size=200GB --zone=europe-west2-c jenkins-jobs

# change group of jobs in jenkins
^^ from root to jenkins  
cd /var/jenkins_home >> should be a 'jobs' directory  
sudo chown jenkins:jenkins jobs  

ensure latest pod after group change:  
kubectl delete -f jenkins  
kubectl create -f jenkins  

# run service via nginx
kubectl get services  
^^ access  nginx external-ip (no need to define port)  

quick check:  
run job, close service (jenkins in this case), open service  
job should still be present if successful
