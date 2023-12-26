# Class Activity Multiple pods
1. Clone the this [CMS Repo](1.[https://github.com/nafisatuli/CMS-system.git](https://github.com/nafisatuli/CMS-system.git)) to your local
2. Update the database connection config to environment Variable separate env vars for the database host as `DATABASE_HOST` and `DATABASE_NAME`
3. Create Docker file for the application
4. Build the docker image and push it to your docker hub registry and note the image name and tag
5. Create Deployment, and service manifest with PV and PVC for MongoDB
6. Deploy the MongoDB database 
7. Create deployment manifest for the CMS application with PV and PVC for the path `/app/public/uploads` folder to host uploaded images for the posts
8. Expose Environment variables on the deployment manifest with MongoDB service name
9. Deploy the application and connect with port forwarding option.
