# Complete CI/CD with GitHub Actions for React application

# Note: For this project, we will use the "Ubuntu" machine. Commands may be different if you are using a different OS.

# Step 1 : Let's create a simple React application

```
npx create-react-app my-app
cd my-app
npm start
```
Then open http://localhost:3000/ to see your app.

# Step 2 : Clone the source code

If you are using Ubuntu Machine, Git will be pre-installed. Clone the repository by using 'git clone' command and move into there.

We shall only work in the same directory for the duration of this project. The reason you will understand at last stage.

```
git clone https://github.com/Pradyumna018/Todo-List-App-Deployment-Using-GitHub-Action-CICD.git

```
# Step 3 : Create a Multi-Stage Dockerfile for our application

Now, let's understand the requirement first. We require node to be installed and running in the background in order to run the react application.. Also, we will need nginx to serve the requests which will help us to access the application after deploying in EB.

## Write a Dockerfile with the following code:

```
FROM node:14-alpine as builder
WORKDIR /app 
COPY package.json . 
RUN npm install 
COPY . . 
RUN npm run build

FROM nginx 
EXPOSE 80 
COPY --from=builder /app/build /usr/share/nginx/html

```

## Explanation :

This Dockerfile has two stages. The first stage builds a web application with NPM using the official Node.js 14 Alpine image. The second stage serves the constructed web application on port 80 using the official NGINX image. The built application files from the first stage are copied to the second stage, which is the final image, that can be used to run the web application in a Docker container.

# Step 4 : Create a docker-compose.yml file

## Write a docker-compose.yml with the following code:

```
version: '3'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - '80:80'
```

# Step 5 : Create .github/workflows directory
## And inside this directory create one file which is deploy.yml

## Write deploy.yml with the following code:

```
name: Deploy React to AWS
on:
  push:
    branches:
      - "main"

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout source code
        uses: actions/checkout@v2

      - name: Generate deployment package
        run: zip -r deploy.zip . -x '*.git*'

      - name: Deploy to EB
        uses: einaregilsson/beanstalk-deploy@v21
        with:
          aws_access_key: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws_secret_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          application_name: react-app1
          environment_name: React-app-env-1
          version_label: ${{ github.sha }}
          existing_bucket_name: elasticbeanstalk-us-east-1-691097374572
          region: us-east-1
          deployment_package: deploy.zip
```

# Step 6 : Configure Elastic BeanStalk

* Go to the AWS Elastic BeanStalk Service.
* Click on "Create Application"
* Provide the details such as Name = "<Any name>", Platform = "Docker"
* Add Service role
* Select default VPC
* Now Click on "Create Application" 

# Step 7 : Now Push all your code to Github Repo.

# Conclusion:

The project setup described to automate the entire workflow, from the development phase through to deployment. This setup encourages collaboration among team members and ensures consistency throughout the development and deployment processes. It is designed to facilitate a smooth and efficient workflow.


# OUTPUT

![Screenshot 2023-11-29 180928](https://github.com/Pradyumna018/Todo-List-App-Deployment-Using-GitHub-Action-CICD/assets/136186419/2fc26ccd-dd3c-4449-a804-47244cec4df9)

# Thank You ❤️




