 # CLDV6212-Final-POE

Just like in PROG 2B you will need to make an ASP.Net Core Web API with Swagger for API documentation. However, for cloud you will be required to create endpoints for user registration and login enhanced with encryption and JWT authentication and RBAC. Additionally you will need to deploy your service to a Linux VPS Host. I have chosen Azure VM to demo deployment however we encourage all groups to explore multiple platforms like AWS, Oracle, Google Cloud etc. N.B. Stay away from Render as you will not be able to deploy more than one service on the free tier. Lastly we have a CI/CD pipeline that is optional according to the POE...

It is not optional at all. We will explore how to get those 10 free marks in this readme. A cool thing about having the pipeline running your Postman tests, unit tests etc. is that you can talk about it in your video showing all tests passing on all three platforms. That nice green tick will tell your marker that everything runs perfectly. From there you can talk about the jobs in the yaml file, show the postman collection.json in the docs folder etc. We will discuss the best possible video format later on in the document.

## Building a secure login with an ASP.Net Core Web API

### NuGet packages
Remember that you guys are still using the Azurite Emulator for Part 3 so you will have user info stored in an Azurite Table so you need to get Azure.Data.Tables again. You also need Swagger for API documentation so you of course need Swashbuckle.AspNetCore. For the JWT authentication you will need to get Microsoft.AspNetCore.Authentication.JwtBearer. Lastly, for the encryption you can get BCrypt (that is what I'm using), Argon2ID and PBKDF2. Here is the NuGet package: BCrypt.Net-Next.

### Modelling data
You need to model the User data in an entity with the ITableEntity interface implemented.

### JWT
You guys will actually have sensitive data in your appsettings.json. These will be JWT Settings including the secret which is something like this. It must be used to authenticate every session:

````
  "JwtSettings": {
    "Secret": "Cldv6212sUPERLongSecretForP0EPart3@cloudisCo0lSha256!2026",
    "Issuer": "CoffeeNChillAPI",
    "Audience": "CoffeeNChillClients"
  }
````

Here's some code. You need to pull all secrets from your appsettings.json. Remember your ClaimTypes:
````
  var jwtSettings = _configuration.GetSection("JwtSettings");
  var secretKey = Encoding.UTF8.GetBytes(jwtSettings["Secret"]!);
````

````
var tokenDescriptor = new SecurityTokenDescriptor
{
  Subject = new ClaimsIdentity(claims),
  Expires = DateTime.UtcNow.AddHours(2),
  Issuer = jwtSettings["Issuer"],
  Audience = jwtSettings["Audience"],
  SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(secretKey), SecurityAlgorithms.HmacSha256Signature)
};
````

### Login and register
Remember that you are working with controllers for this part. To maintain clean separation of storage concerns have one controller for login and another for registration. Have another controller for the actual endpoints with RBAC. Every time a user logs in they must have an authenticated :
````
    [HttpGet("staff/dashboard")]
    [Authorize(Roles = "Admin,Staff")] <---
````

**Note**: You need to have global exception handling for each endpoint. You must validate **EVERYTHING** as per the rubric. You need to have custom exception handling meaning you create your own helper methods to act as middleware.

### Deploying to Azure VM
Whoever has the most Azure credits can handle this part. Just login and create a resource group if you do not have one already then create one and follow the steps. VM not VM Scale Set:

<img width="1863" height="885" alt="image" src="https://github.com/user-attachments/assets/9cb4468e-e291-4c8a-b221-d9744d532e2b" />

<img width="1862" height="882" alt="image" src="https://github.com/user-attachments/assets/29f50e48-4978-4e58-99f1-f406906fd729" />

Go "Create" a new VM and follow the instructions under Basic until you hit these settings:
<img width="1280" height="508" alt="image" src="https://github.com/user-attachments/assets/2a2a0e9f-418c-486d-a08d-afd46590b441" />

- Check "Run with Azure Spot Discount"
- Go "See all sizes"
- Go for the cheapest option. With the Azure Spot Discount it should be significantly reduced to under 0.1 dollars. My price is greyed out because I already have one VM running:
<img width="1865" height="885" alt="image" src="https://github.com/user-attachments/assets/b04f508d-bd94-414d-88ca-4717afd4e506" />

<img width="560" height="31" alt="image" src="https://github.com/user-attachments/assets/b987773c-d15a-42e3-9785-b1725a4477c7" />


<img width="1863" height="831" alt="image" src="https://github.com/user-attachments/assets/96797290-c1e1-4cf4-b9a5-7b46bf1f01b8" />

- Enable SSH public key and generate new key pair

- Change the selected inbound ports:
<img width="1865" height="881" alt="image" src="https://github.com/user-attachments/assets/2fe61f2a-9ac8-47c3-b4dd-780c79e1e57a" />

- Go to "Disks" next and go to OS disk type and select Standard SSD under Locally Redundant Storage to keep cost down:
<img width="1863" height="975" alt="image" src="https://github.com/user-attachments/assets/0bc08f49-8364-4d66-9206-310bf6fd5e25" />

Next:

<img width="940" height="447" alt="image" src="https://github.com/user-attachments/assets/0643d804-02dd-44b2-a936-3c2f92f2b3a5" />

<img width="940" height="446" alt="image" src="https://github.com/user-attachments/assets/6ed273db-8a5e-430f-8d35-13b8a5b15bb7" />

Make sure you have VM access:
<img width="1865" height="886" alt="image" src="https://github.com/user-attachments/assets/37111db0-06c9-4042-84d6-af365b4f406e" />


If not:

You will need to add an inbound security rule to connect SSH into your VM:

<img width="913" height="1034" alt="image" src="https://github.com/user-attachments/assets/3a7ba8df-4afb-4bee-a121-3985ab6e66c1" />

#### SSH:
<img width="1865" height="881" alt="image" src="https://github.com/user-attachments/assets/3cb29265-78e7-4584-a807-5e703dc9ae17" />

You would have downloaded a .pem file containing your SSH Key Pairs. Go find the files path and come paste it here in Azure. Go edit settings first:
<img width="1863" height="887" alt="image" src="https://github.com/user-attachments/assets/34fb637c-7677-4152-a23b-2771fdcbc72f" />

Paste the command into a command prompt and SSH:
<img width="1486" height="758" alt="image" src="https://github.com/user-attachments/assets/7caf8e7b-01c4-42de-8528-6b7653a2929b" />

You should come to something like this:
<img width="1480" height="550" alt="image" src="https://github.com/user-attachments/assets/a5c2e043-2ec5-45b7-b537-4c9e7ef8ee11" />

**PLEASE NOTE THAT THIS IS JUST A GUIDE TO CREATE THE RESOURCE. YOU STILL NEED TO DEPLOY YOUR SERVICES TO THE VM**

## Basic advice and requirements:

Based on the transcript, here is a structured breakdown of the POE (Portfolio of Evidence) requirements and guidelines discussed by Reece Wanvig for the assessment.

### 1. Video Presentation & Demonstration
*   **Professional Video:** Create a professional, engaging video presentation.
*   **Live VPS Deployment:** Demonstrate the application running live on a VPS (Virtual Private Server).
*   **System Interaction:** Show the data/stuff moving between different systems and visually demonstrate that multiple containers were created on the system.
*   **Design Choices:** Provide detailed explanations of your technical design choices.
*   **No "Hunting":** You must explicitly point out and demonstrate where all features are. Do not make the lecturer search for your marks. 

### 2. API Testing & Authentication
*   **Swagger UI JWT Authentication Walkthrough:** You must show a walkthrough of the authentication flow in Swagger.
*   **Public vs. Protected Endpoints:** Demonstrate authorization by showing the difference between public and protected endpoints.
*   **Postman Suite Execution:** Execute and show a complete Postman suite. 
*   **Flexibility (Swagger vs. Postman):** You can demonstrate entirely in Swagger, entirely in Postman, or a mix of both. *However, you **must** include some Swagger demonstration to prove Swagger is working and the authorization is set up.* (Showing Postman in the pipeline instead of the actual app is also acceptable).

### 3. Architecture & Diagrams
*   **Technical Diagram:** Provide a diagram of your architecture.
*   **Hardware Inclusion:** The hardware must be included in the diagram (e.g., if you are using a Raspberry Pi or AWS, it must be shown).

### 4. CI/CD Pipeline & Deployment (Bonus Marks)
To get the bonus marks, your pipeline must be fully automated:
*   **GitHub Actions:** Configure a GitHub Actions workflow.
*   **Automated Docker Build:** The pipeline must automatically build the Docker image.
*   **Docker Hub Push:** Automatically push the versioned image to Docker Hub.
*   **Automated Deployment:** The pipeline must automatically SSH into the live VPS host and execute `docker-compose pull && docker-compose up -d` on commit.

### 💡 Lecturer's Advice for the Group
*   **Start early.** Since this is group work, finish it early so you don't have to fight with group members at the last minute. 
*   **Make it obvious.** If you built it, show it. Don't assume the marker knows where to look.

## Deployment
Keep in mind that you guys are using docker and docker hub

# Deployment Architecture & CI/CD Overview

This project uses an automated CI/CD pipeline to containerize and deploy our C# HTTP trigger Azure Function onto an Azure Virtual Machine via Docker Hub.

## High-Level Workflow

1. **Code Push:** Changes pushed to the `main` branch trigger a GitHub Actions workflow.
2. **Build & Push:** GitHub Actions builds the Docker image and pushes the latest version to Docker Hub.
3. **Automated Deployment:** The workflow securely connects to the Azure VM via SSH, pulls the updated container image, and restarts the service.

## Required Repository Secrets

To secure the pipeline, the following secrets must be configured in GitHub (**Settings > Secrets and variables > Actions**):
- `DOCKER_USERNAME` & `DOCKER_PASSWORD` (Docker Hub credentials)
- `VM_HOST` (Azure VM Public IP)
- `VM_USERNAME` (VM SSH user)
- `VM_SSH_KEY` (Private SSH key content)

**AS MUCH AS I WANT TOO I CANNOT GIVE AWAY TOO MUCH BECUASE IT'S SUPPOSED TO BE ADDITIONAL RESEARCH. IT'S NOT TOO DIFFICULT I PROMISE**

## Checklist based on the rubric and some additional expectations

1. Once again the pipeline is a requirement to ensure you guys have a bit of a safety net. Based on the **rubric you need to do the following**: Complete automated CI/CD pipeline building, pushing versioned Docker Hub images, SSHing into the live VPS host, and executing `docker-compose pull && docker-compose up -d` automatically on commit. The next sentences explain how to enhance it. The pipeline should build your API and/or serverless functions docker image and create a test network (you can do pipelines for both individually as in one for the API and one for the serverless functions from parts 1 and 2 and of course your table storage from part 3), do a healthcheck for your services and execute a combination of unit tests as well as run your postman collection tests with newman. You must have logging in the pipeline and of course clean up docker resources when you're done. Lastly if all tests pass and there are changes, said changes should be deployed to your hosted service on whichever platform it may be. The benefit of doing this is you prove to your marker that all tests work right out of the gate with the green tick.
2. This is an end to end system meaning parts 1 to 3 must work together. Only admins and staff should be able to interact with staff documents, add menu items, change an orders status etc. If it has to do with making an order it can be AllowAnonymous as customers/students do not need to register. Only an admin should be able to register a staff member.
3. When coding up the solution please use a **DTO ARCHITECTURE**.
4. When making the video preferably keep it between 15 and 20 minutes long. In the video you must touch on each point in the rubric to be awarded high marks.
5. This comes directly from the rubric: Engaging, professional video demonstrating the live VPS deployment, demonstrating JWT auth flows in Swagger UI, showing public vs protected endpoint authorization, and detailing technical design choices.
6. For your video you should start with your deployment. Show the platform, the URL etc. If you have a pipeline come to GitHub and talk about your pipeline ensuring you speak about how it shows all tests pass from your postman collection, updates docker hub and the deployed instance (don't spend too long here). Next show the deployed instance working on postman by running your collection using the hosts IP. Next have a section to do it all using swagger. Just demo the part 3 requirements on swagger. Have a section where you talk about JWT, preferably before demoing part 3 feature requirements in action. Mention your encryption algorithm for your passwords. Show RBAC in the video as well by showing a user not having access to an endpoint.
7. The readme: follow the requirements of the rubric, avoid huge walls of text as we are looking for good formatting, explanations etc. Explain part 3 by documenting JWT and RBAC as well as encryption in some detail. Document your deployment process and speak on what platform you chose and why. **Provide the URL**. Document your pipeline, tests and CI/CD pipeline.
8. We want a changelog and clear headings showing part 3 additions, a youtube link, a live deployment URL and a table of contents.

BEST OF LUCK!!

